#### 1.在Eclipse上创建maven项目时，出现本地库jar包无法解析的解决办法
```
Eclipse的Problems窗口下的Errors项会出现如下的jar包无法解析错误提示信息：
Description	Resource	Path	Location	Type
Failure to transfer com.github.stephenc.findbugs:findbugs-annotations:jar:1.3.9-1 
from http://download.java.net/maven/2 was cached in the local repository, resolution will not be reattempted until 
the update interval of java.net repository has elapsed or updates are forced. 
Original error: Could not transfer artifact com.github.stephenc.findbugs:findbugs-annotations:jar:1.3.9-1 from/to 
java.net (http://download.java.net/maven/2/): The operation was cancelled.
...
解决方法：1.首先去本地库的相应目录下查看，应该会存在一些如 _remote.repositories、xxx.lastUpdated、xxx.sha1的文件
2.将上述文件从目录中全部删除，目录下只留下jar文件与相应的pom文件
3.在Eclipse中的Maven菜单栏下点击“Update Project”即可，此时，Errors下面对应的错误应该会消失。
```

#### 2.在Eclipse上创建maven项目时，出现本地库jar包无法读取或无效的解决办法
```
Eclipse的Problems窗口下的Errors项会出现如下的错误提示信息：
Description	Resource	Path	Location	Type
Archive for required library:
'C:/Users/benqwwg/.m2/repository/com/google/guava/guava/12.0.1/guava-12.0.1.jar' in project 'hbase_api_test' 
cannot be read or is not a valid ZIP file	hbase_api_test...
解决方法：1.首先去本地库的相应目录下查看，如果jar文件与pom文件都存在，且不存在其他文件，则直接在Eclipse中将该错误删除即可
```



