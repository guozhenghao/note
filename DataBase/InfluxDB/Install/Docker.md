# Docker InfluxDB 安装
- 下载镜像
````
docker pull influxdb
````

- 创建容器
````
docker run -d -p 8083:8083 -p8086:8086 --expose 8090 --expose 8099 --name influxdb -tid 镜像id
````

- 进入终端
````
docker exec -ti 容器id /usr/bin/influx
````