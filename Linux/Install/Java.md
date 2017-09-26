# Linux下的Java安装
## 下载
首先在服务器上运行一下更新

`yum update`

然后在系统上搜索，任何版本的已安装的JDK组件

`rpm -qa | grep -E '^open[jre|jdk]|j[re|dk]'`

如果之前已经安装了JAVA1.6或其他的版本，执行下列命令，将他们卸载。

`yum remove java-1.xxxxxxx.xxxxxxxxxxx`

到http://www.oracle.com/technetwork/java/javase/downloads/index.html下载与自己匹配的rmp。

或者使用wget命令

`wget xxxxxxxxx下载地址`

## 安装 

安装

`rpm -ivh 文件`

查看安装版本

`java -version`

## 设置全局变量

````
export JAVA_HOME=/usr/java/jdk1.8.0_25/
export PATH=$PATH:$JAVA_HOME
````

安装路径在机器重启后会消失，所以需要把安装目录保存到系统高配置文件中。

在/etc/profile.d/下创建一个叫做java.sh 的文件

````
#!/bin/bash
JAVA_HOME=/usr/java/jdk1.8.0_25/
PATH=$JAVA_HOME/bin:$PATH
export PATH JAVA_HOME
export CLASSPATH=.
````

改变权限

`chmod +x /etc/profile.d/java.sh`

使刚才改变的环境变量永久有效运行

`source /etc/profile.d/java.sh`