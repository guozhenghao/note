# Kafka普通服务器环境安装

## 文件下载

-->[文件下载地址](http://kafka.apache.org/downloads)<--

选择自需要的版本，下载binary的，source需要自己编译。

## 部署

将下载的文件上传至服务器，然后进行解压

然后进入解压文件夹中的config目录，修改server.properties配置文件，修改内容如下：
```bash
#集群中每个id递增
broker.id=0
#ip填写本机地址
listeners=PLAINTEXT://本机ip:9092
#如需外网使用，设置此项，ip为外网ip，端口可以与刚才不同
advertised.listeners=PLAINTEXT://外网ip:9092
#填写zk集群地址，多个使用逗号隔开
zookeeper.connect=zk1ip:2181
#其他设置项可不更改，深入设置查看官网详细参数介绍
```

然后进入解压文件夹中的bin目录，执行下方语句启动kafka:

`./kafka-server-start.sh ../config/server.properties`

后台启动使用nohup即可

如果有问题，查看报错信息对应修改

## 简单测试

在解压文件的bin目录下执行如下代码：

- 创建生产者

    `./kafka-console-producer.sh --broker-list 本机ip:9092 --topic testkafka`
- 创建消费者

    `./kafka-console-consumer.sh --zookeeper 本机ip:2181 --topic testkafka`

能够正常收发消息则成功