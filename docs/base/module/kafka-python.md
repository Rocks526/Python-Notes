# kafka-python模块介绍

kafka的Python客户端，API类似于Java客户端，支持大多数Kafka功能。

# 代码示例

```python
# kafka-python示例代码

from kafka import KafkaConsumer, KafkaProducer, KafkaAdminClient, KafkaClient

# 生产者  会自动创建topic
producer = KafkaProducer(bootstrap_servers=['127.0.0.1:9092'])
topic = "kafka-python"

for i in range(10):
    producer.send(topic=topic, value="This is {} msg...".format(i).encode('utf-8'))
    print("send success")

producer.close()

# 消费者
consumer = KafkaConsumer(topic, auto_offset_reset='earliest', group_id='python-group', bootstrap_servers=['127.0.0.1:9092'])
# 订阅主题
# consumer.subscribe(topics=[topic])
try:
    for msg in consumer:
        print(msg)
        print("%s:%d:%d: key=%s value=%s" % (msg.topic, msg.partition, msg.offset, msg.key, msg.value))
        # 解码
        print(msg.value.decode('utf-8'))
except KeyboardInterrupt as e:
    print(e)
   
```

