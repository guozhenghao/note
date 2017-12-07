# MongoDB更改字段名
### $rename
- update和updateMany
````
db.getCollection('表名字').updateMany({"此处相当于find，不填就是所有"},{"$rename":{"老字段名":"新字段名"}})
````
使用update的时候尽管find处为空是匹配所有，但是只更改一个，使用updateMany后所有更改。