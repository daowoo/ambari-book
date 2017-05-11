# 创建NTP时钟同步服务

为了保障大数据平台中Server收集到的状态信息、配置信息、任务调度信息以及保存的历史记录信息的有序和一致，集群内所有的节点主机和通过浏览器访问Web管理页面的客户机之间必须彼此时钟同步。因此我们通过在所有主机上安装NTP服务来保证时间同步，即以某台主机作为ntp server，其他主机作为ntp client从server端同步时间，这里我们选择本地源repo主机作为server。

* 安装并启动ntp服务。

```
yum install -y ntp

systemctl start ntpd.service
systemctl enable ntpd.service
```

* 设置语言区域、时区。

```
localectl set-locale LANG=en_US.utf8     #设备系统语言及区域
timedatectl set-timezone Asia/Shanghai   #设置时区
```

* 同步网络时间或手动设置当前时间。

```
ntpdate time.ntp.org               #有外网条件下，直接更新互联网标准时间
timedatectl set-ntp yes            #启用NTP同步

timedatectl set-time 2017-05-11    #无外网条件下，手动设置当前日期
timedatectl set-time 16:47:00      #设置当前时间
```

* 修改ntp服务的配置文件，创建ntp server端，确保如下配置项。

```
[root@repo yum.repos.d]# cat /etc/ntp.conf 
# Permit all access over the loopback interface.  This could
# be tightened as well, but to do so would effect some of
# the administrative functions.
restrict 127.0.0.1   #确保localhost本身有足够权限
restrict ::1

# Hosts on local network are less restricted.
#restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
restrict 192.168.0.0 mask 255.255.0.0 nomodify notrap  #配置客户端访问NTP Server的权限，
                                                       #授权192.168.0.0~192.168.255.255网段
                                                       #客户机被允许从这台机器上查询和同步时间

#broadcast 192.168.1.255 autokey    # broadcast server
#broadcastclient            # broadcast client
#broadcast 224.0.1.1 autokey        # multicast server
#multicastclient 224.0.1.1        # multicast client
#manycastserver 239.255.254.254        # manycast server
#manycastclient 239.255.254.254 autokey # manycast client

server  127.127.1.0   #将localhost的本地时钟作为时间供给源，这样，即便该机器失去网络连接，也可以继续为网络提供服务
fudge   127.127.1.0 stratum 10
```

* 重启ntp服务，并查询ntp服务状态。

```
[root@repo yum.repos.d]# systemctl restart ntpd.service

[root@repo yum.repos.d]# ntpstat       #查询ntp服务同步状态
synchronised to NTP server (115.28.122.198) at stratum 3 
   time correct to within 1804 ms
   polling server every 64 s

[root@repo yum.repos.d]# ntpq -p       #查看连接上的ntp远程服务器
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 79.98.105.18    .INIT.          16 u    -   64    0    0.000    0.000   0.000
+52.187.51.163   202.83.101.70    2 u   43   64    1   60.413  -1586.1   3.586
*time6.aliyun.co 10.137.38.86     2 u   45   64    1   47.904  -1582.3   2.975
 61.216.153.107  211.22.103.158   3 u   48   64    1   50.326  -1583.0   0.106
 LOCAL(0)        .LOCL.          10 l   67   64    2    0.000    0.000   0.000
```



