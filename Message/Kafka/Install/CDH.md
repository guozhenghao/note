# 通过CDH安装kafka
## 下载所需文件
- 方法1

    打开cdh管理界面，依次点击上方主机-->弹出菜单中的parcel-->界面中kafka后面的下载

    ![parcel下载流程](/res/kafka_install_cdh.png)

    下载完成后，点击分配、激活即可

    *此种方法非常方便，但是对于网速或代理有一定的要求，如果无法下载，使用方法2*

- 方法2

    首先根据自己的cdh版本选择适合自己的kafka版本，查看版本--->[点击此处](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html#pcm_kafka)<---下图有我用的版本

    ![cdh对应的kafka版本](/res/kafka_install_cdh_version.png)

    --->[点击此处](http://archive.cloudera.com/csds/kafka/)<---下载csd包
    
    --->[点击此处](http://archive.cloudera.com/kafka/parcels/)<---下载parcle包 进入对应版本文件夹后，下载自己操作系统所需版本(比如el7对应的是redhat7、centos7)的parcle文件、sha1文件(下载后将后缀改为sha)、json文件  
    
    下载完成后，将csd包上传到指定位置，路径为cdh目录下的/opt/cloudera/csd

    将parcel中三个文件上传到指定位置，路径为cdh目录下的/opt/cloudera/parcel-repo

    然后在cdh管理界面中-->主机(屏幕上方)-->parcel 找到kafka 如果后面不是下载则成功 分配激活即可 如果有问题 则将上述上传文件步骤 重复在所有集群中服务器执行

## 添加kafka服务

进入cdh管理界面，添加服务，选择kafka

需要选择Broker、MirrorMaker、Gateway主机，其中mirror为跨集群同步工具，gateway不清楚，后两者我没有选择，只选择了broker，选择完成后进入下一步，进行一些参数的配置。

只需修改kafka MirrorMaker和Source Brokers List（报红的两条选项）。如果当时没有选择mirror，随便填写任意服务器即可如`example.com:9092`，如果设置了按照如下格式填写`master:9092,slave1:9092,slave2:9092`。Brokers按照当时所选填写节点列表即可，格式同上mirror。

然后就可以启动了，如果启动后报错，错误是OutOfMemoryError，打开cdh管理界面，点击kafka，按照下图设置完成后重启即可

![OutOfMemoryError解决方法](/res/kafka_install_cdh_outofmemoryerror.png)