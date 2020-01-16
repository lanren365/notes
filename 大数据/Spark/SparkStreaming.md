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