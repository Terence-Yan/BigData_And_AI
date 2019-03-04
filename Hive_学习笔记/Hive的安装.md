### 1.Hive的安装
```
1.下载 Hive 的安装包,如 apache-hive-2.1.0-bin.tar.gz
2.解压安装包：tar -xzvf hive-x.y.z.tar.gz
3.配置环境变量并使其立即生效；
  a).配置环境变量：vim /etc/profile
     export HIVE_HOME=/usr/local/src/jdk1.8.0_191
     export PATH=$HIVE_HOME/bin:$PATH
  b).环境变量生效命令：. /etc/profile
4.进入conf目录,编辑hive-site.xml文件(此步骤不是必须的)：
    若hive-site.xml文件存在，则直接编辑；
    若不存在，则需要自己创建，可通过拷贝默认的配置文件获得：cp hive-default.xml.template hive-site.xml
5.启动 Hive 的 CLI：
  a).首先进行元数据存储数据库的初始化：./bin/schematool -dbType derby -initSchema
  "Starting from Hive 2.1, we need to run the schematool command below as an initialization step. 
  For example, we can use "derby" as db type. 
   $ $HIVE_HOME/bin/schematool -dbType <db type> -initSchema "
  b).启动 Hive CLI：$ $HIVE_HOME/bin/hive
注：若元数据存储库使用的是默认的Derby数据库，则在不同的路径下启动CLI时，上面的a)与b)步骤均需按顺序执行。
```

### 2.Hive安装过程中出现问题“...Error: FUNCTION 'NUCLEUS_ASCII' already exists. (state=X0Y68,code=30000)...”释疑
```
在安装Hive的过程中，若在hive-site.xml文件中配置了如下属性：
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
  </property>
  其中databaseName是元数据存储目录所在的路径，默认为metastore_db，其位于hive的安装目录下，是自动创建的。
  若用户更改此配置的值，则其配置的databaseName值不能与默认配置重名，如databaseName=/user/localhost/hive/metastore_db，
  否则，就会出现上面的错误。
```
