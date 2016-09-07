#安装rabbitmq
###安装erlang
```
yum install erlang
```
###安装RabbitMQ
//本例中RabbitMQ的版本是3.5.1
```
wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.5.1/rabbitmq-server-3.5.1-1.noarch.rpm
rpm --import http://www.rabbitmq.com/rabbitmq-signing-key-public.asc 
yum install rabbitmq-server-3.5.1-1.noarch.rpm
```
###启动RabbitMQ
//配置为守护进程随系统自动启动，root权限下执行

chkconfig rabbitmq-server on

//启动rabbitMQ服务

/sbin/service rabbitmq-server start

//如果报如下异常：

Starting rabbitmq-server (via systemctl):  Job for rabbitmq-server.service failed. See 'systemctl status rabbitmq-server.service' and 'journalctl -xn' for details. [FAILED]

尝试下面的操作：

禁用 SELinux ，修改 /etc/selinux/config

SELINUX=disabled

修改后重启系统

###安装Web管理界面插件
//终端输入：
```
rabbitmq-plugins enable rabbitmq_management
//安装成功后会显示如下内容
The following plugins have been enabled:
mochiweb
webmachine
rabbitmq_web_dispatch
amqp_client
rabbitmq_management_agent
rabbitmq_management
Plugin configuration has changed. Restart RabbitMQ for changes to take effect.
```
###登录Web管理界面
浏览器输入localhost：15672,账号密码全输入guest即可登录
//注意rabbitmq默认本机localhost登录，如果非本机
在/etc/rabbitmq/中配置 rabbitmq.config
```
[root@centos rabbitmq]# touch rabbitmq.config
[root@centos rabbitmq]# vi rabbitmq.config 
//插入如下一句话，重启即可
[{rabbit, [{loopback_users, []}]}].

```
#配置spring和rabbitmq
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
