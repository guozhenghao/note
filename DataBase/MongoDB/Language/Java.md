# Java操作MongoDB
## maven + spring boot 项目
### 前期准备
- 导入包：
````
< dependency>
     < groupId> org.springframework.boot </groupId >
     < artifactId> spring-boot-starter-data-mongodb </ artifactId>
</ dependency>
````
- application.yml中添加数据库连接信息
````
spring:
  data:
    mongodb:
      host: 123.123.123.123
      port: 27017
      repositories:
        enabled: true
      database: test
      username : root
      password: 123123
      authentication-database: admin
server:
  port: 8181
````
### 使用
- 使用时先自动装载MongoTemplate
````
@Autowired
private MongoTemplate mongoTemplate;
````
- find查询 *（创建BasicDBObject对象，然后按照mongodb的语句进行拼接）*

e.g.
````
DBCollection collection = mongoTemplate .getCollection("test" );
//匹配find信息
DBObject findInfo = new BasicDBObject("name" ,"张三");
//下面是两种查询方法：1.返回所有 2.返回一个
//查询所有结果
DBCursor dbc = collection.find(findInfo);
//查询一个
DBObject dbo = collection.findOne(findInfo);
//对查询结果进行操作
while(dbc.hasNext()){
     //...
}
````

- aggregate查询 *（方法同上，最后将每个管道加入到一个list中）*

e.g.
````
DBCollection collection = mongoTemplate .getCollection("test" );
//用于匹配match管道
DBObject match = new BasicDBObject();
DBObject matchInfo = new BasicDBObject("name" ,"张三" );
match.put( "$match" ,matchInfo );
//将需要的管道加入到list中
List<DBObject> list = new ArrayList<>();
list.add( match);
//查询
AggregationOutput output = collection .aggregate(list );
//对查询结果进行操作
Iterator <DBObject> iterator = output .results().iterator();
while (iterator .hasNext()){
      DBObject dbo = iterator.next();
      //...
}
````
## 普通Java项目
### 前期准备
- 导入包

1. mongodb-drive-0.0.0.jar

2. mongodb-drive-core-0.0.0.jar

- 创建一个数据库工具类，用于连接数据库等操作 *（网上全是）*

e.g.

````
import com.mongodb.BasicDBObject;
import com.mongodb.MongoClient;
import com.mongodb.MongoClientOptions;
import com.mongodb.MongoCredential;
import com.mongodb.ServerAddress;
import com.mongodb.MongoClientOptions.Builder ;
import com.mongodb.WriteConcern;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoCursor;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.MongoIterable;
import com.mongodb.client.model.Filters;
import com.mongodb.client.result.DeleteResult;

public enum MongoDBUtil {

      instance;

      private MongoClient mongoClient ;

      static {
          String ip = "123.123.123.123"
           int port = 27017;
          ServerAddress serverAddress = new ServerAddress(ip ,port );
          List<ServerAddress> addrs = new ArrayList<ServerAddress>();
           addrs.add( serverAddress );
          MongoCredential credential = MongoCredential.createScramSha1Credential( "用户名" , "admin" , "密码" .toCharArray());
          List<MongoCredential> credentials = new ArrayList<MongoCredential>();
           credentials .add(credential );
           instance .mongoClient = new MongoClient( addrs, credentials );
           Builder options = new MongoClientOptions.Builder();
           options.connectionsPerHost(300);
           options.connectTimeout(15000);
           options.maxWaitTime(5000);
           options.socketTimeout(0);
           options.threadsAllowedToBlockForConnectionMultiplier(5000);
           options.writeConcern(WriteConcern. SAFE );
           options.build();
     }

      public MongoDatabase getDB(String dbName) {
           if (dbName != null && ! "".equals( dbName)) {
              MongoDatabase database = mongoClient .getDatabase(dbName );
               return database ;
          }
           return null ;
     }

      public MongoCollection<Document> getCollection(String dbName , String collName) {
           if (null == collName || "" .equals(collName )) {
               return null ;
          }
           if (null == dbName || "" .equals(dbName )) {
               return null ;
          }
          MongoCollection<Document> collection = mongoClient .getDatabase(dbName ).getCollection( collName);
           return collection ;
     }
}
````
### 使用
- 连接数据库
````
MongoCollection collection = MongoDBUtil.instance .getCollection( "数据库名", "表名" );
````
- 普通java项目查询的方法与spring差不多，但是查询的结果都是以迭代器的方式返回
````
//distinct
DistinctIterable distinctExample = collection.distinct( "name" , String.class );
//find
FindIterable findExample= collection.find(new BasicDBObject("name" ,"张三"));
//对于查询的结果都需要
Iterator xxIterator = xxExample.iterator();
然后通过hasNext()、next()方法进行操作
````