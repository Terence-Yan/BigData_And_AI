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
创建命名空间                   create_namespace '命名空间名称'
删除命名空间                   drop_namespace '命名空间名称'
-----------------------------------------------------------------------------------------------------------------
status  查看Hbase集群的概要信息
whoami  查看当前用户
list_namespace  查看集群所有的命名空间
```

#### 10.Hbase客户端Client
* 整个Hbase集群的入口
* 使用HBase RPC机制与HMaster和HRegionserver通信
* 与HMaster通信进行管理类的操作
* 与HRegionserver通信进行读写类的操作
* 包含访问HBase的接口，并维护cache来加快对HBase的访问，与HRegionserver交互

#### 11.Hbase的程序协调服务Zookeeper
* 保证任何时候，集群中只有一个Master（HA）
* 存储所有Region的寻址入口
* 实时监控Region Server的上线和下线信息，并实时通知给Master
* 存储HBase的schema和table的元数据

#### 12.Hbase的主节点Master
* 管理用户对Table(不涉及表中存储的数据)的增删改查操作
* 管理HRegionServer的负载均衡，调整Region的分布
* 在Region Split后，负责新Region的分配
* 在HRegionServer失效后，负责失效的HRegionServer上的Region的迁移
* HMaster失效仅仅会导致所有元数据无法修改，表的数据的读写还可以正常进行

#### 13.Hbase的HRegionServer节点
* 维护HRegion，并往HDFS写数据
* 当表的大小超过设置值的时候，Split(切分) HRegion
* 在HRegionServer停机后，负责失效的HRegionServer上的Region的迁移

#### 14.Hbase与Zookeeper的关系
* HBase元数据存储在Zookeeper中
* 默认情况下，HBase管理Zookeeper实例(也就是HBase自带的Zookeeper)，比如，启动或者停止Zookeeper
* Zookeeper解决HBase的单节点故障问题
* HMaster与HRegionServer启动时，会向Zookeeper注册

#### 15.Hbase Region的定位
```
寻找RegionServer的过程
(1).Zookeeper：读取Zookeeper获取-ROOT-表的信息(0.96版本之后，ROOT表已被删除)
(2).-ROOT-：-ROOT-表包含.META.表所在的Region列表，该表只会有一个region，Zookeeper中记录了-ROOT-表的location
(3). .META.：.META.表包含所有的用户空间region列表，以及RegionServer的服务器地址
(4).用户表
(5).Client第一次操作后，会将-ROOT-和.META.缓存到本地，不需要再访问Zookeeper
```

#### 16.Hbase的容错性
* Master容错：Zookeeper重新选择一个新的Master。无Master的过程中，数据的读取仍正常进行；region切分、负载均衡等无法进行
* RegionServer容错：定时向Zookeeper汇报心跳，如果一段时间内未出现心跳，Master将该RegionServer上的region重新分配到其他RegionServer上。</br>
  失效服务器上“预写”日志由主服务器进行分割并派送给新的RegionServer。
* Zookeeper容错：Zookeeper高可靠的服务，不存在单点故障

#### 17.Hbase的数据存储
* Hbase中的所有数据文件都存储在HDFS文件系统上，格式主要有两种：
```
HFile：HBase中KeyValue数据的存储格式，HFile是Hadoop的二进制格式文件，实际上StoreFile就是对HFile做了轻量级封装，即StoreFile的底层就是HFile。
HLog：HBase中WAL(Write Ahead Log)的存储格式，物理上是Hadoop的Sequence File带项目符号的内容。
```

#### 18.Hbase的数据存储--HRegionServer
* HRegionServer管理一系列的HRegion对象
* 每个HRegion对应Table中的一个Region，HRegion由多个HStore组成
* 每个HStore对应Table中一个Column Family的存储
* Column Family就是一个集中的存储单元，故将具有相同IO特性的Column放在一个Column Family会更高效(避免跨文件读取数据)

#### 19.Hbase写入数据的过程
```
1.Client写入 -> 存入MemStore，一直到MemStore满 -> Flush成一个StoreFile，直至增长到一定阈值 -> 出发Compact合并操作 -> 多个StoreFile合并成一个
StoreFile，同时进行版本合并和数据删除 -> 当StoreFiles Compact后，逐步形成越来越大的StoreFile -> 单个StoreFile大小超过一定阈值后，触发Split操作，
把当前Region Split成2个Region，被分割的Region就会下线，新Split出的2个孩子Region会被HMaster分配到相应的HRegionServer上，使得原先1个Region的压力
得以分流到2个Region上
2.HBase只是增加数据，所有的更新和删除操作，都是在Compact阶段做的，所以，用户写操作只需要进入到内存即可立即返回，从而保证I/O高性能
```

#### 20.HStore文件
```
StoreFile以HFile格式保存在HDFS上，包括以下六个数据段信息：
(1).Data Block段：保存表中的数据，这部分可以被压缩
(2).Meta Block段(可选的)：保存用户自定义的kv对，可以被压缩
(3).File Info段：HFile的元信息，不压缩，用户也可以在这一部分添加自己的元信息
(4).Data Block Index段：Data Block的索引，每条索引的key是被索引的block的第一条记录的key
(5).Meta Block Index段(可选的)：Meta Block的索引
(6).Trailer：这一段是定长的，保存的是每一段的偏移量
```

#### 21.HStore文件的压缩
```
1.HFile的Data Block，Meta Block通常采用压缩方式存储:
优点：压缩之后可以大大减少网络IO和磁盘IO
缺点：需要花费cpu进行压缩和解压缩
2.HFile支持的压缩格式：Gzip，Lzo，Snappy等
```

#### 22.HStore文件的KeyValue存储
* HFile里面的每个KeyValue对就是一个简单的byte数组
* KeyLength和ValueLength：两个固定的长度，分别代表Key和Value的长度
* Key部分：Row Length是固定长度的数值，表示RowKey的长度，Row就是RowKey
* Column Family Length是固定长度的数值，表示Family的长度,接着就是Column Family，再接着是Qualifier，然后是两个固定长度的数值，
  表示Time Stamp和Key Type（Put/Delete）
* Value部分没有这么复杂的结构，就是纯粹的二进制数据

#### 23.HLog文件结构
* HLog文件就是一个普通的Hadoop Sequence File，Sequence File的Key是HLogKey对象，HLogKey中记录了写入数据的归属信息，除了table和
  region名字外，同时还包括sequence number和timestamp，timestamp是“写入时间”，sequence number的起始值为0，或者是最近一次存入文
  件系统中sequence number。
* HLog Sequece File的Value是HBase的KeyValue对象，即对应HFile中的KeyValue。

#### 24.将数据从HDFS导入HBase
```
1.在Linux的shell命令行输入：HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase classpath` ${HADOOP_HOME}/bin/hadoop jar /usr/local/src/
  hbase-1.1.10/lib/hbase-server-1.1.10.jar获取Hbase数据传输的帮助命令
2.执行数据传输命令：HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase classpath` ${HADOOP_HOME}/bin/hadoop jar /usr/local/src/hbase-1.1.10
  /lib/hbase-server-1.1.10.jar importtsv -Dimporttsv.columns=HBASE_ROW_KEY,info:name,info:age test02 /hbase_test
  业务含义：表示将HDFS上的文件/hbase_test中的数据导入到Hbase默认命名空间的test02表中，其中/hbase_test文件中每行有3列由制表符分割的数据，这3
  列数据与表test02中的列的对应关系分别为HBASE_ROW_KEY(即第一列为主键),info:name,info:age。
  tsv文件：由制表符分割列值的文件。
