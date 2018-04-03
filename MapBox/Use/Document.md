# MapBox矢量数据生成配图流程
### 切图
使用tippecanoe将需要的geojson文件按照需要的层级转换为mbtiles文件。

转换完成后可以通过`tile-join -n 重命名.mbtiles -o 输出文件名.mbtiles *.mbtiles`将多个mbtiles文件合并。

### 将文件发布，然后通过maputnik配置风格
- 样例
```json
{
  "version": 8,
  "name": "北京",
  "metadata": {
    "mapbox:autocomposite": true,
    "mapbox:type": "default"
  },
   "center":[
    116.29,
    40.16
  ],
  "zoom":7.82,
  "bearing":0,
  "pitch":0,
  "sprite":"./styles/sprite",
  "glyphs": "./fonts/{fontstack}/{range}.pbf",
  "sources": {
    "beijing": {
      "type": "vector",
      "tiles": [
        "http://ip/data/beijing_mix/{z}/{x}/{y}.pbf"
      ],
      "minZoom": 0,
      "maxZoom": 14
    }
  },
  "layers": [
   
    {
      "id": "bj_river-copy",
      "type": "fill",
      "source": "beijing",
      "source-layer": "rivergeojson",
      "minzoom": 10,
      "maxzoom": 14,
      "layout": {
        "visibility": "visible"
      },
      "paint": {
        "fill-color": "rgba(41, 49, 74, 1)"
      }
    }
  ],
  "id": "zi2b8azv1",
  "owner": ""
}
```
### 将配置好的风格发布
- config样例
```json
{
  "options": {
    "paths": {
      "root": "/root/tileserver",
      "fonts": "fonts",
      "styles": "styles",
      "mbtiles": "data"
    },
    "domains": [
      "ip:端口"
    ],
    "maxSize": 2048,
    "pbfAlias": "pbf",
    "serveAllFonts": false
  },
  "styles": {
  	"beijing":{
  		"style":"test.json",
      "tilejson":{
        "type":"overlay"
      }

    }
  },
  "data": {
    "beijing_mix": {
      "mbtiles": "beijing_mix.mbtiles"
    }
  }
}
```
