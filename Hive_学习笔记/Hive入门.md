#### 1.开启在CLI提示符前打印出当前所在数据库名的功能
```
 在CLI命令行输入命令：set hiveconf:hive.cli.print.current.db=true
 或者在hive-site.xml文件中配置此属性值,默认配置是false。
 设置之前,CLI命令界面：
 hive> 
 设置之后,CLI命令界面：
 hive (default)> 
```

#### 2.查询所有数据库的命令：show databases;

#### 3.查询所有数据库的命令：create database [if not exists] <数据库名>;
```
"IF NOT EXISTS"子句是可选的,若使用此子句，则在存在同名数据库的情况下，CLI命令界面不会提示数据库创建失败的信息，
否则，就会有数据库创建失败的提示信息。
```

#### 4.查询当前数据库下所有数据表的命令：show tables;

#### 5.Hive中的数据库
```
Hive中数据库的概念本质上仅仅是表的一个目录或者命名空间。Hive会为每个数据库创建一个目录。数据库中的表将会以这个数据库目录的
子目录形式存储。有一个例外就是default数据库中的表，因为这个数据库本身没有自己的目录。数据库所在的目录位于属性hive.metastore.
warehouse.dir所指定的目录下面。
```

#### 6.CASCADE关键字
```
默认情况下，Hive是不允许用户删除一个包含有表的数据库的。用户要么先删除数据库中的表，然后再删除数据库；要么在删除数据库命令的
最后面加上关键字CASCADE，这样可以使Hive自行删除数据库中的表：
    DORP DATABASE IF EXISTS <mydatabase> CASCADE;
```
