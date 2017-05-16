# 调整服务从组件和客户端

该页面为服务在集群中分配适当主机来作为Slave组件，例如DataNodes、NodeManagers和RegionServers等。同时还可以选择特定的主机以安装所有服务的客户端。

* 默认的分配方案，同样我可以通过集群的规划来进行调整。

![](/assets/4.9-client-deault.png)

* 我们在dn系列主机上安装DataNode、NodeManager、RegionServers和Phoenix Query Server，在gw主机上安装NFSGateway以及服务的客户端，后期在集群中添加限制条件，只能通过gw主机才能访问集群资源和运行任务。

![](/assets/4.9-client-adjustment.png)

