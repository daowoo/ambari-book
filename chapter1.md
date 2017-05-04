# 平台概述
本文档主要介绍达沃大数据平台的整个安装流程，包括平台介绍说明、安装前准备工作、详细的安装过程和注意事项等内容。

## 平台简介

## 基本架构

## 最低需求
### 操作系统版本

|操作系统    |发行版本|
| :--- | :--- |
|CentOS (64-bit)    |CentOS v7.x  |
||CentOS v6.x  |
|Debian    |Debian v7.x|  
|Red Hat (64-bit)    |RHEL 7.0, 7.1, 7.2  |
||RHEL 6.1, 6.2, 6.3, 6.4, 6.5, 6.6, 6.7, 6.8  |
|SUSE (64-bit)    |(SLES) Enterprise Linux 12, SP2 for Teradata  |
||(SLES) Enterprise Linux 12, SP1  |
||(SLES) Enterprise Linux 11, SP4 (HDP 2.2 and later)  |
||(SLES) Enterprise Linux 11, SP3 for Teradata (HDP 2.2 and later) | 
||(SLES) Enterprise Linux 11, SP1 (HDP 2.2) |
|Ubuntu (64-bit)    |Ubuntu 16.04 (Xenial)  |
||Ubuntu 14.04 (Trusty)|

### Web浏览器版本

|操作系统	|浏览器类型及版本|
| :--- | :--- |
|Linux	|Chrome 56.0.2924.87, 57.0.2987|
||Firefox 51, 52|
|Mac OS X	|Chrome 56.0.2924.87, 57.0.2987|
||Firefox 51, 52|
||Safari 10.0.1, 10.0.3|
|Windows	|Chrome 56.0.2924.87, 57.0.2987|
||Edge 38|
||Firefox 51.0.1, 52.0|
||Internet Explorer 10, 11|

### 数据库版本
|组件 |数据库  |描述说明|
| :--- | :--- | :--- |
|Ambari |PostgreSQL 9.1.13+,9.3, 9.4***| |
||MariaDB 10*| |
||MySQL 5.6****| |
||Oracle 11gr2| |
||Oracle 12c**| |
|Druid	|PostgreSQL 9.1.13+, 9.3, 9.4***| |
||MariaDB 10*| |
||MySQL 5.6****| |
|Hive |PostgreSQL 9.1.13+, 9.3, 9.4***| |
||MariaDB 10*| |
||MySQL 5.6****| |
||Oracle 11gr2| |
||Oracle 12c**| |
|Oozie |PostgreSQL 9.1.13+, 9.3, 9.4***| |
||MariaDB 10*| |
||MySQL 5.6****| |
||Oracle 11gr2| |
||Oracle 12c**| |
|Ranger	|PostgreSQL 9.1.13+, 9.3, 9.4***| |
||MariaDB 10*| |
||MySQL 5.6****| |
||Oracle 11gr2| |
||Oracle 12c**| |

### 集群节点数量与存储容量
一般情况下，随着集群中节点主机数量的增加，平台中定义的管理主机所需要的内存和磁盘空间应该不断增大。可参考下面列出的集群规模与存储之间的关系来确定管理主机的恰当配置。

|Number of hosts	|Memory Available	|Disk Space|
| :--- | :--- | :--- |
|1	|1024 MB	|10 GB|
|10	|1024 MB	|20 GB|
|50	|2048 MB	|50 GB|
|100	|4096 MB	|100 GB|
|300	|4096 MB	|100 GB|
|500	|8096 MB	|200 GB|
|1000	|12288 MB	|200 GB|
2000	|16384 MB	|500 GB|

### 建议硬件配置



