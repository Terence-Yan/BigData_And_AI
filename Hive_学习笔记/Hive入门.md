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

#### 4.查询当前数据库下所有数据表的命令：show tables;
