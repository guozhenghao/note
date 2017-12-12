# redis设置密码方式
### 配置文件中更改

### 数据库中更改
进入到数据库后
- 查看配置文件中的密码
````
config get requirepass
````
- 若密码为空，结果如下

````
1) "requirepass"
2) (nil)
````
- 设置密码
````
 config set requirepass 密码
````