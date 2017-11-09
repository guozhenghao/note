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

- near(find)
````
DBObject geometryInfo = new BasicDBObject();
geometryInfo.put("type", "Point");
geometryInfo.put("coordinates",
						new double[] { 中心点lon, 中心点lat });
DBObject nearInfo = new BasicDBObject();
nearInfo.put("$geometry", geometryInfo);
nearInfo.put("$minDistance", 0);
nearInfo.put("$maxDistance", 半径);
DBObject near = new BasicDBObject("$near", nearInfo);
DBObject finalSearchInfo = new BasicDBObject();
finalSearchInfo.put("字段名", near);
````

- polygon(find)
````
List<List<List<Double>>> list1 = new LinkedList<List<List<Double>>>();
List<List<Double>> list2 = new LinkedList<List<Double>>();
// 将面的边界点，以list的形式，添加到面的list中
for (int i = 0; i < polygon.length; i = i + 2) {
	List<Double> list3 = new LinkedList<Double>();
	list3.add(点lon);
	list3.add(点lat);
	list2.add(list3);
}
// 第一个点再添加一遍，形成闭环
List<Double> list3 = new LinkedList<Double>();
list3.add(第一个点lon);
list3.add(第一个点lat);
list2.add(list3);
list1.add(list2);
// 拼接
DBObject geometryInfo = new BasicDBObject();
geometryInfo.put("type", "Polygon");
geometryInfo.put("coordinates", list1);
DBObject geometry = new BasicDBObject("$geometry", geometryInfo);
DBObject geoWithin = new BasicDBObject("$geoWithin", geometry);
DBObject finalSearchInfo = new BasicDBObject();
finalSearchInfo.put("字段名", geoWithin);
````

- near(aggregate)
````
List _list = new LinkedList<>();
_list.add(中心点lon);
_list.add(中心点lat);
DBObject near = new BasicDBObject();
near.put("type", "Point");
near.put("coordinates",_list);
DBObject geoNearInfo = new BasicDBObject();
geoNearInfo.put("near", near);
geoNearInfo.put("distanceField", "字段");
// 相当于match
geoNearInfo.put("query", new BasicDBObject("name","张三"));
geoNearInfo.put("minDistance", 0);
geoNearInfo.put("maxDistance", 半径);
geoNearInfo.put("spherical", true);
geoNear.put("$geoNear", geoNearInfo);

````
