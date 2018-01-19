# InfluxDB 基本操作
### 库相关
- 创建数据库
````
create database "数据库名"
````
- 显示所有数据库
    - 命令行
    ````
    show databases
    ````
    - http
    ````
    (GET)http://ip:port/query?q=show databases、
    //如果有密码 u=用户名&p=密码
    ````
- 删除数据库 
````
drop database "数据库名"
````
- 使用某数据库
````
user 数据库名
````
### 数据保存策略(Retention Policies)
- 查询数据库的策略
````
show retention policies on 数据库名
````
- 创建新的策略
````
create retention policy 策略名 on 库名 duration 3w replication 1 default

//3w：保存3周，3周之前的数据将被删除，h(小时)，d(天)，w(星期)
//replication 1：副本个数，一般为1就可以了
//default：设置为默认策略
````
- 修改策略模式
````
alter retention policy 策略名 on 库名 duration 30d default  
````
- 删除策略模式
````
drop retention policy 策略名
````
### 用户管理
- docker容器创建时
    #### 这块大坑！！网上的教程全是老的，没法用。查看docker file源码得到如下方法。如果以后失效，去github上influxdb中的docker文件中查看源码对应修改即可。
````
docker run -d -p 8083:8083 -p 8086:8086 -p 2015:2015 -e INFLUXDB_ADMIN_USER="用户名" -e INFLUXDB_ADMIN_PASSWORD="密码" -e INFLUXDB_HTTP_AUTH_ENABLED="true" --name 容器名 镜像id
````
- 容器已经创建后修改
````
//创建
CREATE USER admin WITH PASSWORD 'password'
//赋权
GRANT ALL PRIVILEGES TO admin
//打开认证
vi /etc/influxdb/influxdb.conf
[http]标签下auth-enables改为true
````
- 修改密码
````
SET PASSWORD FOR 用户名 = '新密码'
````
### 表相关
- 显示该数据库中所有表
````
show measurements
````
- 删除表
````
drop measurement "表名"
````
- 增加数据(没有新建表的语句，插入时直接建表)
    
    - 命令行
    ````
    insert testTable,name=gzh,sex=male value=4,count=2 1435362189575692182

    //testTable是表名
    //name、sex是tag
    //value、count是field
    //1435362189575692182是自己设置时间戳
    ````
    - http
    ````
    (POST)http://ip:port/write?db=gzh  (body)testTable,name=gzh,sex=male value=4,count=2
    ````
- 查询数据

    - 命令行
    ````
    select * from qqq order by time desc limit 3  
    ````
    - http
    ````
    (GET)http://ip:port/query?db=gzh&q=select * from qqq order by time desc limit 3 
    //如果有密码 u=用户名&p=密码

    //返回结果中除了表明外，有两个数据，columns是表头，values是数据
    ````

### 连续查询(Continous Queries)

当数据超过保存策略里指定的时间之后就会被删除，但是这时候可能并不想数据被完全删掉，influxdb提供了连续查询，可以做数据统计。数据库中自动定时启动的一组语句，语句中必须包含 SELECT 关键词和 GROUP BY time() 关键词采样

- 查看数据库的连续查询
````
show continuous queries  
````
- 创建新的连续查询
````
create continuous query 连续查询的名字 on 数据库名 begin select count(*) into 新的表名 from 当前表名 group by time(30m) end

//30m：时间间隔为30分钟
//如果是不同的database的话，写成库名.表名
````
- 删除连续查询
````
drop continous query 连续查询名字 on 库名 
````
