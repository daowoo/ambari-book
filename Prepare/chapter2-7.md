# 创建Yum缓存代理服务

通常情况下，我们提供的自定义yum本地源压缩包中包含了大数据平台部署、运行和维护过程中的所有软件rpm包。除非是有特殊需要，比如开发人员试验某些新软件包的功能，或是运维人员安装自己熟悉的某些工具软件等，才需要从外部导入rpm包。因此，绝大多数场景下这个步骤都是可选的，运维人员要根据自己的实际需求来决定是否执行该步骤。

在需要导入外部rpm包的情况下，由于生产环境通常都是与外网进行隔离的，就算是可以通过其他手段获取到外网权限，但是通过修改集群中某些主机的网络配置和repo文件很显然是非常低效和十分危险的，极易造成集群主机之间的配置不一致，甚至数据泄露或丢失。

理想的解决方案应该是：

* yum安装软件时优先从集群内的本地源中查找，若存在则直接下载并安装。
* 若没有找到合适的rpm包，则将http请求转发到一台具有外网访问权限的proxy主机。
* proxy解析并执行该http请求进而从外部的标准源中获取rpm包，并缓存在本地。
* 将rpm包转发给原始请求的那台主机，yum完成软件安装。

其结构图如下：

Centos7环境下目前没有开源的工具能够满足以上的解决方案，不过Ubuntu14环境下有一个apt-cacher-ng的工具能满足要求，并且其最新版本增加了对yum所使用的rpm及repodata的支持。接下来描述的就是其实际安装和配置过程。

## proxy主机初始化

利用Usb的系统安装盘在proxy主机上安装Ubuntu 14.04.5 LTS操作系统，安装完成后首先配置主机名。

```
hostnamectl set-hostname proxy.bigdata.wh.com  #设置主机名
```

然后利用ifconfig查询Ubuntu系统识别的网卡信息，执行结果如下。

```
root@proxy:/etc/network# ifconfig 
eth0      Link encap:Ethernet  HWaddr 08:00:27:54:e0:3b         #以太网口eth1
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe54:e03b/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:22809 errors:0 dropped:0 overruns:0 frame:0
          TX packets:4259 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:31386840 (31.3 MB)  TX bytes:344498 (344.4 KB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:a0:59:b6          #以太网口eth1
          inet addr:192.168.36.111  Bcast:192.168.37.255  Mask:255.255.254.0
          inet6 addr: fe80::a00:27ff:fea0:59b6/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:76679 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3088 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:8766387 (8.7 MB)  TX bytes:401980 (401.9 KB)

lo        Link encap:Local Loopback                               #单机环回网卡
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:34 errors:0 dropped:0 overruns:0 frame:0
          TX packets:34 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:4547 (4.5 KB)  TX bytes:4547 (4.5 KB)
```

## apt-cacher-ng安装及配置

## 集群主机代理配置

## 查看proxy缓存



