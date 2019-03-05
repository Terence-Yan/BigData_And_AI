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
注：1.若元数据存储库使用的是默认的Derby数据库，则在不同的路径下启动CLI时，上面的a)与b)步骤均需按顺序执行，此时
    不同路径下存储的元数据是不能共享的。
    2.若元数据存储库使用的是mysql数据库，则在不同的路径下启动CLI时，初始化步骤a)只需执行一次，此时
    不同路径下存储的元数据是可以共享的。
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

### 3.Hive服务端的启动与远程连接
```
1.将hive当做一个服务器启动的命令：bin/hiveserver2
2.远程连接hive服务器的命令：1.bin/beeline
                          2.beeline>(beeline命令提示符) ! connect jdbc:hive2://master(要连接的主机名):10000(默认端口)
```

### 4.远程连接hive服务器时，可能出现如下权限验证异常
```
Error: Failed to open new session: java.lang.RuntimeException: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.security.authorize.AuthorizationException): User: root is not allowed to impersonate anonymous (state=,code=0)
```
解决方案：
```
在hadoop的配置文件core-site.xml中添加如下属性： 
  <property> 
     <name>hadoop.proxyuser.root.hosts</name> 
     <value>*</value> 
  </property> 
  <property> 
     <name>hadoop.proxyuser.root.groups</name> 
     <value>*</value> 
  </property>
释疑：就将上面配置hadoop.proxyuser.xxx.hosts和hadoop.proxyuser.xxx.groups中的xxx设置为root(即你的错误日志中显示的User：xxx为什么就设置为什么)。“*”表示可通过超级代理“xxx”操作hadoop的用户、用户组和主机。重启hdfs。
这样改的原因：
主要原因是hadoop引入了一个安全伪装机制，使得hadoop 不允许上层系统直接将实际用户传递到hadoop层，而是将实际用户传递给一个超级代理，由此代理在hadoop上执行操作，避免任意客户端随意操作hadoop。
注：该解决方案来自网络
```
