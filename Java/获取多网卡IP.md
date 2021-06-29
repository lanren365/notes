# 获取多网卡IP
&emsp;&emsp;主要使用的类是：`java.net.InetAddress`。当前IT界IP地址使用的是32位（IPv4）或者128位（IPv6）位无符号数字表示。它是传输层协议TCP，UDP的基础。InetAddress是Java对IP地址的封装，在java.net中有许多类都使用到了InetAddress，包括ServerSocket，Socket，DatagramSocket等等。在默认情况下，类`InetAddress`会缓存一段有限时间映射，对于主机名解析不成功的结果，会缓存非常短的时间（10秒）来提高性能。  
## 代码块
``` java
//在给定主机名的情况下，根据系统上配置的名称服务返回其 IP 地址所组成的数组。

static InetAddress[] getAllByName(String host)

//返回此 InetAddress 对象的原始 IP 地址。

static InetAddress getByAddress(byte[] addr)

// 根据提供的主机名和 IP 地址创建 InetAddress。

static InetAddress getByAddress(String host,byte[] addr)

//在给定主机名的情况下，根据系统上配置的名称服务返回其 IP 地址所组成的数组。

static InetAddress getByName(String host)

// 返回本地主机。

static InetAddress getLocalHost()

//获取此 IP 地址的完全限定域名。
getCanonicalHostName() 

// 获取此 IP 地址的主机名。    

getHostName() 

```
## 获取IP地址实例代码
### 获取单一地址代码
``` java
String ip = InetAddress.getLocalHost().getHostAddress();
```
### 根据域名获取IP代码
``` java
InetAddress[] addresses = InetAddress.getByName("www.sina.com.cn"); 
```
### 获取多网卡IP代码
``` java
try {
    Enumeration<NetworkInterface> interfaces=null;
    interfaces = NetworkInterface.getNetworkInterfaces();
    while (interfaces.hasMoreElements()) {  
        NetworkInterface ni = interfaces.nextElement(); 
        Enumeration<InetAddress> addresss = ni.getInetAddresses();
        while(addresss.hasMoreElements()){
            InetAddress nextElement = addresss.nextElement();
            String hostAddress = nextElement.getHostAddress();
            System.out.println("本机IP地址为：" +hostAddress);
        }
    } 
} catch (Exception e) {
    e.printStackTrace();
}
```