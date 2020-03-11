# 1. SparkStreaming
## 1.1. 算子操作
### 1.1.1. transformation类算子
```java
package com.spark.sparkstreaming;

import java.util.ArrayList;
import java.util.List;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.PairFunction;
import org.apache.spark.api.java.function.VoidFunction;
import org.apache.spark.streaming.Durations;
import org.apache.spark.streaming.api.java.JavaDStream;
import org.apache.spark.streaming.api.java.JavaPairDStream;
import org.apache.spark.streaming.api.java.JavaReceiverInputDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;

import com.google.common.base.Optional;

import scala.Tuple2;
/**
 * 过滤黑名单
 * transform操作
 * DStream可以通过transform做RDD到RDD的任意操作。
 * @author root
 *
 */
public class TransformOperator {
    public static void main(String[] args) {
        SparkConf conf = new SparkConf();
        conf.setMaster("local[2]").setAppName("transform");
        JavaStreamingContext jsc = new JavaStreamingContext(conf,Durations.seconds(5));
        
        //模拟黑名单
        List<Tuple2<String,Boolean>> blackList = new ArrayList<Tuple2<String,Boolean>>();
        blackList.add(new Tuple2<String,Boolean>("zhangsan",true));
        //将黑名单转换成RDD
        final JavaPairRDD<String, Boolean> blackNameRDD = jsc.sparkContext().parallelizePairs(blackList);
        
        //接受socket数据源
        JavaReceiverInputDStream<String> nameList = jsc.socketTextStream("node5", 9999);
        JavaPairDStream<String, String> pairNameList = 
                nameList.mapToPair(new PairFunction<String, String, String>() {

            /**
             *这块代码在Driver端执行。
             */
            private static final long serialVersionUID = 1L;

            @Override
            public Tuple2<String, String> call(String s) throws Exception {
                return new Tuple2<String, String>(s.split(" ")[1], s);
            }
        });
        JavaDStream<String> transFormResult =
                pairNameList.transform(new Function<JavaPairRDD<String,String>, JavaRDD<String>>() {

            /**
             * 
             */
            private static final long serialVersionUID = 1L;

            @Override
            public JavaRDD<String> call(JavaPairRDD<String, String> nameRDD)
                    throws Exception {
                /**
                 * nameRDD:
                 *   ("zhangsan","1 zhangsan")
                 *   ("lisi","2 lisi")
                 *   ("wangwu","3 wangwu")
                 * blackNameRDD:
                 *   ("zhangsan",true)
                 *   
                 * ("zhangsan",("1 zhangsan",[true]))
                 * 
                 */
                JavaPairRDD<String, Tuple2<String, Optional<Boolean>>> leftOuterJoin = 
                        nameRDD.leftOuterJoin(blackNameRDD);
                //打印下leftOuterJoin
                /*leftOuterJoin.foreach(new VoidFunction<Tuple2<String,Tuple2<String,Optional<Boolean>>>>() {
                    
                    *//**
                     * 
                     *//*
                    private static final long serialVersionUID = 1L;

                    @Override
                    public void call(Tuple2<String, Tuple2<String, Optional<Boolean>>> t)
                            throws Exception {
                        System.out.println(t);
                    }
                });*/
                
                
                //过滤：true的留下，false的过滤
                //("zhangsan",("1 zhangsan",[true]))
                JavaPairRDD<String, Tuple2<String, Optional<Boolean>>> filter = 
                        leftOuterJoin.filter(new Function<Tuple2<String,Tuple2<String,Optional<Boolean>>>, Boolean>() {

                    /**
                     * 
                     */
                    private static final long serialVersionUID = 1L;

                    @Override
                    public Boolean call(Tuple2<String, Tuple2<String, Optional<Boolean>>> tuple)throws Exception {
                        if(tuple._2._2.isPresent()){
                            return !tuple._2._2.get();
                        }
                        return true;
                    }
                });
                
                JavaRDD<String> resultJavaRDD = filter.map(new Function<Tuple2<String,Tuple2<String,Optional<Boolean>>>, String>() {

                    /**
                     * 
                     */
                    private static final long serialVersionUID = 1L;

                    @Override
                    public String call(
                            Tuple2<String, Tuple2<String, Optional<Boolean>>> tuple)
                            throws Exception {
                        
                        return tuple._2._1;
                    }
                });
                
                //返回过滤好的结果
                return resultJavaRDD;
            }
        });
        
        transFormResult.print();
        
        jsc.start();
        jsc.awaitTermination();
        jsc.stop();
    }
}
```
## 1.2. foreachRDD的正确使用姿势
### 1.2.1. 误区
1. 在driver上创建连接对象（比如网络连接或数据库连接）
如果在driver上创建连接对象，然后在RDD的算子函数内使用连接对象，那么就意味着<font color = red>**需要将连接对象序列化后从driver传递到worker上。而连接对象（比如Connection对象）通常来说是不支持序列化的，此时通常会报序列化的异常（serialization errors）。因此连接对象必须在worker上创建，不要在driver上创建。**</font>
```scala
dstream.foreachRDD { rdd =>
　　val connection = createNewConnection() // 数据库连接在driver上执行
　　rdd.foreach { record =>
　　connection.send(record) // 在worker上执行
　　}
}
```

