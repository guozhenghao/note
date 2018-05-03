# tippecanoe
## 安装
```shell
git clone git@github.com:mapbox/tippecanoe.git
cd tippecanoe
make -j
make install
```
## 使用
负责将geojson数据转换成mbtiles文件

- 使用命令：

````
tippecanoe -z 15 -Z 0 -ps -Bg -o out.mbtiles input.geojson
````

- 参数说明

参数 | 作用
----|------
-z | 生成数据最大层级
-Z | 生成数据最小层级
-ps | 不要简化线条和多边形 
-Bg | 自动抽稀，避免单个瓦片要素过多
-o | 输出文件路径

- 示例
````
tippecanoe -z 17 -Z 6 -o river.mbtiles river.geojson
````

[*更多参数和使用方法详见github*](https://github.com/mapbox/tippecanoe)