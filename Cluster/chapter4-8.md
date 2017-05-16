# 调整主服务

该页面为选中的服务组件在集群中分配适当主机来作为Master。页面左侧列显示服务和所选择的当前主机，右列显示这些主机当前的组件分配情况，并指示主机CPU内核的数量和RAM的数量。

初始情况下，系统会给出默认的建议分配方案，我们可以直接采用该默认方案或者自己动手进行调整。

* 默认的分配方案，如果集群中节点较多，建议平均分配这些服务组件，避免单个节点的负荷过重，进而影响集群性能。

![](/assets/4.8-default.png)

* 根据之前规划的主机名来调整主服务位置和个数。

```
1. NameNode和ResourceManager组件调整到nn主机；
2. SNameNode和HST等其他服务组件调整到snn主机；
3. Hive和HBase相关的组件调整到hive主机；
4. Grafana和Activity等余下的组件则全部调整到gw主机；
5. ZoopKeeper数量为三个，分别分布在nn、snn和hive三台主机上。
```

![](/assets/4.8-adjustment.png)

* 可以通过主机旁边绿色的‘+/-’图标来增加/减少组件的Master个数。

![](/assets/4.8-add-del.png)

