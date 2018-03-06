# Docker更改时区

- 首先进入该实例
````
docker exec -it 实例id /bin/bash
````

- 使用`date`查看时间，`date -R`查看时区

- 若不对，使用下方命令，进入指定名目
````
 cd /usr/share/zoneinfo/Asia
````

- 复制一个与本地时间相同地区文件复制到系统的localtime
````
cp Shanghai /etc/localtime
````

- 可以使用date相关命令查看效果，在更改之后如果实例时数据库的，数据库的时间也就做了相应更改