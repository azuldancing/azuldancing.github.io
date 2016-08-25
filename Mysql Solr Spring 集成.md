###1.solr安装
从官网下载solr，安装solr
###2.配置solr和mysql的连接配置
进入到solr的bin目录，创建solr Selector abl，命令如下

![](/images/solr1.jpg)
修改abl的配置文件，目录位置如下

![](/images/solr2.jpg)
修改managed-schema文件，这个文件是查询字段可以显示的配置和索引等配置，新增查询字段和索引，如下修改

![](/images/solr3.jpg)
在solrconfig.xml文件中，配置data-config.xml文件的引入路径

![](/images/solr4.jpg)
创建data-config.xml文件
```
命令为：touch data-config.xml
```
修改data-config.xml文件用来配置数据源和sql查询，如下

![](/images/solr5.jpg)

jar包引入
```
A:将solr-dataimporthandler-5.5.0.jarr从solr-5.5.0/dist文件夹下
  copy到solr-5.5.0/server/solr-webapp/webapp/WEB-INF/lib当中，此java包是导入数据用的。
B:从mysql官网中下载一个mysql-connector-java-5.1.35.zip压缩包，
  解压出一个mysql-connector-java-5.1.35-bin.jar包，将它copy到solr-5.5.0/server/lib下
```
solr重启：命令如下

![](/images/solr6.jpg)

###3.数据刷新和导入数据


地址栏输入http://ip地址:启动的端口号/solr

![](/images/solr7.jpg)

###4.查询导入的数据

![](/images/solr8.jpg)


###5.spring 配置连接solr

#####5.1配置pom所需jar
```
#在pom中引入jar包
<!-- solr引入 -->
<dependency>
        <groupId>org.springframework.data</groupId>
	<artifactId>spring-data-solr</artifactId>
	<version>1.5.0.RELEASE</version>
</dependency>
```
#####5.2配置spring的solrTemperlate
```
<solr:solr-server id="solrServer" url="http://127.0.0.1:8984/solr/abl" />
<bean id="solrTemplate" class="org.springframework.data.solr.core.SolrTemplate" scope="singleton">
    <constructor-arg ref="solrServer" />
</bean>
```
完整配置

![](/images/solr9.jpg)

