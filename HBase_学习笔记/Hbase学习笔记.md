#### 1.Hbase简介
Hbase是一个构建在HDFS之上、分布式的、面向列的开源数据库，是 Google BigTable的开源实现，它主要用于存储海量数据，是Hadoop生态系统中的重要一员。

#### 2.Hbase的优势
* 成熟：社区成熟、理论经过充分实践、丰富的工具支持
* 高效：将随机读写转化为顺序读写，适合高并发写入；均衡效果好，读写性能和机器数保持线性相关；行中没有保存数据的列，不占存储空间
* 分布式特性：基于HDFS、Zookeeper；一致性、可用性、分区容忍性；大数据存储；易扩展

#### 3.Hbase表的特点
* 大：一个表可以有数十亿行，上百万列
* 面向列：面向列(族)的存储和权限访问，列(族)独立索引
* 稀疏：对于为空(null)的列，并不占用存储空间，因此，表可以设计的非常稀疏
* 数据类型单一：Hbase中的数据类型都是字符串(string)
* 无模式：每行都有一个可排序的主键和任意多的列，列可以根据需要动态增加，同一张表不同的行可以有截然不同的列

#### 4.Hbase与RDBMS的区别
* 数据类型：Hbase中的数据都是字符串类型(string)
* 数据操作：Hbase只有普通的增、删、改、查等操作，没有表之间的关联查询
* 存储模式：Hbase是基于列式存储的，而RDBMS是基于行式存储的
* 应用场景：Hbase适合存储大量数据，查询效率极高

#### 5.主键(Row Key)
* 用来检索记录的主键
* 访问Hbase表中的行，只有三种方式：通过单个Row Key访问；通过Row Key的range；全表扫描
* 主键为任意字符串，最大长度为64kb，按字典顺序存储，在Hbase内部保存为字节数组

#### 6.Hbase存储细节
* 每个列族存储在HDFS上的一个单独文件夹中
* Key 和 Version number会在每个列族中存储一份
* 空值不会被保存

#### 7.Hbase的安装
```
单机模式
1.解压Hbase的tar.gz文件
2.修改hbase-env.sh文件，添加Java的环境变量
3.修改hbase-site.xml文件，配置如下参数：
  """
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://master:9000/hbase</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/usr/local/src/hbase-1.1.10/data/zkData</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
</configuration>
  """
4.修改regionservers文件，指定hbase主机
5.验证hbase是否安装成功，依次启动zookeeper、master、regionserver：bin/hbase-daemon.sh start zookeeper（master、regionserver）
6.使用jps查看进程，应该存在以下3个进程：HMaster、HRegionServer、HQuorumPeer
7.最后，查看hbase的webUI，默认端口号：60010(1.0之前版本)，16010(1.0之后版本)。若能成功访问，则安装成功。
```

#### 8.Hbase在HDFS上的文件目录简介
```
    ../hbase
         |_______ .tmp          #hbase的临时目录，对表进行创建、删除等操作时，hbase会将表move到该目录，然后进行操作
         |_______ MasterProcWALs   
         |_______ WALs          #预写日志文件,是regionserver在处理数据的插入和删除的过程中用来记录操作内容的一种日志
         |_______ data          #核心目录，存储Hbase表的数据。默认情况下，该目录下有两个子目录：/hbase/data/default，
                                 在用户创建表的时候，没有指定namespace时，表就创建在此目录下；/hbase/data/hbase 系统内部创建的表
         |_______ hbase.id      #存储的是集群的唯一的cluster id（UUID）
         |_______ hbase.version    #集群版本号
         |_______ oldWALs      #当/hbase/WALs目录中的logs没有用之后，会将这些logs移动到此目录下，HMaster会定期的进行清理
```

#### 9.Hbase Shell操作
```
名称                           命令表达式
创建表                         create '表名称'，'列族名称1'，'列族名称2'，'列族名称N'
添加记录                       put '表名称'，'行名称'，'列名称:'，值
查看记录                       get '表名称'，'行名称'
查看表中的记录总数              count '表名称'
删除记录                       delete '表名称'，'行名称'，'列名称'
删除一张表                     先要禁用该表，才能对其进行删除：第一步 disable '表名称'，第二步 drop '表名称'
查看所有记录                   scan '表名称'
查看某个表某个列中所有数据       scan '表名称'，{COLUMNS='列族名称：列名称'}
更新记录                       就是重写一遍进行覆盖
```





