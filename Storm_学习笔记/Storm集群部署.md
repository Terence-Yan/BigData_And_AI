#### 1.Storm 集群环境准备及部署
```
1.Zookeeper集群安装部署
2.相关依赖环境的安装：主要包括Java、Python等
3.Storm集群安装部署
```

#### 2.Zookeeper 集群部署
```
Zookeeper安装部署的主要流程：
1.配置hosts,测试节点之间的连通性;
2.解压安装包zookeeper-3.x.x.tar.gz;
  a).解压命令：tar -zxvf zookeeper-3.x.x.tar.gz
  b).linux远程拷贝命令：scp -r zookeeper-3.4.10 root@slave1:/usr/local/src/
3.创建项目目录并将解压目录移入项目目录中的zookeeper子目录(这一步不是必须的,只是为了项目管理的方便);
4.修改Zookeeper的配置文件zoo.cfg;
5.建立Zookeeper节点标识文件myid;
6.配置环境变量并使环境变量立即生效;
  a).配置环境变量：vim /etc/profile
     export ZOOKEEPER_HOME=/usr/local/src/zookeeper-3.4.10
     export PATH=$ZOOKEEPER_HOME/bin:$PATH
  b).环境变量生效命令：. /etc/profile
7.启动Zookeeper,查看是否安装成功。
  a).启动：zkServer.sh start
  b).查看状态：zkServer.sh status
```

#### 3.Storm 集群相关依赖软件安装

#### 4.Storm 集群部署
