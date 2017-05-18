# 配置Server

在启动Server之前，必须先完成Server的数据库配置、JDK安装、自定义账户。

* 执行setup命令

```
[root@server resources]# ambari-server setup
Using python  /usr/bin/python
Setup ambari-server
Checking SELinux...
SELinux status is 'disabled'
```

* 默认情况下，使用root用户运行Server进程。

```
Customize user account for ambari-server daemon [y/n] (n)? n
Adjusting ambari-server permissions and ownership...
Checking firewall status...
```

* 选择JDK版本。

```
Checking JDK...
[1] Oracle JDK 1.8 + Java Cryptography Extension (JCE) Policy Files 8
[2] Oracle JDK 1.7 + Java Cryptography Extension (JCE) Policy Files 7
[3] Custom JDK
==============================================================================
Enter choice (1):
```

* 拷贝repo主机`/home/repo/resource`目录中的jdk-8u112安装包至Server的资源目录。

```
scp vagrant@repo.bigdata.wh.com:/home/repo/resource/jdk-8u112-linux-x64.tar.gz /var/lib/ambari-server/resources/  
scp vagrant@repo.bigdata.wh.com:/home/repo/resource/jce_policy-8.zip /var/lib/ambari-server/resources/
```

* 选择安装【1】。

```
Enter choice (1): 1
JDK already exists, using /var/lib/ambari-server/resources/jdk-8u112-linux-x64.tar.gz
Installing JDK to /usr/jdk64/
Successfully installed JDK to /usr/jdk64/
JCE Policy archive already exists, using /var/lib/ambari-server/resources/jce_policy-8.zip
Installing JCE policy...
Completing setup...
```

* 配置postgresql数据库。

```
Enter advanced database configuration [y/n] (n)? y    #启用高级配置
Configuring database...
==============================================================================
Choose one of the following options:
[1] - PostgreSQL (Embedded)
[2] - Oracle
[3] - MySQL / MariaDB
[4] - PostgreSQL
[5] - Microsoft SQL Server (Tech Preview)
[6] - SQL Anywhere
[7] - BDB
==============================================================================
Enter choice (1): 4                                  #选择自定义的PG数据库
Hostname (localhost): db.bigdata.wh.com              #配置PG主机的hostname
Port (5432): 5432                                    #配置PG主机的数据库端口
Database name (ambari): ambari                       #配置数据库名称
Postgres schema (ambari): ambari                     #配置数据库内的模式名
Username (ambari): ambari                            #配置数据库用户名
Enter Database Password (bigdata):                   #输入该用户的密码
Re-enter password: 
Configuring ambari database...
Configuring remote database connection properties...
WARNING: Before starting Ambari Server, you must run the following DDL against the database to create the schema: 
/var/lib/ambari-server/resources/Ambari-DDL-Postgres-CREATE.sql
```

* 确认数据库连接信息，完成Server安装过程。

```
Proceed with configuring remote database connection properties [y/n] (y)? y   #确认连接信息
Extracting system views...
ambari-admin-2.5.0.3.7.jar
...........
Adjusting ambari-server permissions and ownership...
Ambari Server 'setup' completed successfully.
```



