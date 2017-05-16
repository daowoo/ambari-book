# 调整服务参数

在初始的安装过程中，建议优先采用默认的参数来完成组件的安装，待平台中所有的组件都正常启动后再根据自己的需求调整各项配置参数来提高性能。

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



## Ambari Metrics组件所必须的管理员密码设置。

## SmartSense组件所必须得管理员密码设置