3.使用-Dimporttsv.bulk.output参数，执行数据传输命令：HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase classpath` ${HADOOP_HOME}/bin/hadoop jar 
  /usr/local/src/hbase-1.1.10/lib/hbase-server-1.1.10.jar importtsv -Dimporttsv.columns=HBASE_ROW_KEY,info:name,info:age test02  
  /hbase_test -Dimporttsv.bulk.output=/hbase_test。在数据量较大时，添加该参数能显著提高执行效率，推荐使用该种方式。
```

#### 25.将数据从HBase导出到HDFS
```
1.执行数据传输命令：HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase classpath` ${HADOOP_HOME}/bin/hadoop jar /usr/local/src/hbase-1.1.10
  /lib/hbase-server-1.1.10.jar export test02 /hbase_test
  业务含义：表示将Hbase默认命名空间中的表test02中的数据导出到HDFS文件系统的/hbase_test目录中。
```

#### 26.将数据从HDFS导入到HBase
```
1.执行数据传输命令：HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase classpath` ${HADOOP_HOME}/bin/hadoop jar /usr/local/src/hbase-1.1.10
  /lib/hbase-server-1.1.10.jar import test02 /hbase_test
  业务含义：表示将HDFS上的目录/hbase_test中的文件数据导入到Hbase默认命名空间的test02表中。
```











