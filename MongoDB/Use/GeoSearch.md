# MongoDB空间查询
- 所匹配字段为list类型，且该字段需要添加索引
````
db.表名.createIndex({"字段名":"2dsphere"})
````
## **附近**

- 查询语句
````
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

````

## **点在面内**

- 查询语句 *（边界首尾两点需一样，形成闭合的面）*
````
db.getCollection('表名').find({
    "字段名":
    {$geoWithin:
        {$geometry:
            {type:"Polygon",
              coordinates:[[
                [116.123,39.132],
                [116.456,39.456],
                [116.789,39.789],
                [116.123,39.132],
                ]]
            }
         }
    }
})
````
## **周边查询在聚合中可以进行更多处理**

- 查询语句 
````
db.getCollection('表名').aggregate([
    {$geoNear:
        {near:
            {type:"Point",coordinates:[116.123,39.132] },
            distanceField:"字段名",
            $minDistance:0,
            $maxDistance:500,
            $num:5（相当于limit）,
            spherical:true
         }
    },
    {match、group等操作}
])

````