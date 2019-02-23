#### 1.Linux服务器和window互传文件工具——lrzsz
```
1.使用注意事项：lrzsz这个工具只适合传输小文件，不适合传输大型文件
2.安装： yum install lrzsz -y
3.常用命令：(安装成功后,以后使用可直接在linux命令行输入)
  sz 选定的文件名：将选定的文件发送（send）到本地机器  
  rz：运行该命令会弹出一个文件选择窗口，从本地选择文件上传到服务器
```

#### 2.Linux系统配置环境变量的两种方式
* 在/etc/profile文件中配置,此种配置对所有用户有效
* 在~/.bashrc文件中配置,此种配置只对当前用户有效(~表示当前用户)

#### 3.Linux系统修改时区命令(Centos)
```
a).timedatectl:查看系统时间方面的详情
b).timedatectl list-timezones:列出所有时区
c).timedatectl set-timezone Asia/Shanghai:设置系统时区为上海
注：在对计算机系统进行时间同步操作时,应首先查看其时区信息.
```
