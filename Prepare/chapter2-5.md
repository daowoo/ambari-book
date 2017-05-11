# 初始化集群部署环境

前面几章我们分别完成了yum本地源服务，dns服务以及ntp服务的创建和配置，接下来我们要为集群中的所有主机配置这些基础服务，进一步还要进行安装部署前的环境初始化和检查。

* 禁用防火墙

```
systemctl stop firewalld.service
systemctl disable firewalld.service

setenforce 0  #临时关闭Selinux
sed -i 's/SELINUX=.*/SELINUX=disabled/' /etc/selinux/config  #开机不启动Selinux
```

* 检查UMASK值

UMASK值设置了用户在系统中创建新文件或文件夹时被授予的默权限或基本权限，大数据平台支持的umask值为022/0022和027/0027。集群中的所有主机均要利用umask命令检查或修改umask值。

```
[root@gw ~]# umask  #查询umask值
0022

[root@gw ~]# umask 022   #设置umask值为022
```

* SSH免密登录

```

```

* DNS设置和检查

* NTP时间同步

* Yum设置本地仓库

* 优化swap分区设置

* 删除版本冲突的包



