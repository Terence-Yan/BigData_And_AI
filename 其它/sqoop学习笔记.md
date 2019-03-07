#### 1.Sqoop简介
Sqoop是一款开源的工具，主要用于在Hadoop(Hive)与传统的关系型数据库(RDBMS)间进行数据的传递。可以将一个关系型数据库中的数据导入到Hadoop的HDFS中，也可以
将HDFS中的数据导入到关系型数据库中。

#### 2.Sqoop原理
将数据导入或导出命令转换成MapReduce程序来实现，在MapReduce程序中，主要是对InputFormat和OutputFormat进行定制。

#### 3.Sqoop安装
* 安装Sqoop之前要先准备好Java和Hadoop环境。
* 安装步骤：
```
1.下载安装包
2.将安装包上传到服务器
3.解压Sqoop安装包到指定目录
4.进入conf目录，修改配置文件
  (1).创建用户自定义配置文件：cp sqoop-env-template.sh sqoop-env.sh
  (2).编辑配置文件：
       export HADOOP_COMMON_HOME=Hadoop的安装路径
       export HADOOP_MAPRED_HOME=Hadoop的安装路径
       export HIVE_HOME=Hive的安装路径
       export ZOOKEEPER_HOME=Zookeeper的安装路径
       export ZOOCFGDIR=Zookeeper的安装路径
       export HBASE_HOME=Hbase的安装路径
5.拷贝jdbc驱动包到sqoop的lib目录下
6.验证Sqoop是否安装成功：执行bin/sqoop help命令，若出现如下输出，则表示安装成功：
   “Warning: /usr/local/src/sqoop/bin/../../hbase does not exist! HBase imports will fail.
    Please set $HBASE_HOME to the root of your HBase installation.
    Warning: /usr/local/src/sqoop/bin/../../hcatalog does not exist! HCatalog jobs will fail.
    ...
    19/03/07 18:08:44 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6
    usage: sqoop COMMAND [ARGS]
    vailable commands:
       codegen            Generate code to interact with database records
       create-hive-table  Import a table definition into Hive
       eval               Evaluate a SQL statement and display the results
       export             Export an HDFS directory to a database table...”
7.测试Sqoop是否能够成功连接数据库：执行 bin/sqoop list-databases（sqoop的命令：列出所有的数据库） 
                                    --connect jdbc:mysql://mysqlhost:3306/ 
                                    --username tom 
                                    --password 123
  当出现预期的结果时，则表示连接成功。
```

#### 4.Sqoop之导入数据
在Sqoop中，“导入”概念是指：从非大数据集群(RDBMS)向大数据集群(Hadoop、Hive、Hbase)中传输数据，即使用import关键字。

#### 5.从RDBMS导入到HDFS
```
(1).确定mysql服务正常开启；
(2).在mysql中准备测试数据:create table student(id int(4) primary key not null auto_increment,name varchar(20),age int(3));
(3).导入数据
    a).全量导入：将mysql数据库中目标表的所有数据导入HDFS  
                   bin/sqoop import \
                   --connect jdbc:mysql://mysqlhost:3306/ \
                   --username tom \
                   --password 123 \
                   --table st \     数据源表
                   --target-dir /usr/hive \    导入HDFS的目标路径
                   --delete-target-dir \       若目标路径存在，则删除并新建
                   --num-mappers 1 \
                   --fields-terminated-by "#"  数据输出到文件所用分隔符
```









