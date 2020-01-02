# 1. Linux虚拟IP配置以及IP漂移
## 1.1. HA
### 1.1.1. 概念
高可用性HA（High Availability）指的是：通过尽量缩短因日常维护操作（计划）和突发的系统崩溃（非计划）所导致的停机时间，以提高系统和应用的可用性。HA系统是目前企业防止核心计算机系统因故障停机的最有效手段。
### 1.1.2. 落地
实现HA的方式，一般采用两台机器同时完成一项功能，比如数据库服务器，平常只有一台机器对外提供服务，另一台机器作为热备，当这台机器出现故障时，自动动态切换到另一台热备的机器。
* 怎么实现故障检测的那？  
      心跳，采用定时发送一个数据包，如果机器多长时间没响应，就认为是发生故障，自动切换到热备的机器上去。
* 怎么实现自动切换那？  
      虚IP。何为虚IP那，就是一个未分配给真实主机的IP，也就是说对外提供数据库服务器的主机除了有一个真实IP外还有一个虚IP，使用这两个IP中的 任意一个都可以连接到这台主机，所有项目中数据库链接一项配置的都是这个虚IP，当服务器发生故障无法对外提供服务时，动态将这个虚IP切换到备用主机。
### 1.1.3. 原理
实现原理主要是靠TCP/IP的ARP协议。因为ip地址只是一个逻辑 地址，在以太网中MAC地址才是真正用来进行数据传输的物理地址，每台主机中都有一个ARP高速缓存，存储同一个网络内的IP地址与MAC地址的对应关 系，以太网中的主机发送数据时会先从这个缓存中查询目标IP对应的MAC地址，会向这个MAC地址发送数据。操作系统会自动维护这个缓存。这就是整个实现 的关键。
下边就是我电脑上的arp缓存的内容。

> (192.168.1.219) at <font color=red>00:21:5A:DB:68:E8</font> [ether] on bond0  
> (192.168.1.217) at <font color=red>00:21:5A:DB:68:E8</font> [ether] on bond0  
> (192.168.1.218) at 00:21:5A:DB:7F:C2 [ether] on bond0

192.168.1.217、192.168.1.218是两台真实的电脑，
192.168.1.217为对外提供数据库服务的主机。
192.168.1.218为热备的机器。
192.168.1.219为虚IP。
大家注意红字部分，219、217的MAC地址是相同的。
再看看那217宕机后的arp缓存
```sh
(192.168.1.219) at 00:21:5A:DB:7F:C2 [ether] on bond0
(192.168.1.217) at 00:21:5A:DB:68:E8 [ether] on bond0
(192.168.1.218) at 00:21:5A:DB:7F:C2 [ether] on bond0 
```
架构图如下：  
![HA样例架构](_v_images/_ha样例架构_1522224033_31632.png)
* Nginx 反向代理Server（HA）：  
     ①Nginx master：192.168.1.157  
     ②Nginx backup：192.168.1.158  
* web服务器：  
     192.168.1.160 ，192.168.1.161，192.168.1.162即web服务器，已配置好 Tomcat(Jboss等皆可)和Java程序  
## 1.2. 虚拟IP的配置
1. 添加ip
```vim
[root@zw-test-db mastermha]#/sbin/ip addr add 192.168.26.74/32 dev eth1
```
2. 查看添加的虚拟ip
```vim
[root@zw-test-db mastermha]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
    link/ether 00:50:56:a3:6f:08 brd ff:ff:ff:ff:ff:ff
    inet 192.168.26.233/24 brd 192.168.26.255 scope global eth1
    inet 192.168.26.74/32 scope global eth1  #这个ip就是我刚新加的
    inet6 fe80::250:56ff:fea3:6f08/64 scope link 
       valid_lft forever preferred_lft foreve
```
3. 比如要想删除刚添加的虚拟ip就用如下命令：
```vim
[root@zw-test-db mastermha]#/sbin/ip addr del 192.168.26.74/32 dev eth1
```
5. 为了防止下次重启找不到添加的虚拟ip，可以把命令加到/etc/rc.loca里面
```vim
/sbin/ip addr add 192.168.26.74/32 dev eth1
```

