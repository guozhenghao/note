# tileserver-gl-light

负责mbtiles数据发布。tileserver-gl依赖较多的本地库文件，不容易编译安装，tileserver-gl-light是tileserver-gl的缩减版，去除了本地库依赖，可以通过npm直接安装。

安装命令: `npm install -g tileserver-gl-light`

- 使用命令：

````
tileserver-gl-light [mbtiles]文件 [option]
````

- 参数说明

参数 | 作用
----|------
-p | 指定端口号，默认8080
-c | 指定配置文件，配置文件是json格式文件

[*配置文件的具体参数和写法详见官方链接*](http://tileserver.readthedocs.io/en/latest/config.html)

- 示例
````
tileserver-gl-light river.mbtiles -p 3000
tileserver-gl-light -c config.json -p 3000
````

启动完成后可到l浏览器打开*ip:发布端口*进行预览 