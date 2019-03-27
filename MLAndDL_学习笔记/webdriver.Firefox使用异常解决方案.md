#### 1."WebDriverException: Message: 'geckodriver' executable needs to be in PATH."异常
```
问题背景：Windows8环境，在jupyter中执行python代码
解决方案：1.网上提供的解决办法是，下载geckodriver，解压得到geckodriver.exe，然后将此文件所在路径配置到系统环境里的Path变量里面。
           按此方法尝试之后，不行。
         2.根据异常提示信息，将解压的geckodriver.exe放在正在执行的ipynb文件的父目录下，即python代码的当前路径，异常成功解决。
```

#### 2."WebDriverException: Message: newSession"异常
```
问题背景：Windows8环境，在jupyter中执行python代码
异常原因：geckodriver版本低，不匹配
解决方案：下载高版本的geckodriver(本例采用的是"geckodriver-v0.23.0-win64")，解压替换原来的geckodriver即可。
```