## 1.3. Nginx配置
①修改配置文件：`vi /etc/nginx/nginx.conf`  
* 步骤1.添加负载均衡的http upstream模块
```vim
upstream  esbwebserver  {
  server   192.168.1.160:8888;
  server   192.168.1.161:8888;
}
```
* 步骤2.server指令
```vim
server
{
listen  80;
server_name  localhost;#注意此处为localhost

location / {
       proxy_pass        http://esbwebserver;# 添加的tomcat集群名称
       ......
}
```  
## Keepalived 双机热备
配置文件位置：
 /usr/local/keepalived/etc/keepalived/keepalived.conf  
 步骤一，先配置主Nginx server上的keepalived.conf文件，如下所示：
```vim
 ! Configuration File for keepalived
 
  global_defs {
  notification_email {
    644856452@qq.com
  }
  notification_email_from 644856452@qq.com
  smtp_server 127.0.0.1
  smtp_connect_timeout 30
  router_id LVS_DEVEL
}


vrrp_script check_run {
   script "/root/bin/nginx_check.sh"
   interval 5
}
vrrp_sync_group VG1 {
    group {
       VI_1
    }
}


vrrp_instance VI_1 {
   state MASTER
   interface eth0
   virtual_router_id 51
   mcast_src_ip 192.168.1.157
   priority 100
   advert_int 1
   authentication {
       auth_type PASS
       auth_pass 1111
   }

    track_script {
        check_run
    }

   virtual_ipaddress {
       192.168.1.110
   }
}
```
步骤二，配置backup 服务器的keepalived.conf文件，如下所示：
```vim
! Configuration File for keepalived

global_defs {
  notification_email {
    644856452@qq.com     
  }
  notification_email_from Alexandre.Cassen@firewall.loc
  smtp_server 127.0.0.1
  smtp_connect_timeout 30
  router_id LVS_DEVEL
}


vrrp_script check_run {
   script "/root/bin/nginx_check.sh"
   interval 5
}
vrrp_sync_group VG1 {
    group {
      VI_1 
    }
}


vrrp_instance VI_1 {
   state BACKUP
   interface eth0
   virtual_router_id 51
   priority 99
   advert_int 1
   authentication {
       auth_type PASS
       auth_pass 1111
   }


    track_script {
        check_run
    }
   virtual_ipaddress {
       192.168.1.110
   }
}
```
参考下例配置：
-----------------------------------------------------带注释的示例----------------------------------------------------
在主服务器编写配置文件
```vim
vrrp_script check_run{
                script "/opt/nginx_pid.sh"         
###监控脚本
                interval 2                             
###监控时间
                weight 2                                ###如果检测返回值不为真weight 2 表示减2，权重值降低，backup server权重值>现Master的，切换
}


vrrp_instance VI_1 {
        state MASTER                           ### 设置为 主
        interface eth0                            ### 监控网卡    
        virtual_router_id 51                  ### 这个两台服务器必须一样
        priority 101                                 ### 权重值 MASTRE 一定要高于 BAUCKUP
        authentication {
                     auth_type PASS             ### 加密
                     auth_pass 1111          ###加密的密码，两台服务器一定要一样，不然会出错
}

       track_script {
                check_run     ### 执行监控的服务
        }

        virtual_ipaddress {
             192.168.1.110                          ###    VIP 地址
        }
}
```

6.在backup server  服务器 keepalived 配置
```vim
vrrp_script check_run{
                script "/opt/nginx_pid.sh"
                interval  2
                weight   2
}


vrrp_instance VI_1 {
       state BACKUP                                ### 设置为 辅机
        interface eth0
        virtual_router_id 51                      ### 与 MASTRE 设置 值一样
        priority 100                                     ### 比 MASTRE权重值 低

        authentication {
                     auth_type PASS
                     auth_pass eric                    ### 密码 与 MASTRE 一样
        }

        track_script {
                check_run
        }

        virtual_ipaddress {
                 192.168.1.110
        }
}
```
----------------------------------------------over------------------------------------------------------------------- 

说明：  
1. 绑定虚拟IP：
  ifconfig eth0:1 192.168.1.110 broadcast 192.168.1.255 netmask 255.255.255.0 up  
  route add -host 192.168.1.110 dev eth0:1

2.  启动，关闭keepalived ：  
 service keepalived start  
 service keepalived stop

3. 测试可用性：  
①主Nginx停止Nginx或直接断网情况下（backup正常），访问虚拟IP：192.168.1.110的相关Web服务，正常，测试通过  
②backup Nginx停止Nginx或直接断网情况下（Master正常），访问虚拟IP：192.168.1.110的相关Web服务，正常，测试通过
