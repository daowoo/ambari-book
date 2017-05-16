# 基本架构

## 组成部分

达沃大数据平台基本架构采用的是Server/Client的模式，其核心由两部分组成：若干个Agent和一个Server。

* Server: 接收Agent Interface的状态上报请求完成集中式管理和监控逻辑。
* Agent: 负责所在节点主机的状态采集、维护及利用Agent Interface上报。

另外还需要一些必要的系统及第三方服务来保证平台的正常安装和运行。

* DNS Server: 为集群内的各个主机提供域名解析服务，实现主机域名和IP地址的相互映射。
* NTP Server: 为集群内的各个主机提供系统时间同步服务，主要是Server及Agent之间的时间同步。
* Local Repository: 包含了集群安装过程中需必须的所有rpm包，方便离线安装并提高安装速度。
* Postgresql DB: 为Server和Hadoop组件分别创建数据库实例，集中提供数据库服务，方便后期维护和管理。

> 注意：Postgresql可替换为Oracle、MySql等其他数据库。

![](/assets/1.2-structure.png)

## 技术实现

在技术实现上，达沃大数据平台广泛地依赖其它已经成熟的开源工具，还提供了一个界面清亮的管理监控Web页面，达到集群安装的步骤化、集群状态的可视化、集群资源管理的一键化、配置变更历史的版本化。这些自动化手段的运用，极大的降低了集群运维的难度，减少了集群运维的工作量。

* 使用Ganglia收集度量指标。
* 用Nagios支持系统报警，当需要引起管理员的关注时（比如，节点停机或磁盘剩余空间不足等问题），系统将向其发送邮件。
* 支持安装基于Kerberos的安全Hadoop集群，以此实现了对Hadoop安全的支持。
* 提供了基于角色的用户认证、授权和审计功能，并为用户管理集成了LDAP和Active Directory。

![](/assets/1.2-technical.png)