2. 为每一条记录都创建一个连接对象
通常来说，连接对象的创建和销毁都是很消耗时间的。因此频繁地创建和销毁连接对象，可能会导致降低spark作业的整体性能和吞吐量。
```scala
dstream.foreachRDD { rdd =>
rdd.foreach { record =>
    val connection = createNewConnection() //每插入一条数据，创建一个连接
    connection.send(record)
    connection.close()
    }
}
```
　　比较正确的做法是：对DStream中的RDD，调用foreachPartition，对RDD中每个分区创建一个连接对象，使用一个连接对象将一个分区内的数据都写入数据库中。这样可以大大减少创建的连接对象的数量。
### 1.2.2. 正确
1. 为每个RDD分区创建一个连接对象
```scala
dstream.foreachRDD { rdd =>
rdd.foreachPartition { partitionOfRecords =>
        val connection = createNewConnection()
        partitionOfRecords.foreach(record => connection.send(record))
        connection.close()
    }
}
```
2. 为每个RDD分区使用一个连接池中的连接对象
```scala
dstream.foreachRDD { rdd =>
rdd.foreachPartition { partitionOfRecords =>
        // 从数据库连接池中获取连接
        val connection = ConnectionPool.getConnection()
        partitionOfRecords.foreach(record => connection.send(record))
        ConnectionPool.returnConnection(connection) // 用完以后将连接返    回给连接池，进行复用
    }
}
```
### 1.2.3. Kafka的例子
#### 1.2.3.1. 异常例子

```scala
nameAddrPhoneStream.foreachRDD(rdd => {
  //在Driver中执行
 
  //初始化生产者配置
  val props = new Properties()
  props.setProperty("bootstrap.servers", "master:9092")
  props.setProperty("client.id", "kafkaGenerator")
  props.setProperty("key.serializer", classOf[StringSerializer].getName)
  props.setProperty("value.serializer", classOf[StringSerializer].getName)
  //创建生产者
  val producer = new KafkaProducer[K, V](props)
  
  rdd.foreach(record => {
    //在RDD中的各条记录的本地计算结点Worker中执行
    
    //生产者向Kafka发送消息
    producer.send(new ProducerRecord[K, V]("kafkaProducer", record))
  })
})
```
执行结果
> 异常信息：KafkaProducer 未能序列化：
> Caused by: java.io.NotSerializableException: org.apache.kafka.clients.producer.KafkaProducer
> 
> 原因是：
> 1. ds.foreachRDD(func)操作的函数在Driver中运行；
> 2. rdd.foreach(func)操作的函数在Worker中运行；
> 3. producer对象需要从Driver序列化传送到Worker中，而producer并不能序列化。

#### 1.2.3.2. 正常的例子
开销大的实现方式
```scala
nameAddrPhoneStream.foreachRDD(rdd => {
  
  rdd.foreach(record => {
 
    //初始化生产者配置
    val props = new Properties()
    props.setProperty("bootstrap.servers", "master:9092")
    props.setProperty("client.id", "kafkaGenerator")
    props.setProperty("key.serializer", classOf[StringSerializer].getName)
    props.setProperty("value.serializer", classOf[StringSerializer].getName)
    //创建生产者
    val producer = new KafkaProducer[K, V](props)
 
    //生产者向Kafka发送消息
    producer.send(new ProducerRecord[K, V]("kafkaProducer", record))
  })
})
```