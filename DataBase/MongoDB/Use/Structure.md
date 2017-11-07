# MongoDB的返回结构改变
*该方法在MongoDB 3.4版本以后才有*

- 该方法用于aggregate中，使用的关键字是$project，该关键字不光能选择是否返回某字段，还可以改变返回的数据结构

- 使用方法

例如返回值原结果如下：
````
{ "_id" : ObjectId("55ad167f320c6be244eb3b95"), "x" : 1, "y" : 1 }
````
使用语句：
````
db.collection.aggregate( [ { $project: { myArray: [ "$x", "$y" ] } } ] )
````
结果：
````
{ "_id" : ObjectId("55ad167f320c6be244eb3b95"), "myArray" : [ 1, 1 ] }
````