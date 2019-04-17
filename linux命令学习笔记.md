#### 1.立即进行关机： shutdown -h now

#### 2.重新启动： shutdown -r now/reboot

#### 3.注销当前用户： logout

#### 4.添加用户：useradd <user_name>(只有root用户具有该权限)

#### 5.给指定用户修改密码：passwd <user_name>(只有root用户具有该权限)

#### 6.删除用户：userdel <user_name>(只有root用户具有该权限)

#### 7.删除用户及其主目录：userdel -r <user_name>(只有root用户具有该权限)

#### 8.显示当前工作目录：pwd

#### 9.进入指定目录：cd <dir_name> (可以是绝对路径，也可以是以当前目录为根的相对路径)

#### 10.列出文件和目录：ls
* ls -a   显示隐藏文件
* ls -l   显示长列表格式

#### 11.建立目录：mkdir <dir_name>

#### 12.删除空目录：rmdir <dir_name>

#### 13.建立空文件：touch <dir_name>

#### 14.复制命令：cp

#### 15.移动文件或修改文件名：mv

#### 16.显示文件内容，带分页：more <dir_name>

#### 17.显示文件内容，带分页：less

#### 18.在文本中查询内容：grep -n <search_context> <dir_name>
* -n: 显示查找内容所在行号
* <search_context>: 要查找的内容
* <dir_name>： 目标文件(在该文件下查找)

#### 19.管道命令符：|，将左侧命令的处理结果传递给右侧命令继续处理

