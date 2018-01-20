# Docker InfluxDB 安装
- 下载镜像
````
docker pull influxdb
````

- 创建容器

    - 普通创建
    ````
    docker run -d -p 8083:8083 -p8086:8086 --expose 8090 --expose 8099 --name influxdb -tid 镜像id
    ````
    - 挂载配置文件
    ````
    -v 本机文件地址(需提前创建好文件):/etc/influxdb/influxdb.conf
    ````
    - 设置用户名密码
    ````
    //网上的教程全是老的，没法用。查看docker file源码得到如下方法。如果以后失效，去github上influxdb中的docker文件中查看源码对应修改即可。
    
    -e INFLUXDB_ADMIN_USER="用户名" -e INFLUXDB_ADMIN_PASSWORD="密码" -e INFLUXDB_HTTP_AUTH_ENABLED="true"
    ````

- 进入终端
````
docker exec -ti 容器id /usr/bin/influx
````
- 进入容器
````
docker exec -ti 容器id /bin/bash
````
