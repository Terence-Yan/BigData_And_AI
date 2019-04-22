#### 1.Hadoop格式化时，出现namenode无法启动
```
问题描述：Hadoop是伪分布式模式，且当前机器部署了一个完全分布式的Hadoop集群，错误提示信息如下：
"""
......
19/03/11 14:16:30 FATAL namenode.NameNode: Failed to start namenode.
java.lang.IllegalArgumentException: URI has an authority component
	at java.io.File.<init>(File.java:423)
	at org.apache.hadoop.hdfs.server.namenode.NNStorage.getStorageDirectory(NNStorage.java:329)
	at org.apache.hadoop.hdfs.server.namenode.FSEditLog.initJournals(FSEditLog.java:270)
......
"""
解决办法：在hdfs-site.xml文件重新配置namenode与datanode的存储路径：
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/softwares/hadoop-2.6.5/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/softwares/hadoop-2.6.5/dfs/data</value>
    </property>
原因分析：可能由于Hadoop的默认配置特性与当前机器的配置有冲突
警告： 这种场景下，其他的默认配置信息也存在冲突的可能。
```

#### 2.启动Hadoop时，出现namenode启动失败
```
问题描述：Hadoop是伪分布式模式，且当前机器部署了一个完全分布式的Hadoop集群，错误提示信息如下：
"""
...
2019-03-11 15:39:20,330 FATAL org.apache.hadoop.hdfs.server.namenode.NameNode: Failed to start namenode.
java.lang.IllegalArgumentException: Does not contain a valid host:port authority: 13308
	at org.apache.hadoop.net.NetUtils.createSocketAddr(NetUtils.java:212)
	at org.apache.hadoop.net.NetUtils.createSocketAddr(NetUtils.java:164)
	at org.apache.hadoop.net.NetUtils.createSocketAddr(NetUtils.java:153)
	at org.apache.hadoop.hdfs.server.namenode.NameNode.getHttpAddress(NameNode.java:534)
...
"""
解决办法：查看hdfs-site.xml文件中的HDFS Web UI的端口配置是否有效。
问题解决之前的配置：
    <property>
        <name>dfs.http.address</name>
        <value>13308</value>
    </property>
问题解决之后的配置：
    <property>
        <name>dfs.http.address</name>
        <value>hostname:13308</value>
    </property>
```

#### 3.跨平台运行job任务
```
如果需要从windows系统上运行job提交的客户端程序，则需要设置跨平台提交参数：
   conf.set("mapreduce.app-submission.cross-platform", "true");
```

#### 4.【Error: java.io.IOException: Unable to initialize any output collector】
```
问题背景：Windows开发环境，在Eclipse上运行job提交任务，控制台显示了如标题所示的异常信息
问题根因：提交job任务的主类中的Text类引用错误
         job.setMapOutputKeyClass(Text.class);
解决方案：应该引用的是hadoop包中的Text类——org.apache.hadoop.io.Text
```




