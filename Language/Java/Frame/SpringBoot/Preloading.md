# Spring Boot 启动时预加载
- 预加载mongodb中数据的方法样例
```java
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.core.annotation.Order;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.stereotype.Component;

import com.mongodb.BasicDBObject;
import com.mongodb.DBCollection;
import com.mongodb.DBCursor;
import com.mongodb.DBObject;

@Component
@Order (value = 1)
public class MyStartupRunner implements CommandLineRunner {

      public static Map<String, String[]> city = new HashMap<>();


      @Autowired
      private MongoTemplate mongoTemplate ;

      @Override
      public void run(String... args ) throws Exception {
          DBCollection collection = mongoTemplate .getCollection("new_city" );
          DBObject findInfo = new BasicDBObject();
           findInfo.put( "border" , 0);
           findInfo.put( "coordinates" , 0);
          List<DBObject> cursor = collection .find(new BasicDBObject(),findInfo ).toArray();
           for (DBObject d : cursor ) {
               HashMap map =  ( HashMap) d .get("location" );
               double lon = Double.parseDouble( map.get( "lon").toString());
               double lat = Double.parseDouble( map.get( "lat").toString());
               city .put(d .get("code" ).toString(),
                         new String[] { toString().valueOf (lon ), toString(). valueOf( lat), d .get("name" ).toString() });
          }
     }
}

```