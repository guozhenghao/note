# Spring Boot 三层结构样例
### Dao层
````
import org.bson.types.ObjectId;
import org.springframework.stereotype.Repository;
import com.mongodb.DBObject;

@Repository
public interface TestDao {
     Map<String,List<Map<String,String>>> get( String test);
}

````
### Server层
````
import org.apache.catalina.connector.OutputBuffer;
import org.bson.types.ObjectId;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationListener;
import org.springframework.context.event.ContextRefreshedEvent;
import org.springframework.data.domain.Sort;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query ;
import org.springframework.stereotype.Repository;
import com.fasterxml.jackson.databind.introspect.BasicClassIntrospector;

@Repository
public class TestServices implements TestDao {

      @Override
      public Map<String, List<Map<String,String>>> get(String test) {
           return null ;
     }

}


````
### Controller层
````
import org.bson.types.ObjectId;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@ResponseBody
@RequestMapping (value = "test" )
public class TestController {

     @RequestMapping(value = "get" )
    public ResultMap get( String test ) {
          return null;
    }

}
````