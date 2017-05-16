# 调整服务参数

在初始的安装过程中，建议优先采用默认的参数来完成组件的安装，待平台中所有的组件都正常启动后再根据自己的需求调整各项配置参数来提高性能。  
![](/assets/4.10-top-title.png)

## Hive组件所必须的数据库连接设置。

这里我们选择连接在db主机上创建的postgresql数据库，以便实现所有组件数据库的集中管理和维护。

* 首先在db主机上创建hive数据库。

```
[root@db ~]# sudo -u postgres psql
could not change directory to "/root"
psql (9.2.18)
Type "help" for help.

postgres=# CREATE DATABASE hive;
CREATE DATABASE
postgres=# CREATE USER hive WITH PASSWORD '123';
CREATE ROLE
postgres=# GRANT ALL PRIVILEGES ON DATABASE hive TO hive;
GRANT
postgres=# \c hive
You are now connected to database "hive" as user "postgres".
hive=# CREATE SCHEMA hive AUTHORIZATION hive;
CREATE SCHEMA
hive=# ALTER SCHEMA hive OWNER TO hive;
ALTER SCHEMA
hive=# ALTER ROLE hive SET search_path to 'hive', 'public';
ALTER ROLE
hive=# \q
[root@db ~]# psql -U hive -d hive
psql (9.2.18)
Type "help" for help.

hive=> \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 ambari    | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =Tc/postgres         +
           |          |          |            |            | postgres=CTc/postgres+
           |          |          |            |            | ambari=CTc/postgres
 hive      | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =Tc/postgres         +
           |          |          |            |            | postgres=CTc/postgres+
           |          |          |            |            | hive=CTc/postgres
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(5 rows)
```

* 完善数据库连接配置，测试数据库连接是否正常。

![](/assets/4.10-hive-config.png)

* 观察连接失败日志可知错误原因是Server中还未设置postgres JDBC驱动程序和程序路径。

首先从本地源中下载postgresql-jdbc.jar数据库驱动包。

```
[root@server ~]# wget http://repo.bigdata.wh.com/resource/postgresql-jdbc.jar
--2017-05-16 09:00:31--  http://repo.bigdata.wh.com/resource/postgresql-jdbc.jar
Resolving repo.bigdata.wh.com (repo.bigdata.wh.com)... 192.168.36.247
Connecting to repo.bigdata.wh.com (repo.bigdata.wh.com)|192.168.36.247|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 446067 (436K) [application/x-java-archive]
Saving to: ‘postgresql-jdbc.jar’

100%[=====================================================================>] 446,067     --.-K/s   in 0.1s    

2017-05-16 09:00:32 (3.82 MB/s) - ‘postgresql-jdbc.jar’ saved [446067/446067]
```

然后在Server中通过ambari-server setup命令来配置postgres驱动包。

```
[root@server ~]# ambari-server setup --jdbc-db=postgres --jdbc-driver=/root/postgresql-jdbc.jar
Using python  /usr/bin/python
Setup ambari-server
Copying /root/postgresql-jdbc.jar to /var/lib/ambari-server/resources
If you are updating existing jdbc driver jar for postgres with postgresql-jdbc.jar. 
Please remove the old driver jar, from all hosts. Restarting services that need the driver, 
will automatically copy the new jar to the hosts.
JDBC driver was successfully initialized.
Ambari Server 'setup' completed successfully.
```

* 重新测试postgres数据库连接。

![](/assets/4.10-conn-sucessful.png)

## Ambari Metrics组件所必须的管理员密码设置。

![](/assets/2.10-password-metrics.png)

## SmartSense组件所必须得管理员密码设置

![](/assets/2.10-password-smartsense.png)




