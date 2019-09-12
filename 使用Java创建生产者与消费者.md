## 本文利用java语言介绍如何使用Kafka来接收和发送消息。
## 首先我们创建一个java  Maven项目，引入如下依赖
<div>
<dependency>
<groupId>org.apache.kafka</groupId>
<artifactId>kafka-log4j-appender</artifactId>
<version>1.0.0</version>
</dependency>

<dependency>
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
<version>1.2.17</version>
</dependency>

<dependency>
<groupId>org.apache.kafka</groupId>
<artifactId>kafka-clients</artifactId>
<version>2.3.0</version>
</dependency>
</div>
## 此处kafka的依赖包的版本要根据实际安装的版本而定。
下面我们首先来创建一个生产者：

package org.study.mq.kafka.java;

import org.apache.kafka.clients.producer.*;

import java.util.HashMap;
import java.util.Map;

public class ProducerSample {

    public static void main(String[] args) {
        Map<String, Object> props = new HashMap<String, Object>();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("zk.connect","127.0.0.1:2181");
        
        String topic = "test";
        Producer<String, String> producer = new KafkaProducer<String, String>(props);
        producer.send(new ProducerRecord(topic, "idea-key2", "java-message 4"));
        producer.send(new ProducerRecord(topic, "idea-key2", "java-message 5"));
        producer.send(new ProducerRecord(topic, "idea-key2", "java-message 6"));

        producer.close();
    }

}

## 代码解释;
<ul>
	<li>'bootstrap.servers'代表的是Kafka集群，如果集群中有很多台服务器，那么服务器地址之间用逗号分隔。 9092是Kafka服务器默认监听的端口号。</li>
	<li>"key.serializer"和"value.serializer"代表的是消息的序列化类型。Kafka的消息是以键值对的形式发送到Kafka服务器上的，在消息发送到服务器上之前，
消息的生产者需要把不同类型的消息序列化为二进制类型，实例中发送的是文本类型，所以使用的是：StringSerializer。</li>
	<li>key.deserializer和"value.deserializer表示的是消息的反序列化类型。把来自Kafka集群的二进制消息反序列化为制定的类型。</li>
	<li>zk.connect用于指定Kafka连接Zookeeper的URL，提供了给予Zookeeper的集群服务器自动感知功能，可以动态的从ZooKeeper中读取Kafka集群的配置信息。</li>
	<li>当我们有了消息的生产者之后就可以调用send方法，发送消息了。该方法的入参是ProducerRecord类型的对象，ProducerRecord类提供了多种构造函<br/>数，常见的三种
ProducerRecord(topic, partition,key,value)<br/>
ProducerRecord(topic,key,value)<br/>
ProducerRecord(topic,value)<br/>
不难看出，key和value是必填的。如果指定了partition，那么消息就会被发送到指定的partition；如果没有指定partition但是指定了key，<br/>
那么消息就会按照hash(key)发送到对应的partition。如果partition和key都没有指定，那么消息就会以轮询的方式依次发送到每一个partition。
</li>
	</ul>

下面创建一个消费者：

package org.study.mq.kafka.java;

import org.apache.kafka.clients.consumer.*;
import java.util.Arrays;
import java.util.Properties;

public class ConsumerSample {

    public static void main(String[] args) {
        String topic = "test";

        Properties props = new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("group.id", "testGroup1");
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        Consumer<String, String> consumer = new KafkaConsumer(props);
        consumer.subscribe(Arrays.asList(topic));
        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(100);
            for (ConsumerRecord<String, String> record : records)
                System.out.printf("partition = %d, offset = %d, key = %s, value = %s%n", record.partition(), record.offset(), record.key(), record.value());
        }

    }
}

## 代码解释：
<ul>
	<li>group.id表示的是消费者的分组ID</li>
	<li>
enable.auto.commit表示的是 消费者的offest是否自动提交。</li>
	<li>auto.commit.interval.ms 用于设置自动提交offest到Zookeeper的时间间隔，时间单位是毫秒。</li>
	<li>
key.deserializer和value.deserializer表示用字符串来反序列化消息数据。</li>
	</ul>

## 测试步骤：
1、启动 ZooKeeper <br/>
2、启动 Kafka服务器<br/>
3、先运行消费者，这样当生产者发送消息的时候，消费者就能在后端看到消息。<br/>
4、运行生产者 ，发布几条消息，此时可以看到消费者接收到了消息。<br/>
5、在cmd中创建消费者，也订阅 topic test 。<br/>
cd E:\Kafka\kafka_2.12-2.3.0\bin\windows<br/>
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning<br/>
可以看到 cmd 中打印的消息 和 IDE 中显示的消息 是相同的。
