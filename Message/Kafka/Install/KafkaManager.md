# kafka-manager安装

kafka-manager是yahoo开发的一个kafka管理检测系统，安装所需环境为kafka、java、sbt

## 前期准备

- sbt安装

    首先-->[点击此处](https://www.scala-sbt.org/download.html)<--下载sbt安装包

    上传至服务器然后解压文件  `tar -zxvf xxxxxxxxx.tgz`

    在解压文件夹中创建sbt文本文件,并添加所需信息
        
    ```bash
    cd /sbt
    vim sbt
    # 在文本中添加如下信息
    BT_OPTS="-Xms512M -Xmx1536M -Xss1M -XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=256M"
    java $SBT_OPTS -jar 此处是解压的sbt文件所在位置/sbt/bin/sbt-launch.jar "$@" 
    ```

    修改文件权限  `chmod u+x sbt`

    配置环境变量
    ```bash
    vim ~/.bashrc
    # 在文件尾部添加如下代码后
    export PATH=解压sbt文件位置/sbt/:$PATH
    # 保存退出,然后使其生效
    source ~/.bashrc
    ```

    首次使用sbt时，会下载一些文件，非常非常慢，跟代理无关，只能等着

- 下载manager

    - 自己编译

        下载项目源代码  `git clone https://github.com/yahoo/kafka-manager`
        
        在解压文件目录下运行  `./sbt clean dist`

        执行成功后，会在/target/universal目录下有一个zip的压缩包，将次压缩包放到需要部署的位置解压即可

        因为sbt的原因，可能不好成功
    
    - 直接下载别人编译好的

        我上传了个编译好的版本

        -->[kafka-manager-1.3.3.7.zip](/Message/Kafka/Install/kafka-manager-1.3.3.7.zip)<--
        
        下载后直接在部署位置解压即可

## 配置并运行
    
解压完成后，进入解压目录下的/conf文件夹，修改application.conf文件，将`kafka-manager.zkhost`设置为自己的zk地址

进入解压目录下的/bin文件夹，执行`./kafka-manager`即可启动，默认端口是9000，如果需要更改启动的端口，使用`./kafka-manager -Dhttp.port=9001`启动即可。

如要后台启动，使用nohup即可

启动完成后，在浏览器访问该ip:port，添加一个cluster，hosts填写zk的ip:port，version选择与自己最相近的即可，如果没有JMX不要勾选，然后save即可