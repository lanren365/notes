# 1. Log的四种记录框架
## 1.1. j.u.l
￼j.u.l是java.util.logging包的简称，是JDK在1.4版本中引入的Java原生日志框架。Java Logging API提供了七个日志级别用来控制输出。这七个级别分别是：SEVERE、WARNING、INFO、CONFIG、FINE、FINER、FINEST。
## 1.2. Log4j
Log4j是Apache的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件，甚至是套接口服务器、NT的事件记录器、UNIX Syslog守护进程等；我们也可以控制每一条日志的输出格式；

通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。Log4也有七种日志级别：OFF、FATAL、ERROR、WARN、INFO、DEBUG和TRACE。

最令人感兴趣的就是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。
## 1.3. LogBack
LogBack也是一个很成熟的日志框架，其实LogBack和Log4j出自一个人之手，这个人就是Ceki Gülcü。
logback当前分成三个模块：logback-core,logback- classic和logback-access。
logback-core是其它两个模块的基础模块。
logback-classic是Log4j的一个改良版本。此外logback-classic完整实现SLF4J API使你可以很方便地更换成其它日记系统如Log4j或j.u.l。
logback-access访问模块与Servlet容器集成提供通过Http来访问日记的功能。
## 1.4. Log4j2
前面介绍过Log4j，这里要单独介绍一下Log4j2，之所以要单独拿出来说，而没有和Log4j放在一起介绍，是因为作者认为，Log4j2已经不仅仅是Log4j的一个升级版本了，而是从头到尾被重写的，这可以认为这其实就是完全不同的两个框架。

关于Log4j2解决了Log4j的哪些问题，Log4j2相比较于Log4j、j.u.l和logback有哪些优势，我们在后续的文章中介绍。
# 2. Log4j2配置文件的说明
onMatch和onMismatch都有三个属性值，分别为Accept、DENY和NEUTRAL

分别介绍这两个配置项的三个属性值：

onMatch="ACCEPT" 表示匹配该级别及以上
onMatch="DENY" 表示不匹配该级别及以上
onMatch="NEUTRAL" 表示该级别及以上的，由下一个filter处理，如果当前是最后一个，则表示匹配该级别及以上
onMismatch="ACCEPT" 表示匹配该级别以下
onMismatch="NEUTRAL" 表示该级别及以下的，由下一个filter处理，如果当前是最后一个，则不匹配该级别以下的
onMismatch="DENY" 表示不匹配该级别以下的
