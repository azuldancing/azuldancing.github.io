###pom引入jar
```
<!-- rabbitmq -->
<dependency>
	<groupId>org.springframework.amqp</groupId>
	<artifactId>spring-rabbit</artifactId>
	<version>1.2.2.RELEASE</version>
</dependency>
```
###配置xml文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit-1.0.xsd">
                
                
   <bean id="rabbitClientConnectionFactory" class="com.rabbitmq.client.ConnectionFactory">  
        <property name="host" value="10.41.87.68"/>  
        <property name="port" value="5672" />  
        <property name="username" value="guest" />  
        <property name="password" value="guest" />  
     <!--    <property name="connectionTimeout" value="10000" />   -->
    </bean>  
      <!-- 连接服务配置 -->
   <rabbit:connection-factory id="rabbitConnectionFactory"  connection-factory="rabbitClientConnectionFactory"/>
   <rabbit:admin connection-factory="rabbitConnectionFactory" />
   <!-- queue 队列声明 -->
   <rabbit:queue id="jimmy-request-queue" durable="true" auto-delete="false" exclusive="false" name="jimmy-request-queue" />
<!--   exchange queue binging key 绑定 -->
   <rabbit:direct-exchange name="jimmy-queue-exchange" durable="true" auto-delete="false" id="jimmy-queue-exchange">
      <rabbit:bindings>
         <rabbit:binding queue="jimmy-request-queue" key="jimmy-request-queue" />
      </rabbit:bindings>
   </rabbit:direct-exchange>

   <!--   spring template声明 -->
    <rabbit:template exchange="jimmy-queue-exchange"   id="amqpTemplate"  connection-factory="rabbitConnectionFactory"  />
    <!-- queue litener  观察 监听模式 当有消息到达时会通知监听在对应的队列上的监听对象 -->
    <rabbit:listener-container connection-factory="rabbitConnectionFactory" acknowledge="auto" >
        <rabbit:listener queues="jimmy-request-queue" ref="foo" method="handleRequestMessage" />
    </rabbit:listener-container>
  
   <bean id="foo" class="com.stengg.kafka.RabbitmqListener"></bean>
  
</beans>
```
###注入AmqpTemplate
```
@Autowired
private AmqpTemplate amqpTemplate;

ResponseEntity response = new ResponseEntity();
response.setRtCode("ddddddddddddddddd");

amqpTemplate.convertAndSend("jimmy-request-queue",response);
amqpTemplate.convertAndSend("jimmy-request-queue","asssssssssssssssssssaa");
```
###消费者
```
package com.stengg.kafka;

import com.alibaba.fastjson.JSONObject;
import com.stengg.utils.ResponseEntity;

public class RabbitmqListener {


	public void handleRequestMessage(Object obj) {
		if(obj instanceof ResponseEntity){
			System.out.println("ResponseEntity"+JSONObject.toJSONString(obj));
		}else if(obj instanceof String){
			System.out.println("String"+obj);
		}

	}

}

```
