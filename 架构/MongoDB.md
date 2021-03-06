# 1. MongoDB
## 1.1. 定义
MongoDB采用Bson格式存储，BSON格式比JSON数据类型更加丰富，支持ObjectId、日期类型、正则表达式、JS代码、二进制数据等.
## 1.2. 特点
* MongoDB 不支持事务
* MongoDB 不支持多表连接查询
* MongoDB 中的键值对是有序的，相同的键值对，不同的顺序，属于不同的文档
* new Date(); 返回日期对象，属于日期类型，Date()函数返回日期字符串，在Shell中操作日期要使用日期类型， 日期类型是包含时区的
* _id的值可以是任意类型，默认是ObjectId类型，可以在分片环境中生成唯一的标识符（时间戳（单位：秒）+主机的唯一标示（主机名的hash值）+进程标识符PID+自动增加的计数器）， 通过时间戳可以大概知道ObjectId大概是按时间先后排序的，主机的唯一标示可以用于保证在多台服务器器上不重复，进程ID为了保证同台服务器上多个不同的进程之间生成不重复 值，最后一部分是一个自增的计数器， 前面三部分是为了保证同一秒在同一台机器上的同一个进程上生成一个唯一的值，最后一部分用于保证在同一秒、同一台机器、同一个进程 在同一秒内（注意是同一秒内）生成唯一值
* mongodb中有一些特殊的键，它们被称为修改器、操作符等
## 1.3. 与关系型数据库对比
* MongoDB ———-> SQL
* database数据库 —->database数据库
* collection集合——->table表
* document文档—-> row 行
* filed字段——-> column列
* index索引—–> index索引
* 不支持多表连接查询—> 支持多表连接查询