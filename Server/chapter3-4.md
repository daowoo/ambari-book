# 启动Server

* 通过start命令启动Server。

```
[root@server resources]# ambari-server start
Using python  /usr/bin/python
Starting ambari-server
Ambari Server running with administrator privileges.
Organizing resource files at /var/lib/ambari-server/resources...
Ambari database consistency check started...
Server PID at: /var/run/ambari-server/ambari-server.pid
Server out at: /var/log/ambari-server/ambari-server.out
Server log at: /var/log/ambari-server/ambari-server.log
Waiting for server start............
```

* 通过status命令查看Server进制状态。

```
[root@server resources]# ambari-server status
Using python  /usr/bin/python
Ambari-server status
Ambari Server running
Found Ambari Server PID: 17791 at: /var/run/ambari-server/ambari-server.pid
```

* 通过stop命令可以关闭Server。

```

```

* 数据库一致性检查。 

Server启动时，会运行数据库一致性检查来查找问题，如果发现任何问题，启动将中止并显示以下消息：DB配置一致性检查失败。 并将有关数据库一致性检查结果的更多详细信息写入/var/log/ambari-server/ambari-server-check-database.log文件。

```
[root@server resources]# cat /var/log/ambari-server/ambari-server-check-database.log
2017-05-14 21:30:18,384  INFO - Checking DB store version
2017-05-14 21:30:20,214  INFO - DB store version is compatible
2017-05-14 21:30:37,208  INFO - ******************************* Check database started *******************************
2017-05-14 21:30:37,208  INFO - Ensuring that the schema set for Postgres is correct
2017-05-14 21:30:37,244  INFO - Checking for configs that are not mapped to any service
2017-05-14 21:30:37,259  INFO - Checking for configs not mapped to any cluster
2017-05-14 21:30:37,269  INFO - Checking for configs selected more than once
2017-05-14 21:30:37,282  INFO - Checking for hosts without state
2017-05-14 21:30:37,293  INFO - Checking host component states count equals host component desired states count
2017-05-14 21:30:37,326  INFO - Checking services and their configs
2017-05-14 21:30:37,326  INFO - Getting ambari metainfo instance
2017-05-14 21:30:37,327  INFO - Executing query 'GET_SERVICES_WITHOUT_CONFIGS'
2017-05-14 21:30:37,337  INFO - Executing query 'GET_SERVICE_CONFIG_WITHOUT_MAPPING'
2017-05-14 21:30:37,348  INFO - Getting stack info from database
2017-05-14 21:30:37,359  INFO - Executing query 'GET_SERVICES_WITH_CONFIGS'
2017-05-14 21:30:37,371  INFO - Comparing service configs from stack with configs that we got from db
2017-05-14 21:30:37,371  INFO - Getting services which has mapped configs which are not selected in clusterconfigmapping
2017-05-14 21:30:37,384  INFO - Checking Topology tables
2017-05-14 21:30:37,406  INFO - ******************************* Check database completed *******************************
```

* 通过浏览器访问Server在默认端口8080开启的Web主页。

```
http://server.bigdata.wh.com:8080/
```



