# Docker常规命令
- 下载镜像
````
docker pull 镜像名:版本号(可以只写镜像名，下载最新版本)
````
- 查看下载镜像
````
docker images
````
- 创建实例
````
docker run -p 端口:端口 --name 实例名 -tid 镜像id -v 本机目录:实例中目录
````
- 查看实例
````
docker ps -a(可以查看未运行的实例)
````
- 启动、停止、删除实例
````
docker start/stop/rm 实例id
````
- 重启docker
````
sudo systemctl restart docker
````
- 查看容器挂载信息
````
docker inspect 容器名 | grep Mounts -A 20
````
- 查看docker指定容器日志
````
docker logs -f -t --since="2017-05-31" --tail=10 容器名
````