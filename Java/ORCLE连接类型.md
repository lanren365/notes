# ORCLE连接类型
使用Java程序连接Oracle数据库有三种方式：Servcie、SID、TNS。
## Servcie
```md
jdbc:oracle:thin:@//<host>:<port>/<service_name>
```
## SID
```md
jdbc:oracle:thin:@<host>:<port>:<SID> 
```
## TNS
```md
jdbc:oracle:thin:@<TNSName> 
```
其中：Service用于一般集群环境链接数据库。SID用于连接某一个单独的数据库实例。TNS用户已经配置好的Oracle不需要书写详细的服务器信息。