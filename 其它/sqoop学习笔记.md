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
                   --connect jdbc:mysql://mysqlhost:3306/database_name \
                   --username tom \
                   --password 123 \
                   --table st \     数据源表
                   --target-dir /usr/hive \    导入HDFS的目标路径
                   --delete-target-dir \       若目标路径存在，则删除并新建
                   --num-mappers 1 \
                   --fields-terminated-by "#"  数据输出到文件所用分隔符
     b).查询导入：通过指定查询SQL将mysql数据库中的目标数据导入HDFS  
                   bin/sqoop import \
                   --connect jdbc:mysql://mysqlhost:3306/database_name \
                   --username tom \
                   --password 123 \
                   --target-dir /usr/hive \    导入HDFS的目标路径
                   --delete-target-dir \       若目标路径存在，则删除并新建
                   --num-mappers 1 \
                   --fields-terminated-by "#" \     数据输出到文件所用分隔符
                   --query 'select * from st where id<=3 and $CONDITIONS;'   指定查询语句
                或 --query "select * from st where id<=3 and \$CONDITIONS;"  $CONDITIONS是sqoop的参数，不能省略
     c).指定列导入：通过指定目标表的列信息将mysql数据库中的所有数据导入HDFS  
                   bin/sqoop import \
                   --connect jdbc:mysql://mysqlhost:3306/database_name \
                   --username tom \
                   --password 123 \
                   --target-dir /usr/hive \    导入HDFS的目标路径
                   --delete-target-dir \       若目标路径存在，则删除并新建
                   --num-mappers 1 \
                   --fields-terminated-by "#" \     数据输出到文件所用分隔符
                   --table stu \   数据源表
                   --columns name,age     指定数据源表中的目标列，如果涉及多列，则用逗号分隔，分隔时不能添加空格
      d).通过WHERE关键字筛选查询导入：通过指定查询条件将mysql数据库中的所有数据导入HDFS  
                   bin/sqoop import \
                   --connect jdbc:mysql://mysqlhost:3306/database_name \
                   --username tom \
                   --password 123 \
                   --target-dir /usr/hive \    导入HDFS的目标路径
                   --delete-target-dir \       若目标路径存在，则删除并新建
                   --num-mappers 1 \
                   --fields-terminated-by "#" \     数据输出到文件所用分隔符
                   --table stu \   数据源表   
                   --where "id=3"      where与query参数不能同时使用
```

#### 6.从RDBMS导入到Hive
```
bin/sqoop import \
--connect jdbc:mysql://mysqlhost:3306/database_name \
--username tom \
--password 123 \
--table st \     数据源表
--num-mappers 1 \
--fields-terminated-by "#" \  数据输出到文件所用分隔符
--hive-import \    hive导入声明
--hive-overwrite \   目标表存在则对其进行覆盖
--hive-table [database_name.]table_name     Hive数据库的目标表，如果没有指定数据库信息，则默认是Hive中的default数据库
注：1.进行数据的导入过程中，执行sqoop命令时，总是抛出连接被拒绝的异常信息，其中一段一直没有引起注意的异常信息如下：
   “...
    19/03/07 23:18:08 INFO hive.HiveImport: Caused by: java.sql.SQLException: Unable to open a test connection to the given database.
    JDBC url = jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true, username = tom. Terminating connection pool (set 
    lazyInit to true if you expect to start your database after your app). 
    Original Exception: -----
    19/03/07 23:18:08 INFO hive.HiveImport: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
    19/03/07 23:18:08 INFO hive.HiveImport:
    ...
   ”。
   解决问题思考概述：刚开始，一直以为是mysql数据库访问权限的问题，做了各种尝试之后，还是无济于事。最后才重新认真查看分析日志信息，上面的一段信息再一
   次引起了注意。这时，想起来，hive的配置文件中设置了元数据库的访问信息，并且当前机器是hive的客户端，其安装文件是从另一台安装了hive服务端的机器上远
   程拷贝过来的。可能是hive配置文件中的mysql的访问主机配置没有修改，即还是服务端配置的localhost。而现在是通过远程连接访问hive服务端的，所以应该将
   localhost改为hive服务端的主机名称。在对Hive的配置文件修改之后，重新执行了sqoop导入命令，最终，执行成功。
   2.从RDBMS导入数据到Hive，该过程其实是分两步执行的，第一步是先将数据导入到HDFS，默认的临时目录是“/user/用户名/表名”，第二步再将导入到HDFS的数据
   迁移到Hive仓库。
```

#### 7.从RDBMS导入到Hbase
```
bin/sqoop import \
--connect jdbc:mysql://mysqlhost:3306/database_name \
--username tom \
--password 123 \
--table st \     数据源表
--columns "id,name,age" \   指定数据源表的目标列
--num-mappers 1 \
--hbase-create-table \   导入的目标表不存在，则创建(该属性可能因为版本兼容问题不可用)
--hbase-table "hbase_table" \  目标表
--column-family "info" \  指定列簇信息
--hbase-row-key "id" \    
--split-by id   
注：sqoop1.4.6只支持HBase1.0.1之前的版本的自动创建HBase表的功能。
```

#### 8.Sqoop之导出数据
在Sqoop中，“导出”概念是指：从大数据集群(Hadoop、Hive)向非大数据集群(RDBMS)中传输数据，即使用export关键字。

#### 9.从Hive/HDFS导出到RDBMS(暂不支持直接从Hbase导出到RDBMS)
```
bin/sqoop export \
--connect jdbc:mysql://mysqlhost:3306/database_name \
--username tom \
--password 123 \
--table st \     目标表(Mysql中的表若不存在，不会自动创建)
--num-mappers 1 \
--export-dir /usr/hive/data \  数据源文件所在目录
--input-fields-terminated-by "#"    数据源文件的分隔符
```

#### 10.通过编辑脚本执行sqoop导出任务
```
(1).创建一个.opt文件：
          [root@master ~]# mkdir sqoopsh
          [root@master ~]# touch sqoopsh/hdfs2rdbms_job.opt
(2).编写sqoop脚本：
          [root@master ~]# vim sqoopsh/hdfs2rdbms_job.opt
          """
             export
             --connect
             jdbc:mysql://hostname:3306/database_name
             --username 
             root
             --password 
             123
             --table
             table_name
             --num-mappers
             1
             --export-dir
             /user/hive/data
             --input-fields-terminated-by
             "\t"
          """
 (3).执行该sqoop脚本：  bin/sqoop --options-file sqoopsh/hdfs2rdbms_job.opt
```


