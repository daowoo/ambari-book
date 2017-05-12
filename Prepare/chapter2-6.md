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

* 初始化数据库，我们直接使用postgres默认的数据目录/var/lib/pgsql/data
  。

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

* 数据库初始化后，在数据库目录data中会生成一系列的文件夹和文件。

  bash-4.2$ tree -L 1 data/  
    data/  
    \|-- base           \#默认表空间的目录，与每个数据库对应的子目录存储在该目录中  
    \|-- global         \#集群范围的表存储在该目录中，比如‘pg\_database’  
    \|-- pg\_clog        \#包含事务提交状态数据的子目录  
    \|-- pg\_hba.conf    \#访问认证配置文件，包括允许哪些IP的主机访问，采用的认证方法是什么等信息  
    \|-- pg\_ident.conf  \#‘ident’认证方式的用户映射文件  
    \|-- pg\_multixact   \#包含多重事务状态数据的子目录\(使用共享的行锁\)  
    \|-- pg\_notify      \#包含LISTEN/NOTIFY状态数据的子目录  
    \|-- pg\_serial      \#包含已提交可串行化事务信息的子目录  
    \|-- pg\_snapshots   \#包含输出快照的子目录  
    \|-- pg\_stat\_tmp    \#用于统计子系统的临时文件存储在该目录中  
    \|-- pg\_subtrans    \#包含子事务状态数据的子目录  
    \|-- pg\_tblspc      \#包含指向表空间的符号链接的子目录  
    \|-- pg\_twophase    \#包含用于预备事务的状态文件的子目录  
    \|-- PG\_VERSION     \#一个包含PostgreSQL主版本号的文件  
    \|-- pg\_xlog        \#包含WAL\(预写日志\)文件的子目录  
    \`-- postgresql.conf   \#数据库实例的主配置文件，基本上所有的配置参数均在此文件中

  12 directories, 4 files

* 配置PG监听的IP和端口

```
bash-4.2$ cd data/
bash-4.2$ vi postgresql.conf
# - Connection Settings -
listen_addresses = '*'         #监听所有ip的连接，默认是本机
port = 5432                    #这个不开也行，默认就是5432端口
```

* 配置访问权限

```

```



