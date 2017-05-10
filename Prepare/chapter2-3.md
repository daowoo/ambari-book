# 创建DNS服务

大数据平台严重依赖于DNS，在正常的操作期间会执行许多次DNS查找，我们必须为集群内的所有主机配置正向和方向DNS，完成主机名（Fully Qualified Domain Name）到IP地址的映射。要达到这个目的，可以通过编辑集群中所有主机上的/etc/hosts文件或者创建一个DNS服务器来解决，这里我们来详细描述利用bind9来创建DNS服务器，以及配置集群内的bigdata.wh.com子域的过程。

* 系统安装后配置hostname

```
hostnamectl set-hostname proxy.bigdata.wh.com  #设置主机名
```

* 编辑/etc/hosts文件

首先编辑hosts文件添加本地源的IP地址和主机名之间的映射关系。

```
[root@dns ~]# cat /etc/hosts
127.0.0.1    dns.bigdata.wh.com    dns
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

192.168.36.247 repo.bigdata.wh.com  repo
```

* 下载本地源repo文件，更新yum缓存

```
wget -O /etc/yum.repos.d/bigdata.repo http://repo.bigdata.wh.com/resource/bigdata.repo
yum clean all
yum makecache
```

* 安装bind及相关工具

```
yum install -y bind                #提供DNS服务,程序名named
yum install -y bind-utils          #测试域名的周边工具,如dig、host、nslookup
```

bind安装完成后，其包含的配置文件和区域文件如下：

    [root@dns etc]# tree /etc/ |grep named
    |   |-- named
    |-- named
    |-- named.conf               #主配置文件
    |-- named.iscdlv.key
    |-- named.rfc1912.zones      #区域配置文件
    |-- named.root.key
    |   `-- named
    |   |-- named

    [root@dns etc]# tree /var/named/
    /var/named/
    |-- data
    |-- dynamic
    |-- named.ca                #根区域文件
    |-- named.empty
    |-- named.localhost         #默认的解析库文件
    |-- named.loopback          #添加区域后新建一个该区域的解析库文件
    `-- slaves

    3 directories, 4 files




