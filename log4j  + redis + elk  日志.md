本文参考文档

[http://www.cnblogs.com/onetwo/p/6059231.html](http://note.youdao.com/)
[http://m.blog.csdn.net/lh2420124680/article/details/74277380](http://note.youdao.com/)

安装过程中的错误参考

[http://blog.csdn.net/lijiaz5033/article/details/73614617](http://note.youdao.com/)

# 1.下载ELK所需软件
本位以5.5.1为例，
   下载地址为[https://www.elastic.co/downloads](下载)
~~~~
jdk1.8
elasticsearch-5.5.1.tar.gz
kibana-5.5.1-linux-x86_64.tar.gz
logstash-5.5.1.tar.gz
redis-3.2.10.tar.gz
node-v6.10.2-linux-x64
elasticsearch-head
~~~~
elasticsearch-head的下载地址为:[https://github.com/mobz/elasticsearch-head](下载)

# 2.安装
安装jdk

安装redis

安装elasticsearch，运行不能使用root启动

户创建运行ELK的用户
~~~~
[root@localhost local]# groupadd elk
[root@localhost local]# useradd -g elk elk
 ~~~~
创建ELK运行目录,并切换用户
~~~~
[root@localhost local]# mkdir /elk
[root@localhost local]# chown -R elk:elk /elk
[root@localhost local]# chnod -R 777 /elk
[root@localhost local]# su elk
~~~~
修改elasticsearch文档
~~~~
[root@localhost elk]# cd elasticsearch-5.5.1/
[root@localhost elasticsearch-5.5.1]# vim config/elasticsearch.yml 
~~~~
修改如下
~~~~
cluster.name: my-application
node.name: node-1
path.data: /usr/local/elk/data
path.logs: /usr/local/elk/logs
network.host: 192.168.126.129
http.port: 9200
http.cors.enabled: true
http.cors.allow-origin: "*"
~~~~
保存并启动
~~~~
[root@localhost elasticsearch-5.5.1]# ./bin/elasticsearch &
~~~~
查看是否启动成功
~~~~
netstat -ant
tcp6       0      0 192.168.126.129:9200    :::*                    LISTEN     
tcp6       0      0 192.168.126.129:9300    :::*                    LISTEN    
~~~~
用浏览器访问：http://192.168.126.129:9200

安装elasticsearch-head
~~~~
[root@localhost elk]# cd elasticsearch-head/
[root@localhost elasticsearch-head]# npm install
[root@localhost elasticsearch-head]# grunt server 
~~~~
网页上输入localhost：9100，点击连接

安装logstash
logstash是ELK中负责收集和过滤日志的

配置logstash方法很多，参考[https://kibana.logstash.es/content/logstash/get-start/install.html]()
```
[root@localhost logstash-5.5.1]# cd config/
[root@localhost config]# touch logstash.conf
[root@localhost config]# vi logstash.conf
input {
        redis{
                host=>'192.168.126.129'
                port=>'6379'
                key=>"loginput"
                data_type=>"list"
                type=>'redis-input'
        }
}

output {

        #stdout{codec => rubydebug}
        elasticsearch {
                        hosts => ["192.168.126.129:9200"]
                        index => "logstash-%{+YYYY.MM.dd}"
                        document_type => [type]
                        flush_size => 2
                        idle_flush_time => 2
                        sniffing => true
                        template_overwrite => true
                        codec => plain {
                             charset => "UTF-8"
                          }
        }
}
```
保存并启动
```
[root@localhost logstash-5.5.1]# ./bin/logstash -d config/logstash.conf &
```
安装kibana
```
[root@localhost elk]# cd kibana-5.5.1-linux-x86_64/
[root@localhost kibana-5.5.1-linux-x86_64]# vi config/kibana.yml
server.port: 5601
server.host: "192.168.126.129"
elasticsearch.url: "http://192.168.126.129:9200"
kibana.index: "logstash"
```
保存退出启动kibana
```
[root@localhost kibana-5.5.1-linux-x86_64]# ./bin/kibana &
```
用浏览器访问：http://192.168.126.129:5601

pom引入elk所需jar包
```
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.5.1</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>net.logstash.log4j</groupId>
            <artifactId>jsonevent-layout</artifactId>
            <version>1.0</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.ryantenney.log4j</groupId>
            <artifactId>redis-appender</artifactId>
            <version>1.0.1</version>
        </dependency>
```
配置log4j.properties
```
log4j.rootLogger=DEBUG, redis, console

log4j.appender.redis=com.ryantenney.log4j.RedisAppender
log4j.appender.redis.host=192.168.126.129
log4j.appender.redis.port=6379
log4j.appender.redis.key=loginput
log4j.appender.redis.period=500
log4j.appender.redis.batchSize=50
log4j.appender.redis.purgeOnFailure=true
log4j.appender.redis.alwaysBatch=true
log4j.appender.redis.layout=net.logstash.log4j.JSONEventLayout
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d %p [%t] %c - %m - %r %n
```

```
参考
ELKB5.2.2集群环境部署及优化终极文档
本人陆陆续续接触了ELK的1.4，2.0，2.4，5.0，5.2版本，可以说前面使用当中一直没有太多感触，最近使用5.2才慢慢有了点感觉，可见认知事务的艰难，本次文档尽量详细点，现在写文档越来越喜欢简洁了，不知道是不是不太好。不扯了看正文（注意这里的配置是优化前配置，正常使用没问题，量大时需要优化）。

备注：
本次属于大版本变更，有很多修改，部署重大修改如下：
1，filebeat直接输出kafka，并drop不必要的字段如beat相关的
2，elasticsearch集群布局优化：分三master节点6data节点
3，logstash filter 加入urldecode支持url、reffer、agent中文显示
4，logstash fileter加入geoip支持客户端ip区域城市定位功能
5, logstash mutate替换字符串并remove不必要字段如kafka相关的
5，elasticsearch插件需要另外部署node.js，不能像以前一样集成一起
6，nginx日志新增request参数、请求方法

一，架构
可选架构
filebeat--elasticsearch--kibana
filebeat--logstash--kafka--logstash--elasticsearch--kibana
filebeat--kafka--logstash--elasticsearch--kibana
由于filebeat5.2.2支持多种输出logstash、elasticsearch、kafka、redis、syslog、file等，为了优化资源使用率且能够支持大并发场景选择
filebeat（18）--kafka（3）--logstash（3）--elasticsearch（3）--kibana（3--nginx负载均衡

具体步骤见文档
http://jerrymin.blog.51cto.com/3002256/1927481

```
