#### 1.Linux系统配置环境变量的两种方式
* 在/etc/profile文件中配置,此种配置对所有用户有效
* 在~/.bashrc文件中配置,此种配置只对当前用户有效(~表示当前用户)

#### 2.Kafka集群搭建
* 安装Java环境
* 搭建Zookeeper集群
* 搭建Kafka集群
```
Kafka安装的主要流程：
1.解压安装包kafka-2.9.2-0.8.1.1.tgz;
  a).解压安装包： tar -zxvf kafka-2.9.2-0.8.1.1.tgz
2.进入conf目录,编辑配置文件server.properties
3.进入bin目录,启动kafka集群： ./kafka-server-start.sh -daemon ../config/server.properties
  其中 -daemon表示在后台启动
4.验证集群是否搭建成功,参考官方文档：<a href="http://kafka.apache.org/quickstart" target="_blank">Kafka快速入门</a>
```
```
注：
a).Linux是按照从前往后的模式扫描环境变量的值
b).在Linux系统设置定时任务命令：crontab -e (命令含义:编辑一个定时任务)
编辑内容：0 0 * * 0 sh /usr/local/example.sh(要执行的脚本)
其中 0 0 * * 0 表示脚本的执行时间,含义为：分钟 小时 年 月 星期,本例的执行时间为每个星期日的0时0分.
启动定时任务的命令： sh /usr/local/example.sh
```

