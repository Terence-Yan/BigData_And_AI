#### 1.write: 给指定用户发信息，以Ctrl+D保存结束
* 语法： write <用户名>

#### 2.wall: 发广播信息(给所有用户发信息)
* 语法： wall [message]

#### 3.ping: 测试网络连通性
* 语法： ping [-c] <IP地址>，-c:指定发送次数

#### 4.ifconfig: 查看和设置网卡信息
* 语法： ifconfig <网卡名称> <IP地址>

#### 5.mail: 查看发送电子邮件，以Ctrl+D保存结束
* 语法： mail <用户名>

#### 6.last: 列出当前与过去登录系统的用户信息
* 语法： last

#### 7.lastlog: 检查特定用户上次的登录时间
* 语法： lastlog [-u] <用户的id>

#### 8.traceroute: 显示数据包到主机间的路径
* 语法： traceroute <目标网站域名>

#### 9.netstat: 显示网络相关信息
* 语法： netstat [参数选项]
```
-t：TCP协议
-u：UDP协议
-l：监听
-r：路由
-n：显示IP地址和端口号
```

#### 10.setup: 配置网络(Redhat系列专有命令，只有root用户拥有此权限)
* 语法： setup

#### 11.mount: 挂载命令
* 语法： mount [-t 文件系统名称] <设备文件名> <挂载点>

#### 12.unmount: 卸载命令
* 语法： unmount <设备文件名>

#### 13.shutdown: 关机命令
```
语法： shutdown [参数选项] <关机时间>
示例： shutdown -h now，马上关机
       shutdown -h 20:30，晚上8点30分关机
-c：取消前一个关机命令
-h：关机
-r：重启
关机或重启之前，应该先关闭掉服务器上的服务
服务器一般只能重启，不能关机
其他关机命令：
halt
poweroff
init 0
其他重启命令：
reboot
init 6
```

#### 14.Linux系统运行级别
* 0：关机
* 1：单用户(类似于Windows的安全模式)
* 2：不完全多用户，不含NFS服务
* 3：完全多用户
* 4：未分配用户
* 5：图形界面
* 6：重启
* 系统运行级别的配置文件：/etc/inittab
* 查看系统运行级别：runlevel








