# Java Spring 拼接Demo

- find
````
DBCollection collection = mongoTemplate.getCollection("表名");
// 匹配查询
DBObject matchInfo = new BasicDBObject();
matchInfo.put("name", "张三" );
matchInfo.put("age", BasicDBObject("$gte",25) );

// 匹配显示字段
DBObject projectInfo = new BasicDBObject();
projectInfo.put("_id", 0 );

// find前面参数用于匹配，第二个参数用于是否显示，可填可不填
DBCursor dbo = collection.find(matchInfo,projectInfo).sort(new BasicDBObject("time", -1)).limit(10);
````

- aggregate
````
DBCollection collection = mongoTemplate.getCollection("表名");

// 匹配match
DBObject match = new BasicDBObject();
DBObject matchInfo = new BasicDBObject();
matchInfo.put("cityname", new BasicDBObject("$regex", city));
match.put("$match", matchInfoDetail);

// 匹配group(_id可以group多个，值用一个BasicDBObject然后添加多个)
DBObject groupInfo = new BasicDBObject();
groupInfo.put("_id", "$pointId");
groupInfo.put("avgPageLevel", new BasicDBObject("$avg","$pageLevel"));
DBObject group = new BasicDBObject();
group.put("$group", groupInfo);

// 匹配project
DBObject project = new BasicDBObject();
project.put("$project", new BasicDBObject("_id",0));

// 匹配sort
DBObject sort = new BasicDBObject();
sort.put("$sort", new BasicDBObject("aqi",-1));

// 拼接
List<DBObject> finalInfo = new ArrayList<>();
finalInfo.add(match);
finalInfo.add(group);
finalInfo.add(project);
finalInfo.add(sort);

AggregationOutput result = collection.aggregate(finalInfo);
Iterator<DBObject> resultIterator = result.results().iterator();

````

- and
````
List andInfo = new LinkedList<>();
andInfo.add(new BasicDBObject("date",new BasicDBObject("$gte",strDate)));
andInfo.add(new BasicDBObject("hour",new BasicDBObject("$gte",strHour)));
````

