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

```

```



