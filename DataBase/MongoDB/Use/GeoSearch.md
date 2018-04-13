# MongoDB空间查询
## 前期准备
#### 数据格式
mongo对于空间字段有特殊的结构要求，结构如下:
```json
{
	"字段名": {
		"type": "geo类型",
		"coordinates": "根据不同类型建立的list"
	}
}
```
常用的空间类型简单样例：
- point

    点类型的数据部分为一个list，分别为lon和lat
    ```json
    {
        "字段名": {
            "type": "Point",
            "coordinates": [1,2]
        }
    }
    ```
    点类型的结构mongo做了特殊处理，可以直接按照如下形式建立:
    ```json
    {
        "字段名": [116.39]
    }
    ```
- multi_point

    点类型的数据部分为一个list，分别为lon和lat
    ```json
    {
        "字段名": {
            "type": "MultiPoint",
            "coordinates": [
                [ 1, 1 ],
                [ 2, 2 ],
                [ 3, 3 ]
            ]
        }
    }
    ```
- line

    直线类型的数据部分为一个list，这个list里套了两个list，分别为直线两个端点的lon和lat
    ```json
    {
        "字段名": {
            "type": "LineString",
            "coordinates": [ [ 1, 1 ], [2, 2 ] ]
        }
    }
    ```
- multi_line

    多直线类型的数据部分为一个list，这个list中的所有项为line的结构
    ```json
    {
        "字段名": {
            "type": "MultiLineString",
            "coordinates": [
                [ [ 1, 1 ], [2, 2 ] ],
                [ [ 2, 2 ], [3, 3 ] ],
                [ [ 3, 3 ], [4, 4 ] ],
            ]
        }
    }
    ```
- polygon

    面类型的数据部分为一个list，里面又有一个list，这个list里是边界点

    **特别注意的是，首末点要相同，用于首尾相连成一个封闭面**
    ```json
    {
        "字段名": {
            "type": "Polygon",
            "coordinates": [ 
                [ [ 0, 0 ] , [ 1, 1 ] , [ 1, 3 ] , [ 0, 0  ] ] 
            ]
        }
    }
    ```
- multi_polygon

    多面类型的数据部分为一个list，里面为多个polygon

    ```json
    {
        "字段名": {
            "type": "MultiPolygon",
            "coordinates": [ 
                [ [ [ 0, 0 ], [ 1, 1 ], [ 1, 3 ], [ 0, 0 ] ] ],
                [ [ [ 2, 2 ], [ 3, 9 ], [ 3, 1 ], [ 2, 2 ] ] ]
            ]
        }
    }
    ```
#### 添加索引
根据所需的查询操作等，选择所需的索引

`db.表名.createIndex({"字段名":"2dsphere"})`

`db.表名.createIndex({"字段名":"2d"})`

## 使用
**所有find中的操作，也可以放到聚合函数中做match**
- 附近(圆)
    ```json
    db.getCollection('表明').find({
        "字段名":
        {$near:
            {
                $geometry:{type:"Point", coordinates:[116.079763622,39.761223]},
                $minDistance:0,
                $maxDistance:500
            }
        }
    })
    ```

- 点在面内

    **注意查询语句中设定的面也要首尾相连**
    ```json
    db.getCollection('表名').find({
        "字段名":
        {$geoWithin:
            {$geometry:
                {type:"Polygon",
                 coordinates:[
                    [[116.123,39.132],[116.456,39.456],[116.789,39.789],[116.123,39.132]]
                 ]
                }
            }
        }
    })
    ````
- 周边查询(聚合函数)
    ```json
    db.getCollection('表名').aggregate([
    {"$geoNear" : 
            {"near" : 
                { "type" : "Point" , "coordinates" : [ 116.156387329102, 40.1573604627098]} , 
            "distanceField" : "字段名" , 
            "query" : { "date" : "20171025","hour":"16"}, 
            "minDistance" : 0, 
            "maxDistance" : 10,
            "num" : 2 , 
            "spherical" : true
           }
        }
    ])
    ```
    *query相当于match*
