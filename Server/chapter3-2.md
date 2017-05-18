# 创建PG实例

Server端默认情况下，在其启动过程中会在本机安装并创建postgresql数据库实例，并调用`/var/lib/ambari-server/resources/`目录的SQL脚本来初始化数据库实例。为了便于集中管理，我们使用了独立的主机来创建postgresql数据库实例，并且hadoop集群的其他组件所需的数据库均在该主机上创建。

* 利用root用户登录登录db主机。

```
WARNING! The remote SSH server rejected X11 forwarding request.
Last login: Sun May 14 18:35:36 2017 from 192.168.1.54
Welcome to your Vagrant-built virtual machine.
[root@db ~]#
```

* 利用psql登录默认数据库。

```
[root@db ~]# sudo -u postgres psql                              #以postgres用户登录
could not change directory to "/root"
psql (9.2.18)
Type "help" for help.

postgres=# CREATE DATABASE ambari;                               #创建数据库ambari
CREATE DATABASE
postgres=# CREATE USER ambari WITH PASSWORD '123';               #创建用户ambari并设置密码
CREATE ROLE
postgres=# GRANT ALL PRIVILEGES ON DATABASE ambari TO ambari;    #给用户ambari添加操作数据库ambari的授权
GRANT
postgres=# \c ambari                                             #切换登录到ambari数据库
You are now connected to database "ambari" as user "postgres".
ambari=# CREATE SCHEMA ambari AUTHORIZATION ambari;              #创建从属于用户ambari的模式ambari
CREATE SCHEMA
ambari=# ALTER SCHEMA ambari OWNER TO ambari;                    #修改模式ambari的拥有者为用户ambari
ALTER SCHEMA
ambari=# ALTER ROLE ambari SET search_path to 'ambari', 'public';
ALTER ROLE
ambari=# \q
```

* 从server主机的`/var/lib/ambari-server/resources/`目录拷贝DDL初始化脚本。

```
[root@db ~]# scp vagrant@server.bigdata.wh.com:/var/lib/ambari-server/resources/Ambari-DDL-Postgres-CREATE.sql ./
The authenticity of host 'server.bigdata.wh.com (192.168.70.100)' can't be established.
ECDSA key fingerprint is 75:06:22:b7:e2:3b:46:88:51:97:c4:4f:27:20:21:26.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'server.bigdata.wh.com,192.168.70.100' (ECDSA) to the list of known hosts.
vagrant@server.bigdata.wh.com's password: 
Ambari-DDL-Postgres-CREATE.sql                                                       100%   77KB  77.3KB/s   00:00    
[root@db ~]# ls
Ambari-DDL-Postgres-CREATE.sql  anaconda-ks.cfg  ec2-keypair
```

* 利用DDL脚本初始化ambari数据库

```
[root@db ~]# psql -U ambari -d ambari
psql (9.2.18)
Type "help" for help.

ambari=> \i /root/Ambari-DDL-Postgres-CREATE.sql
```

* 列出ambari数据库中的所有数据表

```
ambari=> \dt
 ambari | adminpermission               | table | ambari
 ambari | adminprincipal                | table | ambari
 ambari | adminprincipaltype            | table | ambari
 ambari | adminprivilege                | table | ambari
 ambari | adminresource                 | table | ambari
 ambari | adminresourcetype             | table | ambari
 ambari | alert_current                 | table | ambari
 ambari | alert_definition              | table | ambari
 ambari | alert_group                   | table | ambari
 ambari | alert_group_target            | table | ambari
 ambari | alert_grouping                | table | ambari
 ambari | alert_history                 | table | ambari
 --More--
```

至此自定义的数据库就成功创建了，当在server主机上启动Server时，我们就使用ambari用户来登录`db.bigdata.wh.com`主机上的ambari数据库。

