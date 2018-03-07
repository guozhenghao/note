# 读取yaml配置文件
## maven所需依赖
```xml
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.19</version>
</dependency>
```
## 使用
- 配置文件的路径
    
    为了windows和linux通用，建议使用如下方法：
    ```java
    //项目的工作目录
    System.getProperty("user.dir");
    //文件夹的分隔符
    System.getProperty("file.separator");
    //样例 项目根目录下的kafka.yaml文件
    String yamlPath = System.getProperty("user.dir") +  System.getProperty("file.separator") + ("kafka.yaml");
    ```

- 配置文件样例
```yaml
# 注意不能使用tab来缩进
producer:
    # 生产者配置文件实体类目前添加的所有参数demo
    producerConfigDemo:
        # 如果需要直接将配置文件中的某部分转换为对象，下行的！！则为该实体类的位置
        !!com.test.enity.ProducerProperties
        bootstrapServers: ip:port
        topic: test
        acks: all
        retries: 0
        batchSize: 16384
        lingerMs: 1
        bufferMemory: 33554432
        keySerializer: org.apache.kafka.common.serialization.StringSerializer
        valueSerializer: org.apache.kafka.common.serialization.StringSerializer

consumer:
    # 消费者配置文件实体类目前添加的所有参数demo
    consumerConfigDemo:
        !!com.test.enity.ConsumerProperties
        bootstrapServers: ip:prot
        groupId: test
        topic: test
        enableAutoCommit: true
        autoCommitIntercalMs: 1000
        sessionTimeoutMs: 60000
        keyDeserializer: org.apache.kafka.common.serialization.StringDeserializer
        valueDeserializer: org.apache.kafka.common.serialization.StringDeserializer
```

- 使用，以producerConfig为例
    - 首先创建对应的实体类
    ```java
    //部分代码
    package com.test.enity;
    import java.util.Properties;

    public class ProducerProperties {

        private String bootstrapServers;
        private String topic;

        public ProducerProperties() {

        }

        public String getBootstrapServers() {
            return bootstrapServers;
        }

        public void setBootstrapServers(String bootstrapServers) {
            this.bootstrapServers = bootstrapServers;
        }

        public String getTopic() {
            return topic;
        }

        public void setTopic(String topic) {
            this.topic = topic;
        }
    }
    ```

    - 部分解析代码
    ```java
    //获取配置文件中producer中的producerConfigDemo
    Yaml yaml = new Yaml();
    ProducerProperties producerProperties;
    try {
        //此块的load结构根据自己的配置文件结构而定 如果所有结构一样Object可以直接使用对应的类
        Map<String, Map<String, Object>> yamlMap = yaml.loadAs(new FileInputStream("配置文件路径"), Map.class);
        producerProperties = (ProducerProperties) yamlMap.get("producer").get("producerConfigDemo");
    } catch (Exception e) {
        return null;
    }


    //也可以不用类来接收，直接使用map，k/v的形式来获得需要的参数
    ```
