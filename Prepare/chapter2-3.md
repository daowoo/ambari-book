# 创建DNS服务

大数据平台严重依赖于DNS，在正常的操作期间会执行许多次DNS查找，我们必须为集群内的所有主机配置正向和方向DNS，完成主机名（Fully Qualified Domain Name）到IP地址的映射。要达到这个目的，可以通过编辑集群中所有主机上的/etc/hosts文件或者创建一个DNS服务器来解决，这里我们来详细描述利用bind9来创建DNS服务器，配置集群内的bigdata.wh.com子域，以及采用迭代查询的过程。

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

bind安装完成后，其包含的配置文件和区域文件如下。

```
[root@dns etc]# rpm -ql bind
/etc/logrotate.d/named
/etc/named
/etc/named.conf                   #bind主配置文件
/etc/named.iscdlv.key
/etc/named.rfc1912.zones          #区域(zone)配置文件
/etc/named.root.key
/etc/rndc.conf                    #rndc配置文件
/etc/rndc.key
/etc/rwtab.d/named
/etc/sysconfig/named
/run/named
/usr/lib/systemd/system/named-setup-rndc.service  #服务脚本文件
/usr/lib/systemd/system/named.service
/usr/sbin/ddns-confgen             #生成rndc密钥
/usr/sbin/named
/usr/sbin/named-checkconf          #检测/etc/named.conf文件语法
/usr/sbin/named-checkzone          #检测zone和对应zone文件的语法
/usr/sbin/named-compilezone
/usr/sbin/named-journalprint
/usr/sbin/nsec3hash
/usr/sbin/rndc                     #远程dns管理工具
/usr/sbin/rndc-confgen             #生成rndc密钥
/var/log/named.log
/var/named
/var/named/data
/var/named/dynamic
/var/named/named.ca                #根区域文件
/var/named/named.empty
/var/named/named.localhost         #默认的本地主机解析库
/var/named/named.loopback          #添加区域后新建一个该区域的解析库文件
/var/named/slaves                  #从文件夹
```

* 修改主配置文件/etc/named.conf

```
[root@dns named]# cat /etc/named.conf
options {
	listen-on port 53 { 127.0.0.1;192.168.36.149; };   #添加本机内网IP
	listen-on-v6 port 53 { ::1; };
	directory 	"/var/named";
	dump-file 	"/var/named/data/cache_dump.db";
	statistics-file "/var/named/data/named_stats.txt";
	memstatistics-file "/var/named/data/named_mem_stats.txt";
	allow-query     { localhost; };

	/* 
	 - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
	 - If you are building a RECURSIVE (caching) DNS server, you need to enable 
	   recursion. 
	 - If your recursive DNS server has a public IP address, you MUST enable access 
	   control to limit queries to your legitimate users. Failing to do so will
	   cause your server to become part of large scale DNS amplification 
	   attacks. Implementing BCP38 within your network would greatly
	   reduce such attack surface 
	*/
	recursion yes;

	dnssec-enable yes;
	dnssec-validation yes;

	/* Path to ISC DLV key */
	bindkeys-file "/etc/named.iscdlv.key";

	managed-keys-directory "/var/named/dynamic";

	pid-file "/run/named/named.pid";
	session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
	type hint;
	file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

```

* 追加区域bigdata.wh.com的定义。

```
cat << eof >> /etc/named.rfc1912.zones
zone "bigdata.wh.com" IN {
    type master; #设置类型为master
    file "bigdata.wh.com.zone"; #解析库文件名称为bigdata.wh.com.zone,默认的存储目录为/var/named/
    allow-update { none; };
};
eof
```

* 创建区域bigdata.wh.com的解析库文件。

```
cat << 'eof' > /var/named/bigdata.wh.com.zone
$TTL 600 #定义全局默认超时时间
$ORIGIN bigdata.wh.com. #定义域名后缀
@   IN  SOA    dns.bigdata.wh.com. admin.bigdata.wh.com. (
                20170510 #序列号
                1H       #刷新时
                5M       #重试时间
                1W       #超时时间
                10M )    #否定答案缓存TTL值
        IN      NS      dns            #定义区域内的一台nameserver
dns     IN      A       192.168.36.149 #dns这台nameserver所对应的IP
repo    IN      A       192.168.36.247 #区域内其他主机的A记录
admin   IN      CNAME   dns            #admin是dns的别名，admin.bigdata.wh.com.将解析到dns.bigdata.wh.com.
*       IN      A       192.168.30.1   #泛域名解析，以上都不是的解析到192.168.30.1
eof
```

* 检测配置及启动DNS服务。

```
named-checkconf  #检查主配置文件语法
named-checkzone "bigdata.wh.com" /var/named/bigdata.wh.com.zone #检查区域所对应的解析库文件

systemctl enable named.service
systemctl start named.service
```

* 修改新增区域文件的所有者和权限。

```
[root@dns named]# ps aux|grep named
named     2668  0.0  1.4 165652 15008 ?        Ssl  12:13   0:00 /usr/sbin/named -u named
root      2680  0.0  0.0 112640   960 pts/0    S+   12:14   0:00 grep --color=auto named

chown :named bigdata.wh.com.zone
chmod 640 bigdata.wh.com.zone
```

* 测试域名解析成IP是否正常。

```

```



