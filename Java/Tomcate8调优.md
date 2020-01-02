# 1. Tomcate8调优
  Tomcat优化大概分为8种优化分别为：配置文件优化、启动脚本优化、参数优化。
## 1.1. 启动脚本优化(catalina.sh)
```sh
JAVA_OPTS="-Djava.awt.headless=true -Dfile.encoding=UTF-8
-server -XX:MinHeapFreeRatio=80 -XX:MaxHeapFreeRatio=80 -XX:ThreadStackSize=512
-XX:NewSize=128m
-XX:MaxPermSize=64m
-XX:NewRatio=4 -XX:SurvivorRatio=8
-XX:+AggressiveOpts -XX:+UseBiasedLocking
-XX:+UseConcMarkSweepGC -XX:+ScavengeBeforeFullGC
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -Xloggc:../logs/performance_gc.log
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=../logs/performance_heapdump.hprof  
-XX:+UnlockCommercialFeatures -XX:+FlightRecorder -XX:FlightRecorderOptions=defaultrecording=true"
```
参数说明，可参考jvm的配置：
- -server：服务器模式，该参数放置在配置项的首位置
- -Xms：堆的初始大小，单位MB 配置-Xms与-Xmx一致，为可用内存的80%
- -XmX：堆的最大大小，单位MB
- -Xmn：新生代的初始大小，单位MB 为堆大小的3/8
- -XX:+UnlockCommercialFeatures -XX:+FlightRecorder -XX:FlightRecorderOptions=defaultrecording=true:飞行记录在使用jmc工具时，如需要打开飞行记录，需要配置以下3个参数，不用时可以先不配置
## 1.2. 配置文件优化(server.xml)
1. **更改IO模式**  
    将bio协议模式更改为nio
```
 bio模式协议：HTTP1.1
nio模式协议：org.apache.cotyote.http11.Http11NioProtocol
nio2模式协议：org.apache.cotyote.http11.Http11Nio2Protocol
apr模式协议：org.apache.coyote.http11.Http11AprProtocol
```
_**说明：** `bio协议`模式，适用于简单流程；`nio协议`模式，适用于后台耗时的请求的操作；`ARP`模式：tomcat以jni方式调用apache http服务器的核心动态链接库来处理文件或网络传输操作。_  
<!-- Prevent memory leaks due to use of particular java/javax APIs 防止jre内存泄漏-->
`<Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />`  
配置文件实例: 
```xml
<Connector 
port="8080" 
protocol="org.apache.coyote.http11.Http11AprProtocol" --协议类型 
disableUploadTimeout="true"

keepAliveTimeout="20000"

connectionTimeout="20000" --已接受，但未被处理的请求的等待超时时间 ms
redirectPort="8443" --安全通信的转发端口 
URIEncoding="UTF-8"--URL编码字符集
minSpareThreads="100" --默认初始化和保持空闲的线程数
enableLookups="false"--关闭DNS反向查询
useURIValidationHack="false" --关闭不必要的检查
maxThreads="1000" --处理请求线程的最大数目 未配置为200 此属性会被忽略
acceptCount="1000" --所用可能的线程都在使用时传入连接请求的最大长度
disableUploadTimeout="true" --设置允许更长的超时连接
maxConnections="1000"--接受和处理的最大连接数(nio/nio2 1000，apr 8192)
maxHttpHeaderSize="8192"--请求和响应http头的最大大小 8k
tcpNoDelay="true" --tcp不延迟
compression="on"--是否启用压缩 on off force 
compressionMinSize="2048" --压缩前数据最小值 2k byte
noCompressionUserAgents="gozilla,traviata" --设置哪些浏览器不压缩
compressableMimeType="text/html,text/xml,text/css,application/javascript,text/plain" --设置压缩的文件类型
/>
```

2. **调整连接器模式**  
`Executor`方面 可在tomcat组件之间共享，减少创建销毁线程的消耗，提高线程的使用效率  
配置实例：
```xml
<!--连接池设置-->
<Executor 
name="tomcatThreadPool" --线程池名
namePrefix="catalina-exec-" --线程名称前缀 namePrefix+threaNumber
maxThreads="1000" --池中最大线程数
minSpareThreads="100" --活跃线程数 会一直存在
maxIdleTime="60000" --线程空闲时间，超过该时间，线程会被销毁 ms
maxQueueSize="Integer.MAX_VALUE" --被执行前线程的排队数目
prestartminSpareThreads="false" --启动线程池时，是否启用minSpareThreads部分线程
threadPriority="5" --线程池中线程优先级 1~10
className="org.apache.catalina.core.StandardThreadExecutor" --线程实现类 自定义线程需时间 org.apache.catalina.Executor类
/>
<!--当配置了连接池时，需要配置该连接器-->
<Connector 
executor="tomcatThreadPool" --线程池名
port="8080" 
protocol="org.apache.coyote.http11.Http11AprProtocol"
connectionTimeout="20000"
redirectPort="8443" />
```

## 1.3. 配置项目中的超时时间(web.xml)
配置项：`<session-timeout>30</session-timeout>` 单位为分钟。

