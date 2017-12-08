# Docker安装MongoDB
- 查看镜像版本等，可以查看:  [阿里镜像网址](https://cr.console.aliyun.com/#/imageSearch)
````
docker pull mongo:版本号，不写冒号安装最新
````

## 创建实例
- 查看下载的镜像
````
docker images
````
    
如其中有这样一个mongo镜像:
````
REPOSITORY                                       TAG                 IMAGE ID            CREATED             SIZE
docker.io/mongo                                  3.4.10              d22888af0ce0        4 weeks ago         360.9 MB

````

- 创建实例
````
docker run -p 27017:27017 --name 实例名 -tid 刚才查询的IMAGE_ID(d22888af0ce0) 
````
- 如需挂在到容器可以加入如下指令：
````
-v 本机地址:容器地址
````

## 注意
创建的mongodb是没有登录验证的，如果需要登录验证，需要如下操作。

- 创建实例时后面加"--auth"
````
docker run -p 27017:27017 --name 实例名 -tid 刚才查询的IMAGE_ID(d22888af0ce0) --auth
````

- 然后以admin的身份进入数据库
````
docker exec -it 实例ID(docker ps查看) mongo admin
````
- 进入后输入
````
db.createUser({user:"用户名",pwd:"密码",roles:["root"]})
````
- 输入"exit"退出即可
