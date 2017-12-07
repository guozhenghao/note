# Docker下载镜像并创建实例
## 下载镜像
- 查看镜像版本等，可以查看:  [阿里镜像网址](https://cr.console.aliyun.com/#/imageSearch)
````
// 以下载mongo3.4.10版本为例
docker pull mongo:3.4.10
````

## 创建实例
- 查看下载的镜像
````
docker images
````
    
如其中有这样一个mysql镜像:
````
REPOSITORY                                       TAG                 IMAGE ID            CREATED             SIZE
hub.c.163.com/library/mysql                      5.7                 9e64176cd8a2        7 months ago        407.1 MB

````

- 创建实例
````
docker run -p 3306:3306 --name 实例名 -e MYSQL_ROOT_PASSWORD=密码 -tid 刚才查询的IMAGE_ID(9e64176cd8a2) 
````
- 如需挂在到容器可以加入如下指令：
````
-v $PWD/conf/my.cnf:/etc/mysql/my.cnf -v $PWD/logs:/logs -v $PWD/data:/mysql_data
````

3306:3306 一个是外网端口一个是容器端口，没弄明白，先留坑

