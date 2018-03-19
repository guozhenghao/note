# kafka python使用
## 环境及依赖安装
`pip install kafka-python`

## 简单实用demo
- 生产者
```python
# -*- coding: UTF-8 -*-

from kafka import KafkaProducer
import json

# 创建生产者
producer1 = KafkaProducer(bootstrap_servers = 'ip:port')
# 创建生产者 消息json格式
producer2 = KafkaProducer(bootstrap_servers = 'ip:port',value_serializer=lambda v: json.dumps(v).encode('utf-8'))
for i in range(10):
    # 发送的消息要转换成byte
    producer1.send('topic',bytes(i))
    # 发送json
    a = {'a':i}
    producer2.send('topic',a)
producer1.close()
producer2.close()

```
- 消费者
```python
# -*- coding: UTF-8 -*-

from kafka import KafkaConsumer
import json

consumer = KafkaConsumer('topic',bootstrap_servers='ip:port',group_id ='test')
for msg in consumer:
    print(json.loads(msg.value))
```