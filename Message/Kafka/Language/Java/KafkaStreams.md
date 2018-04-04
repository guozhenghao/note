# KafkaStreams
## 创建topic
```shell
bin/kafka-topics.sh --create \
    --zookeeper localhost:2181 \
    --replication-factor 1 \
    --partitions 1 \
    --topic input-test
```
```shell
bin/kafka-topics.sh --create \
    --zookeeper localhost:2181 \
    --replication-factor 1 \
    --partitions 1 \
    --topic output-test \
    --config cleanup.policy=compact
```
## 代码
- 导入包
    ```xml
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-clients</artifactId>
        <version>1.0.0</version>
    </dependency>

    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-streams</artifactId>
        <version>1.0.0</version>
    </dependency>
    ```
- 代码
    ```java
    import org.apache.kafka.common.serialization.Serdes;
    import org.apache.kafka.streams.*;
    import org.apache.kafka.streams.kstream.KStream;
    import org.apache.kafka.streams.kstream.KTable;
    import org.apache.kafka.streams.kstream.Produced;

    import java.util.Arrays;
    import java.util.Properties;

    public class WordCount {

        public static void main(String[] args) throws Exception {
            Properties props = new Properties();
            props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-wordcount");
            props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "ip:port");
            props.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
            props.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());

            final StreamsBuilder builder = new StreamsBuilder();

            KStream<String, String> source = builder.stream("input-topic", Consumed.with(Serdes.String(), Serdes.String()));
            KTable<String, Long> wordCounts = source.flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+"))).groupBy((key, value) -> value).count();
            wordCounts.toStream().to("output-topic", Produced.with(Serdes.String(), Serdes.Long()));
            final KafkaStreams streams = new KafkaStreams(builder.build(), props);
            streams.cleanUp();
            streams.start();
            Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
        }
    }
    ```
## 使用
运行java程序，在命令行创建生产者，topic使用java中的input，再创建一个消费者，topic使用java中的output

在生产者中输入字符串 多个单词使用空格分隔，如
````
my name is qqq
qqq is a boy
````
 
则在消费者中会出现
````
my      1
name    1
is      2
qqq     2
a       1
boy     1
````