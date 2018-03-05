# Java使用kafka简单样例

### Producer
```java
public class KafkaProducerTest {
    public static void StartProducer(){
        Properties props = new Properties();
        props.put("bootstrap.servers", "192.168.1.174:9092");
        props.put("acks", "all");
        props.put("retries", 0);
        props.put("batch.size", 16384);
        props.put("linger.ms", 1);
        props.put("buffer.memory", 33554432);
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.IntegerSerializer");

        Producer<String,Integer> producer = new KafkaProducer<String, Integer>(props);

        for (int i=0;i<100;i++) {
            producer.send(new ProducerRecord<String, Integer>("kafkaTest", Integer.toString(i), Integer.toString(i)),
            new SendCallBack());
            
        }

        producer.close();

        System.out.println("消息发送完毕");
    }
}

class  SendCallBack implements Callback{
    public void onCompletion(RecordMetadata recordMetadata, Exception e) {
        if(recordMetadata == null){
            System.out.printf("消息发送失败:%s \n",e.getMessage());
            return;
        }

        System.out.printf("发送成功: %s",recordMetadata.offset());
    }
}
```

### Consumer
```java
public class KafkaConsumerTest {

    public static void StartConsumer(){
        Properties props = new Properties();
        props.put("bootstrap.servers", "192.168.1.174:9092");
        props.put("group.id","test");
        props.put("enable.auto.commit","true");
        props.put("session.timeout.ms","30000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String,String> consumer = new KafkaConsumer<String, String>(props);
        consumer.subscribe(Arrays.asList("kafkaTest"));

        while (true){
            ConsumerRecords<String, String> records =consumer.poll(50000);

            for(ConsumerRecord<String,String> record:records){
                System.out.printf("offset =%d key=%s value=%s \n",record.offset(),record.key(),record.value());
            }
        }
    }

}
```

### Main
```java
public class Application {
    public static void main(String[] args){
        KafkaProducerTest.StartProducer();
        KafkaConsumerTest.StartConsumer();
    }
}
```