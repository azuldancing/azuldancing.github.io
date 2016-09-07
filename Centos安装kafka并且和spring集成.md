#安装请<a href="https://mos.meituan.com/library/32/how-to-install-kafka-on-centos7/"> 参考</a>
###说明
```
Kafka 使用Zookeeper 来保存相关配置信息，Kafka及Zookeeper 依赖Java 运行环境，从oracle网站下载JDK 安装包，解压安装：
```
###安装jdk
###安装zookeeper
- 下载zookeeper-3.4.8.tar.gz
- 解压到/usr/local目录下
```
[root@centos local]# tar zxvf /tmp/zookeeper-3.4.8.tar.gz -C /usr/local/
[root@centos local]# mv zookeeper-3.4.8/ zookeepermv
//在/usr/local/zookeeper/conf目录下
[root@centos conf]# cp zoo_sample.cfg zoo.cfg
//注意
tickTime=2000    
dataDir=/Users/apple/zookeeper/data    
dataLogDir=/Users/apple/zookeeper/logs    
clientPort=4180   
```
- 参数说明
```
tickTime: zookeeper中使用的基本时间单位, 毫秒值.
dataDir: 数据目录. 可以是任意目录.
dataLogDir: log目录, 同样可以是任意目录. 如果没有设置该参数, 将使用和dataDir相同的设置.
clientPort: 监听client连接的端口号.
```
###启动zookeeper
```
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
//或者
bin/zookeeper-server-start.sh start
```
###安装Kafka
- 下载kafka_2.10-0.10.0.1.tgz
- 解压到/usr/local目录下
- 修改kafka配置文件
```
[root@centos config]# vi server.properties 
listeners=PLAINTEXT://10.41.87.68:9092
advertised.listeners=PLAINTEXT://10.41.87.68:9092
zookeeper.connect=localhost:2181
zookeeper.connection.timeout.ms=6000
```
- 启动Kafka 服务
```
./kafka-server-start.sh ../config/server.properties
```
- 创建topic
```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```
- 查看topic
```
bin/kafka-topics.sh --list --zookeeper localhost:2181
```
- 产生消息
```
bin/kafka-console-producer.sh --broker-list 10.41.87.68:9092 --topic test 

Hello world！

Hello Kafka！

```
- 消费消息
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
Hello world!
Hello Kafka!


