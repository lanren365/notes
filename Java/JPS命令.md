# JPS命令
## 定义
&emsp;&emsp;`jps`是`Java Virtual Machine Process Status Tool`的简称，是java提供的一个显示当前所有java进程pid的命令，适合在linux/unix平台上简单察看当前java进程的一些简单情况。`jps`命令 与PS命令类似，它的作用是显示当前系统的java进程情况及进程id。
## 使用方法
&emsp;`jps`的通用命令格式为：`jps [-X]`的形式。具体的`-X`描述如下：
&emsp;&emsp;1. `-q`只显示pid，不显示class名称,jar文件名和传递给main方法的参数。
> $ jps -q
>28715
>23789

&emsp;&emsp;2. `-m`输出传递给main方法的参数，在嵌入式jvm上可能是null。
> $ jps -m
>23789 BossMain

&emsp;&emsp;3. `-l`输出应用程序main class的完整package名或者应用程序的jar文件完整路径名。
> $ jps -l
> 1760 /home/appuser/flycua/assembly/bin/../lib/assembly.jar
> 104833 ./../lib/refundprcocessor.jar
> 27270 org.apache.catalina.startup.Bootstrap
> 3175 org.apache.catalina.startup.Bootstrap
> 79721 ./../lib/integration.jar
> 112107 sun.tools.jps.Jps
> 2285 org.apache.catalina.startup.Bootstrap
> 71084 ./../lib/cuss.jar
> 2190 org.apache.catalina.startup.Bootstrap
> 126703 org.apache.catalina.startup.Bootstrap
> 102075 org.apache.catalina.startup.Bootstrap
> 70748 org.apache.catalina.startup.Bootstrap
> 33021 ./../lib/sales.jar
> 5214 org.apache.catalina.startup.Bootstrap
> 46687 org.apache.catalina.startup.Bootstrap

&emsp;&emsp;4. `-v`输出传递给JVM的参数。
> $ jps -v
> 1760 assembly.jar -DLOGPATH=/home/appuser/flycua/assembly/bin/../logs/ -Xmx1024M -Xms1024M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+DisableExplicitGC -Xnoclassgc
> 104833 refundprcocessor.jar -DLOGPATH=./../logs/ -Xmx384M -Xms384M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+DisableExplicitGC -Xnoclassgc
> 27270 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/booking/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx3000M -Xms3000M -XX:SurvivorRatio=8 -XX:MaxMetaspaceSize=512M -Xss512k -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/booking -Dcatalina.home=/home/appuser/flycua/booking -Djava.io.tmpdir=/home/appuser/flycua/booking/temp
> 3175 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/paymentclient/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx2560M -Xms2560M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/paymentclient -Dcatalina.home=/home/appuser/flycua/paymentclient -Djava.io.tmpdir=/home/appuser/flycua/paymentclient/temp
> 79721 integration.jar -DLOGPATH=./../logs/ -Xmx256M -Xms256M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+DisableExplicitGC -Xnoclassgc
> 2285 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/pip_data/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx2048M -Xms2048M -XX:SurvivorRatio=8 -XX:MaxMetaspaceSize=512M -Xss512k -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/pip_data -Dcatalina.home=/home/appuser/flycua/pip_data -Djava.io.tmpdir=/home/appuser/flycua/pip_data/temp
> 71084 cuss.jar -DLOGPATH=./../logs/ -Xmx384M -Xms384M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+DisableExplicitGC -Xnoclassgc
> 2190 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/itour/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx1024M -Xms1024M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=768M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/itour -Dcatalina.home=/home/appuser/flycua/itour -Djava.io.tmpdir=/home/appuser/flycua/itour/temp
> 126703 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/tms/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx800M -Xms800M -XX:SurvivorRatio=8 -XX:MaxMetaspaceSize=256M -Xss512k -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/tms -Dcatalina.home=/home/appuser/flycua/tms -Djava.io.tmpdir=/home/appuser/flycua/tms/temp
> 112275 Jps -Denv.class.path=.:/usr/java/default/lib/dt.jar:/usr/java/default/lib/tools.jar -Dapplication.home=/usr/java/jdk1.8.0_111 -Xms8m
> 102075 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/seat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx2048M -Xms2048M -XX:MaxMetaspaceSize=512M -XX:SurvivorRatio=8 -Xss512k -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/seat -Dcatalina.home=/home/appuser/flycua/seat -Djava.io.tmpdir=/home/appuser/flycua/seat/temp
> 70748 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/ancillaryfare/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx2048M -Xms1024M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/ancillaryfare -Dcatalina.home=/home/appuser/flycua/ancillaryfare -Djava.io.tmpdir=/home/appuser/flycua/ancillaryfare/temp
> 33021 sales.jar -DLOGPATH=./../logs/ -Xmx384M -Xms384M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+DisableExplicitGC -Xnoclassgc
> 5214 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/gateway/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx3072M -Xms3072M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=768M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/gateway -Dcatalina.home=/home/appuser/flycua/gateway -Djava.io.tmpdir=/home/appuser/flycua/gateway/temp
> 46687 Bootstrap -Djava.util.logging.config.file=/home/appuser/flycua/user/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xmx3078M -Xms1024M -XX:SurvivorRatio=8 -Xss256k -XX:MaxMetaspaceSize=512M -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:+CMSParallelRemarkEnabled -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:+PrintGCDetails -Xloggc:/home/appuser/java_gc_log/ancillary_gc.log -XX:+DisableExplicitGC -Xnoclassgc -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dcatalina.base=/home/appuser/flycua/user -Dcatalina.home=/home/appuser/flycua/user -Djava.io.tmpdir=/home/appuser/flycua/user/temp

&emsp;&emsp;5. `-V` 隐藏输出传递给JVM的参数。
> $ jps -V
> 1760 assembly.jar
> 104833 refundprcocessor.jar
> 27270 Bootstrap
> 3175 Bootstrap
> 79721 integration.jar
> 2285 Bootstrap
> 71084 cuss.jar
> 2190 Bootstrap
> 126703 Bootstrap
> 112468 Jps
> 102075 Bootstrap
> 70748 Bootstrap
> 33021 sales.jar
> 5214 Bootstrap
> 46687 Bootstrap