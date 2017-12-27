# 安装Server

* 利用root用户登录server主机

```
Connecting to 192.168.70.100:22...
Connection established.
To escape to local shell, press 'Ctrl+Alt+]'.

WARNING! The remote SSH server rejected X11 forwarding request.
Last login: Sun May 14 18:34:36 2017 from 192.168.70.1
Welcome to your Vagrant-built virtual machine.
[root@server ~]#
```

* 确认本地源及配置repo列表

```
yum repolist
```

* 安装server端

```
[root@server ~]# yum install ambari-server
Total download size: 670 M
Installed size: 755 M
Is this ok [y/d/N]: y
Downloading packages:
(1/4): postgresql-9.2.18-1.el7.x86_64.rpm                                                          | 3.0 MB  00:00:03
(2/4): postgresql-server-9.2.18-1.el7.x86_64.rpm                                                   | 3.8 MB  00:00:05
(3/4): postgresql-libs-9.2.18-1.el7.x86_64.rpm                                                     | 232 kB  00:00:00
(4/4): ambari-server-2.5.0.3-7.x86_64.rpm                                                          | 663 MB  00:01:00
------------------------------------------------------------------------------------------------------------------4/4
Installed:
  ambari-server.x86_64 0:2.5.0.3-7                                                                                    

Dependency Installed:
  postgresql.x86_64 0:9.2.18-1.el7  postgresql-libs.x86_64 0:9.2.18-1.el7  postgresql-server.x86_64 0:9.2.18-1.el7
     
Complete!
```