#spring 和kafka集成
###pom引入jar
```
<!-- hessian -->
 <dependency>
	<groupId>com.caucho</groupId>
	<artifactId>hessian</artifactId>
	<version>4.0.38</version>
</dependency>

<!-- kafka -->
<dependency>
	<groupId>org.springframework.integration</groupId>
	<artifactId>spring-integration-kafka</artifactId>
	<version>2.0.0.RELEASE</version>
</dependency>
```
###配置kafka
- 生产者
```
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
     xmlns:context="http://www.springframework.org/schema/context"  
     xsi:schemaLocation="http://www.springframework.org/schema/beans  
         http://www.springframework.org/schema/beans/spring-beans.xsd  
         http://www.springframework.org/schema/context  
         http://www.springframework.org/schema/context/spring-context.xsd">  
      
     <!-- 定义producer的参数 -->  
     <bean id="producerProperties" class="java.util.HashMap">  
        <constructor-arg>  
            <map>  
                <entry key="bootstrap.servers" value="10.41.87.68:9092"/>  
                <entry key="group.id" value="0"/>  
                <entry key="retries" value="10"/>  
                <entry key="batch.size" value="16384"/>  
                <entry key="linger.ms" value="1"/>  
                <entry key="buffer.memory" value="33554432"/>  
                <entry key="key.serializer" value="org.apache.kafka.common.serialization.IntegerSerializer"/>  
                <entry key="value.serializer" value="org.apache.kafka.common.serialization.ByteArraySerializer"/>  
            </map>  
        </constructor-arg>  
     </bean>  
       
     <!-- 创建kafkatemplate需要使用的producerfactory bean -->  
     <bean id="producerFactory" class="org.springframework.kafka.core.DefaultKafkaProducerFactory">  
        <constructor-arg>  
            <ref bean="producerProperties"/>  
        </constructor-arg>  
     </bean>  
       
     <!-- 创建kafkatemplate bean，使用的时候，只需要注入这个bean，即可使用template的send消息方法 -->  
     <bean id="KafkaTemplate" class="org.springframework.kafka.core.KafkaTemplate">  
        <constructor-arg ref="producerFactory"/>  
        <constructor-arg name="autoFlush" value="true"/>  
        <property name="defaultTopic" value="wangsen"/>  
     </bean>  
  
</beans>  
```
- 消费者
```
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
     xmlns:context="http://www.springframework.org/schema/context"  
     xsi:schemaLocation="http://www.springframework.org/schema/beans  
         http://www.springframework.org/schema/beans/spring-beans.xsd  
         http://www.springframework.org/schema/context  
         http://www.springframework.org/schema/context/spring-context.xsd">  
      
    <!-- 定义consumer的参数 -->  
     <bean id="consumerProperties" class="java.util.HashMap">  
        <constructor-arg>  
            <map>  
                <entry key="bootstrap.servers" value="10.41.87.68:9092"/>  
                <entry key="group.id" value="0"/>  
                <entry key="enable.auto.commit" value="true"/>  
                <entry key="auto.commit.interval.ms" value="1000"/>  
                <entry key="session.timeout.ms" value="15000"/>  
                <entry key="key.deserializer" value="org.apache.kafka.common.serialization.IntegerDeserializer"/>  
                <entry key="value.deserializer" value="org.apache.kafka.common.serialization.ByteArrayDeserializer"/>  
            </map>  
        </constructor-arg>  
     </bean>  
       
     <!-- 创建consumerFactory bean -->  
     <bean id="consumerFactory" class="org.springframework.kafka.core.DefaultKafkaConsumerFactory">  
        <constructor-arg>  
            <ref bean="consumerProperties"/>  
        </constructor-arg>  
     </bean>  
       
     <!-- 实际执行消息消费的类 -->  
     <bean id="messageListernerConsumerService" class="com.stengg.kafka.KafkaConsumer"/>  
       
     <!-- 消费者容器配置信息 -->  
     <bean id="containerProperties" class="org.springframework.kafka.listener.config.ContainerProperties">  
        <constructor-arg value="wangsen"/>  
        <property name="messageListener" ref="messageListernerConsumerService"/>  
     </bean>  
       
     <!-- 创建kafkatemplate bean，使用的时候，只需要注入这个bean，即可使用template的send消息方法 -->  
     <bean id="messageListenerContainer" class="org.springframework.kafka.listener.KafkaMessageListenerContainer" init-method="doStart">  
        <constructor-arg ref="consumerFactory"/>  
        <constructor-arg ref="containerProperties"/>  
     </bean>  
  
</beans>  
```
- kafka生产者接口和实现类
```
public interface KafkaMessageProducer {
	/**
	 * kafka消息发送
	 * 
	 * @param message
	 */
	public void kafkaMessageProducer(byte[] message);

}
```

```
@Component
public class KafkaMessageProducerImpl implements KafkaMessageProducer{	
	
	private KafkaTemplate<Integer, byte[]> kafkaTemplate;

	/**
	 * kafka消息发送
	 * 
	 * @param message
	 */
	@Override
	public void kafkaMessageProducer(byte[] message) {
		kafkaTemplate.sendDefault(message);
	}

}
```
- kafka消费者
```
public class KafkaConsumer implements MessageListener<Integer, byte[]> {
	/**
	 * 收到消息对了
	 */
	@Override
	public void onMessage(ConsumerRecord<Integer, byte[]> arg0) {
		try {
			Object o = HessionSerialize.deserialize(arg0.value());
			dealMessage(o);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	/**
	 * 消息队列处理
	 * @param obj
	 */
	private void dealMessage(Object obj){
		if (obj instanceof ResponseEntity) {
			System.out.println("ResponseEntity");
		}

		if (obj instanceof String) {
			System.out.println("String");
		}
	}

}
```
- kafka支持序列化传输，hessian序列化工具
```
public class HessionSerialize {
	/**
	 * hession序列化
	 * 
	 * @param obj
	 * @return
	 * @throws IOException
	 */
	public static byte[] serialize(Object obj) throws IOException {
		if (obj == null)
			throw new NullPointerException();

		ByteArrayOutputStream os = new ByteArrayOutputStream();
		HessianOutput ho = new HessianOutput(os);
		ho.writeObject(obj);
		return os.toByteArray();
	}

	/**
	 * hession反序列化
	 * 
	 * @param by
	 * @return
	 * @throws IOException
	 */
	public static Object deserialize(byte[] by) throws IOException {
		if (by == null)
			throw new NullPointerException();

		ByteArrayInputStream is = new ByteArrayInputStream(by);
		HessianInput hi = new HessianInput(is);
		return hi.readObject();
	}
}
```

















