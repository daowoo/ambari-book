# 创建Yum本地源

最常用的创建和加载自定义yum本地源的方法有以下三种：

* 下载已整理好的自定义源ISO镜像文件，直接挂载到本地文件系统，在本机上通过**file:**的方式访问。
* 下载已整理好的自定义源tar压缩文件，解压后创建Ftp文件服务器，在局域网通过**ftp:**的方式访问。
* 下载已整理好的自定义源tar压缩文件，解压后创建Web文件服务器，在局域网通过**http:**的方式访问。

我们选择安装和配置都比较简单Apache httpd来实现基于http的Web服务器。

## 获取自定义源压缩包

我们对大数据平台所包含的Server、Agent、Hadoop组件rpm包，以及所依赖的第三方软件rpm包和其他资源都进行了整理，利用createrepo制作成自定义本地源后重新打包并进行相应的版本维护。

* 下载

从公司内网的Ftp服务器上下载bigdata-2.5.0.3-centos7.tar.gz，其地址为[ftp://192.168.16.100/bigdata-2.5.0.3-centos7.tar.gz](ftp://192.168.16.100/bigdata-2.5.0.3-centos7.tar.gz)。

* 解压

将bigdata-2.5.0.3-centos7.tar.gz解压至/home/repo目录，通过tree命令查看其文件系统结构如下：

```
root@proxy:/home/repo# tree -L 2
.
├── ambari                         #Server相关的rpm包
│   └── centos7
├── common                         #依赖的第三方软件rpm包
│   └── repodata
├── hadoop
│   ├── HDP                        #Hadoop核心组件rpm包
│   └── HDP-UTILS-1.1.0.21         #Hadoop相关工具rpm包
└── resource                       #Jdk、jdbc等资源
    ├── jce_policy-8.zip
    ├── jdk-8u77-linux-x64.tar.gz
    ├── postgresql-jdbc.jar
    └── repodata

9 directories, 3 files
```

## 配置yum访问源

* 有永久或临时外网访问权限

为了取得更快的安装速度，将默认的国外源更新为国内的aliyun源或163源。首先备份备份默认源。

```
mv /etc/yum.repos.d /etc/yum.repos.d.bak
```

然后下载对应版本repo文件，放入/etc/yum.repos.d/。

```
mkdir /etc/yum.repos.d
wget -O /etc/yum.repos.d/CentOS7-Base-aliyun.repo http://mirrors.aliyun.com/repo/Centos-7.repo
or
wget -O /etc/yum.repos.d/CentOS7-Base-163.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

* 没有外网访问权限

直接使用之前解压的/home/repo/common目录作为源，通过file://的方式来配置repo文件。同样首先备份下默认源。

```
mv /etc/yum.repos.d /etc/yum.repos.d.bak
```

然后编辑生成使用本地文件系统的repo文件，放入/etc/yum.repos.d/。

```
cat << eof > /etc/yum.repos.d/localfile.repo
[localfile]
name=local file system repository
baseurl=file:///home/repo/common
gpgcheck=0
enabled=1
priority=1
proxy=_none_
eof
```

* 更新yum缓存的数据

运行以下命令生成缓存。

```
yum clean all
yum makecache
```

## 安装yum工具和插件

为了方便后期扩充自己所需的软件包，以及在涉及到多个源之后所需要的优先级管理，接下来安装yum周边的工具和插件。

```
yum install -y yum-utils createrepo      #createrepo命令创建版本库所需要的基础rpm包
yum install -y yum-plugin-priorities     #yum插件，安装后可根据priority值来设置优先级
```

## Apache httpd服务

通过配置httpd虚拟主机的方式实现一个简单的web文件服务器，来作为集群局域网内的yum本地源。首先安装Apache httpd服务。

```
yum install -y httpd
```

安装完成后进入httpd生成的主配置目录/etc/httpd，它的文件结构及意义如下。

    [root@repo httpd]# tree 
    .
    |-- conf                     #主配置目录，通过修改其中的httpd.conf文件来完成配置
    |   |-- httpd.conf
    |   `-- magic
    |-- conf.d                   #扩展配置目录，httpd.conf通过Include conf.d/*的方式来载入该目录中的配置
    |   |-- autoindex.conf
    |   |-- README
    |   |-- userdir.conf
    |   `-- welcome.conf
    |-- conf.modules.d           #httpd核心模块目录
    |   |-- 00-base.conf
    |   |-- 00-dav.conf
    |   |-- 00-lua.conf
    |   |-- 00-mpm.conf
    |   |-- 00-proxy.conf
    |   |-- 00-systemd.conf
    |   `-- 01-cgi.conf
    |-- logs -> ../../var/log/httpd
    |-- modules -> ../../usr/lib64/httpd/modules
    `-- run -> /run/httpd

    6 directories, 13 files

在80端口配置虚拟主机用于通过http方式访问自定义源/home/repo目录，将新建虚拟主机配置文件放在httpd扩展目录conf.d中。

```
cat << eof > /etc/httpd/conf.d/local_repo.conf
<VirtualHost *:80>
  DocumentRoot "/home/repo"
  <Directory "/home/repo">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
  </Directory>
</VirtualHost>
eof
```
> 注意：以上配置的具体含义如下。
```
<VirtualHost *:80>                      #虚拟主机工作在80端口
  DocumentRoot "/home/repo"             #根目录为上面我们解压的自定义源目录
  <Directory "/home/repo">              #根目录的操作权限设置
    Options Indexes FollowSymLinks      #Indexes:支持目录访问， FollowSymLinks:允许跟踪符号链接文件
    AllowOverride None                  #允许所有人访问
    Require all granted
  </Directory>
</VirtualHost>
```






