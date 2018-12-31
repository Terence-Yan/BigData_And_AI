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
5.建立Zookeeper节点标识文件myid,myid文件应该放在zk快照文件目录下(在zoo.cfg文件中配置的,一般目录名称是zkData);
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
```
Java安装的主要流程：
1.解压安装包jdk-8uXX-linux-x64.gz;
2.配置环境变量并使其立即生效；
  a).配置环境变量：vim /etc/profile
     export JAVA_HOME=/usr/local/src/jdk1.8.0_191
     export JDK_HOME=$JAVA_HOME
     export PATH=$JAVA_HOME/bin:$PATH
  b).环境变量生效命令：. /etc/profile
3.检查Java版本及是否安装成功。
  a).java -version
  b).javac -version
 
 Python安装的主要流程：
 1.解压安装包Python-2.x.x.tgz;
   a).解压：tar -zxvf Python-2.x.x.tgz
 2.进入解压目录，进行编译安装;
   a).进入解压目录： cd Python-2.x.x
   b).执行默认配置： ./configure
   c).第二步骤会在解压目录生成一个Makefile文件,执行编译命令：make
   d).执行安装命令：make install
 3.检查Python版本及是否安装成功。
   a).python --version
```

#### 4.Storm 集群部署
```
Storm安装的主要流程：
1.解压安装包apache-storm-0.9.2-incubating.zip;
  a).解压安装包： unzip apache-storm-0.9.2-incubating.zip
2.建立状态存储文件夹并修改Storm配置文件storm.yaml;
  a).修改配置文件： vim conf/storm.yaml;
     storm.zookeeper.servers:
         - "master"
         - "slave1"
         - "slave2"
     nimbus.seeds: ["master"]
     storm.local.dir: "/usr/local/storm/status"
     supervisor.slots.ports:
         - 6700
         - 6701
         - 6702
         - 6703
3.配置环境变量并使其立即生效；
  a).配置环境变量： vim /etc/profile
     export STORM_HOME=/usr/local/src/apache-storm-1.0.5
     export PATH=$STORM_HOME/bin:$PATH
  b).使环境变量生效：. /etc/profile
4.启动Storm集群并检查相应的进程;
  a).启动Storm的UI命令： storm ui >/dev/null 2>&1 &(最后的这个符号表示在后台启动程序)
  b).启动Storm的nimbus节点命令： storm nimbus >/dev/null 2>&1 &  (其中步骤a与b都是在nimbus节点上执行)
  c).启动Storm的supervior工作节点命令： storm supervisor >/dev/null 2>&1 &
5.通过浏览器登录监控UI页面,查看监控属性。
  a).UI页面访问地址：192.169.2.30(nimbus节点所在主机IP):8080
```







