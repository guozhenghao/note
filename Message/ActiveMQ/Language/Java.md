# Java操作ActiveMQ
## 前期准备
- 导入包：

  1.maven项目
```xml
<!-- https://mvnrepository.com/artifact/org.apache.activemq/activemq-all -->
<dependency>
	<groupId>org.apache.activemq</groupId>
	<artifactId>activemq-all</artifactId>
	<version>5.15.0</version>
</dependency>
```
  2.普通Java项目

  activemq-all-5.15.0.jar

## 创建相关类
- ActiveMqUtil
```java
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;
import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.JMSException;
import javax.jms.Session;
import org.apache.activemq.ActiveMQConnectionFactory;

public enum ActiveMqUtil {
      activeMq;
      private ConnectionFactory  connectionFactory = null ;
      private FileInputStream in = null ;

      static{
           try {
               activeMq .connectionFactory = new ActiveMQConnectionFactory("tcp://47.92.6.177:61616" );
          } catch (Exception e ) {
               e.printStackTrace();
          } finally {
               if (activeMq .in != null){
                    try {
                         activeMq .in .close();
                   } catch (IOException e ) {
                         e.printStackTrace();
                   }
              }
          }
     }
      public Connection getConnection() throws JMSException{
           return connectionFactory .createConnection();
     }
      public void cloneConnection(Connection conn ) throws JMSException{
           if (conn != null){
               conn.close();
          }
     }
      public Session getSession(Connection conn) throws JMSException{
          Session session = conn.createSession( false , Session.AUTO_ACKNOWLEDGE );
           return session ;
     }
}

```
- DataProducer
```java
import java.util.Properties;
import javax.jms.Connection;
import javax.jms.Destination;
import javax.jms.JMSException;
import javax.jms.MessageProducer;
import javax.jms.Session;
import javax.jms.TextMessage;

public class DataProducer {

      private Connection connection ;
      private Session session;
      private Destination destination ;
      private MessageProducer producer;

      public DataProducer (String topic ) throws JMSException{
           this .connection = ActiveMqUtil.activeMq .getConnection();
           connection .start();
           this .session = ActiveMqUtil.activeMq .getSession( connection);
           this .destination = session .createTopic( topic);
           this .producer = session .createProducer(destination );
     }
      public void sendMessage(Message message ) throws Exception {
          TextMessage textMessage = session .createTextMessage(message .toString());
//        System.out.println(message.toString());
           producer.send( textMessage );
     }
      public void destory() throws JMSException{
           if (connection != null){
               connection .close();
          }
     }
}

```
- FileMethod
```java
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.InputStreamReader;
import java.io.UnsupportedEncodingException;

public class FileMethod {

      public static BufferedReader readFileReList(String file ) throws UnsupportedEncodingException, FileNotFoundException{
          BufferedReader br = new BufferedReader( new InputStreamReader(new FileInputStream( file), "UTF-8"));
           return br ;
     }
}

```
- MessageAdapter
```java
import java.util.Properties;
import javax.jms.JMSException;

public abstract class MessageAdapter {

      protected DataProducer dataProducer ;
      protected String topic;

      protected MessageAdapter(String topic) throws JMSException {
           this .topic = topic ;
           this .dataProducer = new DataProducer( topic);
     }
      protected void distory() throws JMSException{
           dataProducer .destory();
     }
      protected DataProducer getDataProducer() {
           return dataProducer ;
     }
      public abstract void start() throws Exception;
}

```
- Message
```java
public class Message {

      private String key;
      private String value;

      public String getKey() {
           return key ;
     }
      public void setKey(String key ) {
           this .key = key ;
     }
      public String getValue() {
           return value ;
     }
      public void setValue(String value ) {
           this .value = value ;
     }
      @Override
      public String toString() {
           // TODO Auto-generated method stub
           return key + "|" + value ;
     }
}

```
- ResultMap
```java
public class ResultMap<T> {
    private T result;
    private String message;
    private Integer status ;

    public ResultMap(T result, String message, Integer status ) {
        this .result = result ;
        this .message = message ;
        this .status = status ;
    }

    public T getResult() {
        return result ;
    }

    public void setResult(T result ) {
        this .result = result ;
    }

    public String getMessage() {
        return message ;
    }

    public void setMessage(String message ) {
        this .message = message ;
    }

    public Integer getStatus() {
        return status ;
    }

    public void setStatus(Integer status ) {
        this .status = status ;
    }
}

```
## 使用样例
- 样例
```java
import java.util.HashMap;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import javax.jms.JMSException;
import com.xskj.activeMQ.DataProducer;
import com.xskj.activeMQ.MessageAdapter;
import com.xskj.entity.Message;

public class TransferMessage extends MessageAdapter {

      private String key;
      private String value;

      public TransferMessage(String topic,String key ,String value ) throws JMSException {
           super (topic );
           this .key = key ;
           this .value = value ;
     }

      public void start() throws Exception {
          DataProducer dataProducer = this .getDataProducer();
          Message message = new Message();
           message.setKey( key);
           message.setValue( value);
           this .dataProducer .sendMessage( message);
           this .dataProducer .destory();
     }
}

```
## 如果使用文件进行配置则添加代码到start.propertiesz中
- start.propertiesz

```java
url: tcp://47.92.6.177:61616
filename: E:\\test. csv
topic: topic-test
mongodb: 123.123.123.123
port: 27017
v: 100
v2: 0
v3: 300

```
- ActiveMqUtil
```java
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;
import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.JMSException;
import javax.jms.Session;
import org.apache.activemq.ActiveMQConnectionFactory;

public enum ActiveMqUtil {
      activeMq;
      private ConnectionFactory   connectionFactory = null;
      public Properties pro = null ;
      private FileInputStream in = null ;

      static{
           try {
               activeMq .pro = new Properties();
               activeMq .in = new FileInputStream("start.properties" );
               activeMq .pro .load( activeMq. in);
              String url = activeMq .pro .getProperty( "url");
               activeMq .connectionFactory = new ActiveMQConnectionFactory(url );
          } catch (Exception e ) {
               e.printStackTrace();
          } finally {
               if (activeMq .in != null){
                    try {
                         activeMq .in .close();
                   } catch (IOException e ) {
                         e.printStackTrace();
                   }
              }
          }
     }

      public Connection getConnection() throws JMSException{
           return connectionFactory .createConnection();
     }

      public void cloneConnection(Connection conn ) throws JMSException{
           if (conn != null){
               conn.close();
          }
     }

      public Session getSession(Connection conn) throws JMSException{
          Session session = conn.createSession( false , Session.AUTO_ACKNOWLEDGE );
           return session ;
     }
}

```