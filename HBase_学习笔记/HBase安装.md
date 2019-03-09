#### 1.HBase安装步骤
```
a).确保HDFS处于启动状态
b).在主节点解压缩Hbase
c).修改HBase的环境变量配置文件hbase-env.sh
   添加Java安装目录环境变量：export JAVA_HOME=/安装目录
d).修改hbase-site.xml
e).修改regionservers
f).同步两个从节点: rsync -avz /usr/local/src/hbase-1.1.10(主节点hbase的安装目录) slave1:/usr/local/src/(copy的目标位置)
g).启动Hbase：进入bin目录下, ./start-hbase.sh
h).检查启动是否成功:jps
   主节点 HMaster 从节点 HRegionServer
i).启动Hbase的客户端：进入bin目录下, ./hbase shell
```

#### 2.启动Hbase的shell命令行之后,首次输入list命令时,抛出如下错误：
```
hbase(main):001:0> list
TABLE                                                                                  

ERROR: org.apache.hadoop.hbase.PleaseHoldException: Master is initializing
	at org.apache.hadoop.hbase.master.HMaster.checkInitialized(HMaster.java:2395)
	at org.apache.hadoop.hbase.master.MasterRpcServices.getTableNames(MasterRpcServices.java:882)
```
##### 解决方案
```
1.查看日志
2.日志中有如下错误：
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/src/hbase-1.1.10/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/src/hadoop-2.6.5/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
3.问题定性与解决方法：日志jar包冲突,到指定目录下，删除其中一个jar包即可。
```

#### 3.使用Java API连接Hbase数据库时，连接超时导致无法连接的问题  
```
问题背景描述：在Windows环境下，使用Eclipse进行Hbase项目开发。使用Hbase的Java API连接远程的Hbase服务端，出现连接超时异常而导致无法连接。
Hbase服务端部署在虚拟机上的Linux系统下，Stand Alone模式，Hbase服务端测试是正常的。
异常信息如下：
"""
......
Exception in thread "main" org.apache.hadoop.hbase.client.RetriesExhaustedException: Failed after attempts=36, exceptions:
Sat Mar 09 23:04:03 CST 2019, null, java.net.SocketTimeoutException: callTimeout=60000, callDuration=79807: row 'test01,,' on table
'hbase:meta' at region=hbase:meta,,1.1588230740, hostname=master,16020,1552099289412, seqNum=0
at org.apache.hadoop.hbase.client.RpcRetryingCallerWithReadReplicas.throwEnrichedException(RpcRetryingCallerWithReadReplicas.java:271)
at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas.call(ScannerCallableWithReplicas.java:210)
......
"""
解决办法：只需要在Windows系统的hosts文件里添加Hbase服务端主机的IP与主机名映射即可，例如：
      192.32.128.64      master
```









