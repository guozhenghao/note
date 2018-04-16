# MongoDump
## 备份
-h:指明数据库宿主机的IP

-u:指明数据库的用户名

-p:指明数据库的密码

-d:指明数据库的名字

-c:指明collection的名字

-f:指明要导入那些列

简单示例：
````
mongodump -u 用户名 -p 密码 --port 端口 -authenticationDatabase admin -d 数据库名 -c 表名 -o 输出路径
````

## 恢复
-d:指明数据库的名字

-c:指明collection的名字

简单示例：
````
mongorestore -u root -p 密码 --port 端口号 --authenticationDatabase admin -d 库名 恢复文件位置
````