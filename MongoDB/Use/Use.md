# MongoDB的基本使用方法
## 插入
- 插入一条数据
````
db.getCollection('test').insert({"name":"张三"})
````
如果数据库中已存在"_id"相同的项，现在需要对该项进行更新，则将insert改为save
## 查询
### find
- 查询所有结果
````
db.getCollection('test').find({})
````
- 按照条件查询
````
db.getCollection('test').find({"name":"张三"})
````
- 大于小于匹配

符号 | 匹配符
----|------
大于 | $gt
小于 | $lt
大于等于 | $gte 
小于等于 | $lte
````
db.getCollection('test').find({"age":{"$gt":20}})  
````
- 使用正则进行匹配
````
 db.getCollection('test').find({"name":{"$regex":"张三"}})
````
- 设置查询时需要显示或隐藏的字段（0为隐藏，1为显示）
````
db.getCollection('test').find({},{"_id":0,"name":1})
````
- 对结果排序（1为升序，-1为降序）
````
db.getCollection('test').find().sort({"name":1})
````
- 设置查询结果数量
````
db.getCollection('test').find().limit(10)
````
- distinct操作
````
db.getCollection('test').distinct("name")
````

### aggregate
- 查询所有结果
````
db.getCollection('test').aggregate()
````
- 按条件查询
````
db.getCollection('test').aggregate([
    {"$match":{"name":"张三"}}
])
````
- group分组

功能 | 匹配符
----|------
筛选 | $match
分组 | $group
求和 | $sum
求平均 | $avg
显示隐藏字段 | $project
排序 | $sort
设置查询数量 | $limit
[*更多查看mongodo-pipeline官网文档*](https://docs.mongodb.com/manual/reference/operator/aggregation-pipeline/)
````
db.getCollection('test').aggregate([
    {"$match":{"name":"张三","age":{"$lt":50}}},
    {"$group":{"_id":"$sex","sumAge":{"$sum":"$age"},"avgAge":{"$avg":"age"}}},
    {"$sort":{"sumAge":1}},
    {"$limit":10}
])
````
## 删除
- 删除
````
db.getCollection('test').remove({"name":"张三"})
````
## 索引
- 查看创建索引
````
db.test.getIndexes()
````
- 插入索引
````
db.test.ensureIndex({"name":1})
db.test.createIndex({"name":1})
````
*（可创建复合索引，e.g.{"name":1,"age":-1}）*
- 删除索引
````
db.test.dropIndex({"name":1})
````

