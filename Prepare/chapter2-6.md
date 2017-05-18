# 创建Postgresql数据库

* 安装Postgresql二进制包。

```
yum install -y postgresql-server    #安装服务主程序
yum install -y postgresql-contrib   #安装第三方包和分布式包（可选）
```

* Postgresql安装后的目录说明。

```
[root@db libexec]# rpm -ql postgresql-server.x86_64
/usr/bin/*                                          #initdb、postgres、psql等可执行程序存放在此目录
/usr/lib/systemd/system/postgresql.service          #系统服务注册脚本，包含有服务自启动时PGDATA变量的定义
/usr/lib/tmpfiles.d/postgresql.conf                 #系统服务自启动时的主配置文件
/usr/lib64/pgsql/*                                  #动态库目录，程序运行所需要的动态库均在此目录下
/usr/libexec/initscripts/legacy-actions/postgresql  #initdb执行初始化时的脚本
/usr/share/pgsql                                    #配置文件模板文件以及一些扩展包的sql脚本文件
/var/lib/pgsql                                      #postgres用户的根目录，.bash_profile文件中定义了PGDATA环境变量
```

* 初始化数据库，我们直接使用postgres默认的数据目录`/var/lib/pgsql/data`

```
[root@db ~]# su postgres         #切换至安装PG时创建的系统账户postgres
bash-4.2$ ls
backups  data
bash-4.2$ initdb -D data/ -U postgres -W     #初始化数据库
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

fixing permissions on existing directory data ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 32MB
creating configuration files ... ok
creating template1 database in data/base/1 ... ok
initializing pg_authid ... ok
Enter new superuser password:    #设置PG数据库超级管理员用户postgres的密码
Enter it again: 
setting password ... ok
initializing dependencies ... ok
creating system views ... ok
loading system objects' descriptions ... ok
creating collations ... ok
creating conversions ... ok
creating dictionaries ... ok
setting privileges on built-in objects ... ok
creating information schema ... ok
loading PL/pgSQL server-side language ... ok
vacuuming database template1 ... ok
copying template1 to template0 ... ok
copying template1 to postgres ... ok
```

* 数据库初始化后，在数据库目录data中会生成一系列的文件夹和文件，它们的具体含义如下。

```tree
bash-4.2$ tree -L 1 data/
data/
\|-- base \#默认表空间的目录，与每个数据库对应的子目录存储在该目录中
\|-- global \#集群范围的表存储在该目录中，比如‘pg\_database’
\|-- pg\_clog \#包含事务提交状态数据的子目录
\|-- pg\_hba.conf \#访问认证配置文件，包括允许哪些IP的主机访问，采用的认证方法是什么等信息  
\|-- pg\_ident.conf \#‘ident’认证方式的用户映射文件
\|-- pg\_multixact \#包含多重事务状态数据的子目录\(使用共享的行锁\)
\|-- pg\_notify \#包含LISTEN/NOTIFY状态数据的子目录
\|-- pg\_serial \#包含已提交可串行化事务信息的子目录
\|-- pg\_snapshots \#包含输出快照的子目录
\|-- pg\_stat\_tmp \#用于统计子系统的临时文件存储在该目录中
\|-- pg\_subtrans \#包含子事务状态数据的子目录
\|-- pg\_tblspc \#包含指向表空间的符号链接的子目录
\|-- pg\_twophase \#包含用于预备事务的状态文件的子目录
\|-- PG\_VERSION \#一个包含PostgreSQL主版本号的文件
\|-- pg\_xlog \#包含WAL\(预写日志\)文件的子目录
\`-- postgresql.conf \#数据库实例的主配置文件，基本上所有的配置参数均在此文件中
12 directories, 4 files
```

* 配置PG监听的IP和端口，对于拥有多个IP的主机，可根据实际需要选择监听哪几个IP。

```
bash-4.2$ cd data/
bash-4.2$ vi postgresql.conf
# - Connection Settings -
listen_addresses = '*'         #监听所有ip的连接，默认是本机
port = 5432                    #这个不开也行，默认就是5432端口
```

* 配置访问权限，为了提高安全性，可以配置成只允许特定的几台主机访问。

```
bash-4.2$ vim pg_hba.conf
# TYPE  DATABASE        USER            ADDRESS                 METHOD
# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
host    all             all             0.0.0.0/0               md5   #添加行，所有IP和用户，密码对都可以连接
# IPv6 local connections:
host    all             all             ::1/128                 trust
```

* 利用PG默认提供的管理工具**pg_ctrl**来启动数据库，然后再查看5432端口是否正常开放。

```
bash-4.2$ pg_ctl start -D data/  #利用pg_ctl启动指定数据库实例
server starting
bash-4.2$ ss -tpnl |grep 5432  #查看数据库服务监听的端口是否正常
LISTEN     0      128                       *:5432                     *:*      users:(("postgres",10297,3))
LISTEN     0      128                      :::5432                    :::*      users:(("postgres",10297,4))
```

* 利用PG默认提供的客户端工具psql来连接并操作数据库，psql的操作命令本文不作详细描述，请参考psql --help。

```
bash-4.2$ psql -U postgres   #psql客户端采用postgres用户登陆数据库（当前登陆的是默认的全局数据库postgres）
psql (9.2.18)
Type "help" for help.

postgres=# \l           #执行psql客户端环境下提供的命令，该命令是列出该数据库实例中的所有数据库及模板
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)

postgres=# \t
Showing only tuples.
postgres=# ALTER USER postgres WITH PASSWORD '1';      #利用SQL语句修改管理员密码
ALTER ROLE
postgres=# \q            #退出psql客户端
bash-4.2$ exit           #退出postgres用户
exit
```

* 利用GUI工具连接并操作数据库

postgresql数据库的GUI工具有很多，与PG配合比较好用的主要是Navicat Premium和pgAdmin 3/4了。其中pgAdmin 4采用QT开发，支持跨平台，GUI非常赞，还添加了性能实时监视图表，不过目前多语言和流畅性都还有点问题。

pgAdmin4登录界面：

![](/assets/2.6-pgadmin4-conn.png)

pgAdmin4操作主界面：  
![](/assets/2.6-pgadmin4-view.png)

* 配置PG跟随系统自启动

二进制安装时会自动把postgresql.service配置文件拷贝到`/usr/lib/systemd/system/`目录下，如果需要对自启动服务进行自定义配置，建议不要直接修改`/usr/lib/systemd/system/postgresql.service`文件，而是将该文件拷贝至`/etc/systemd/system/`目录，然后再修改目标文件，并在目标文件内通过**.include**指令包含源文件。

```
systemctl enable postgresql.service
systemctl start postgresql.service

[root@localhost system]# vi /etc/systemd/system/postgresql-9.5.service 
# It's not recommended to modify this file in-place, because it will be
# overwritten during package upgrades.  If you want to customize, the
# best way is to create a file "/etc/systemd/system/postgresql-9.5.service",
# containing
#       .include /lib/systemd/system/postgresql-9.5.service
#       ...make your changes here...
# For more info about custom unit files, see
# http://fedoraproject.org/wiki/Systemd#How_do_I_customize_a_unit_file.2F_add_a_custom_unit_file.3F
# Note: changing PGDATA will typically require adjusting SELinux
# configuration as well.
# Note: do not use a PGDATA pathname containing spaces, or you will
# break postgresql-setup.
.include /lib/systemd/system/postgresql-9.5.service       #通过‘.include’指令包含源文件中的配置项

[Service]
Environment=PGDATA=/home/pgsql/data/                      #然对指定配置项目进行修改
```



