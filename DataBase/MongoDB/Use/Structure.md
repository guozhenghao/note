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

- project 还可用于字符串的连接

````
db.collection.aggregate( [ { $project: { "合并后的名字":{ $concat: [ "$字段名", "123456", "$字段名" ] } } } ] )
````

- project 还可用于字符串的拆分

````
db.collection.aggregate( [ { $project: { "拆分后的名字":{ $substr: [ "$字段名", 开始下标, 长度 ] } } } ] )
````

- split

````
db.collection.aggregate( [ { $project: { "拆分后的名字":{  $split: ["$city", ", "]  } } } ] )
````

