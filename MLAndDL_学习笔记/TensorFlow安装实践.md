#### 1.tensorflow的安装
```
安装环境：Anaconda3
(1).打开Anaconda Prompt;
(2).在命令行中依次输入下面两个命令：(这两行命令是用来修改连接清华镜像的)
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --set show_channel_urls yes
    安装Tensorflow时，需要从Anaconda仓库中下载，一般默认链接的都是国外镜像地址，
    下载速度较慢，这时可以使用国内清华镜像，需要修改链接镜像的地址.
(3).安装tensorflow：conda create -n tensorflow python=3.5.2
(4).若在命令行界面输出提示信息的最后几行，看到如下的信息，则表明tensorflow已经安装成功：
    """
    ......
    # To activate this environment， use:
    # > activate tensorflow
    # To deactivate this environment， use:
    # > deactivate tensorflow
    ......
    """
(5).在命令行输入：activate tensorflow，此时，新的命令行应该类似如下：
    (tensorflow) C:\Users\tom>
(6).如果要安装的是CPU版本，那么在命令行继续输入命令，然后等待安装完成：
    pip install -I https://mirrors.tuna.tsinghua.edu.cn/tensorflow/windows/cpu/tensorflow-1.1.0-cp35-cp35m-win_amd64.whl
    注意，选择安装的版本的python版本要与安装tensorflow时指定的一致：对于本例，“...-cp35-...” 与 “python=3.5.2”是一致的
(7).验证tensorflow安装成功
    在命令行输入 python，进入python交互界面；
    依次执行如下代码： >>> import tensorflow as tf
                     >>> session = tf.Session()
                     >>> a = tf.constant(1)
                     >>> b = tf.constant(2)
                     >>> session.run(a+b)
    若输出结果“3”，则tensorflow安装成功。
```
