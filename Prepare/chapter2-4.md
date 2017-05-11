# 创建NTP服务

为了保障大数据平台中Server收集到的状态信息、配置信息、任务调度信息以及保存的历史记录信息的有序和一致，集群内所有的节点主机和通过浏览器访问Web管理页面的客户机之间必须彼此时钟同步。因此我们通过在所有主机上安装NTP服务来保证时间同步，即以某台主机作为ntp server，其他主机作为ntp client从server端同步时间，这里我们选择本地源repo主机作为server。

* 安装并启动ntp服务

```
yum install -y ntp

systemctl start ntpd.service
systemctl enable ntpd.service
```

* 设置语言区域、时区

```
localectl set-locale LANG=en_US.utf8     #设备系统语言及区域
timedatectl set-timezone Asia/Shanghai   #设置时区
```

* 同步网络时间或手动设置当前时间

```
ntpdate time.ntp.org               #有外网条件下，直接更新互联网标准时间
timedatectl set-ntp yes            #启用NTP同步

timedatectl set-time 2017-05-11    #无外网条件下，手动设置当前日期
timedatectl set-time 16:47:00      #设置当前时间
```



