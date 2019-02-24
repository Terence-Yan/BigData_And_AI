#### 1.配置文件zoo.cfg中的dataDir参数
虽然可以使用该参数的默认配置，但最好还是把data目录移出/tmp目录，以防止Zookeeper填满了根分区(root partition)。

#### 2.常用的shell命令
```
1).启动服务器：bin/zkServer.sh start，这个命令使得Zookeeper服务器在后台中运行。如果在前台中运行以便查看服务器的输出，可以通过以下
   命令运行：bin/zkServer.sh start-foreground。
2).启动shell客户端：bin/zkCli.sh
3).退出shell客户端：quit
4).停止服务器：bin/zkServer.sh stop
```

#### 3.myid文件应位于(配置文件zoo.cfg中的)dataDir参数所指定的目录下
