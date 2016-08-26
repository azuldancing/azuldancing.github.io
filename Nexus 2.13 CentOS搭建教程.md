##1.所需软件
下载jdk
下载Nexus，<a href='https://www.sonatype.com/download-oss-sonatype'>点我下载</a>
##2.安装jdk，方法在"centos安装activemq并开机启动"中已经介绍
##3.安装nexus
```
//usr/local下新建目录
[root@hadoop local]# mkdir nexus
//解压到nexus目录
[root@hadoop local]# tar zxvf /tmp/nexus-2.13.0-01-bundle.tar.gz -C /usr/local/nexus/
//查看目录
[root@hadoop nexus]# ls
nexus-2.13.0-01  sonatype-work
//配置环境变量
[root@hadoop local]# vi /etc/profile
export NEXUS_HOME=/usr/local/nexus/nexus-2.13.0-01/bin
port PATH=$PATH:$JAVA_HOME/bin:$NEXUS_HOME
//退出,环境变量生效
[root@hadoop local]# source /etc/profile
```
##4.配置nexus文件
- 配置nexus启动文件
编辑 /usr/local/nexus/nexus-2.13.0-01/bin 下的 nexus 可执行文件，主要配置部分样例如下
```
#-----------------------------------------------------------------------------
# These settings can be modified to fit the needs of your application

# Set this to the root of the Nexus installation
# 设置 nexus 主目录，就是解压后的那个 nexus目录绝对路径
NEXUS_HOME="/usr/local/nexus/nexus-2.13.0-01"

# If specified, the Wrapper will be run as the specified user.

# IMPORTANT - Make sure that the user has the required privileges to write into the Nexus installation directory.

# NOTE - This will set the user which is used to run the Wrapper as well as
#  the JVM and is not useful in situations where a privileged resource or
#  port needs to be allocated prior to the user being changed.
# nexus官方不推荐以root 用户运行，如果你非要这么做，下面注释去掉 后面填写root
#RUN_AS_USER=
```
- 配置nexus的jdk路径
编辑/usr/local/nexus/nexus-2.13.0-01/bin/jsw/conf 下的 wrapper.conf 文件
```
# Set the JVM executable
# (modify this to absolute path if you need a Java that is not on the OS path)
# 配置 jdk中 java 可执行文件的位置(其实我感觉jre就可以，没测试，有兴趣的测试一下)
wrapper.java.command=/usr/local/java/jdk1.7.0_79/bin/java
```
##5.启动nexus
nexus start
如果提示root 无法启动，则环境变量中加入如下
```
export RUN_AS_USER=root
```
访问 ip:8081/nexus即可

![](/images/nexus_index.jpg)

##6.maven nexus jar包管理
- 仓库类型
打开 Views/Repositories --> Repositories 视图 

![](/images/nexus_repo.jpg)

nexus 仓库分为 4 种，group（仓库组）、hosted（宿主仓库）、proxy（代理仓库）、virtual（虚拟仓库）。
我们自己开发的构件通常是发布到 hosted 仓库，proxy 用来代理远程的公共仓库，一个 group 可以包含多个 hosted/proxy 仓库。

- 仓库组
打开 Views/Repositories --> Repositories --> Public Repositories --> Configuration 视图 

![](/images/nexus_group.jpg)

Configuration 栏的左边是仓库组仓库列表，右边是当前可选的仓库，可以从右边挑选合适的仓库加入到左边的仓库组，点击 Save 保存即可。
一个仓库组通常包含了多个仓库，仓库组中的仓库列表的顺序决定了构件下载时遍历的仓库的先后次序，因此，建议将远程中央仓库（Central）放
到仓库组的最后一项。
- maven settings.xml 配置
在 settings.xml 配置文件中添加如下配置
```
<servers>
  <server>
    <id>releases</id>
    <username>admin</username>
    <password>admin123</password>
  </server>
  <server>
    <id>snapshots</id>
    <username>admin</username>
    <password>admin123</password>
  </server>
  <server>
    <id>thirdparty</id>
    <username>admin</username>
    <password>admin123</password>
  </server>
</servers>
<mirrors>
  <mirror>
    <id>nexus</id>
    <mirrorOf>*</mirrorOf>
    <name>nexus public repositories</name>
    <url>http://127.0.0.1:8088/nexus/content/groups/public</url>
  </mirror>
</mirrors>
```
<server> 节点配置服务的账户密码，用于发布构件时进行身份和权限的认证。<mirror> 节点用于镜像的配置
- 发布构件
在 nexus 中找到如下的视图页面

![](/images/deploy.jpg)

复制以上的配置，粘贴到你的 pom.xml 配置文件中： 
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.fanlychie</groupId>
  <artifactId>proj</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>
  <name>proj</name>
  <url>http://maven.apache.org</url>
  <distributionManagement>
    <repository>
      <id>releases</id>
      <url>http://localhost:8088/nexus/content/repositories/releases</url>
    </repository>
  </distributionManagement>
</project>
```
这里需要注意的是，repository 节点的 id 需与 settings.xml 中配置的 server 节点的 id 相同，如果不相同，修改任意一方都可以，只
要让它们保持一致即可，否则，发布构件的时候会报 401，ReasonPhrase：Unauthorized 的错误，原因是无法认证用户的身份。
右键项目，Run As --> Maven build...，在 Goals 栏输入 deploy 或在命令行（cmd）执行 mvn deploy，在控制台若能看到
BUILD SUCCESS，表明构件发布成功。你可以在 nexus 的 Releases 仓库中找到这个构件：

![](/images/deploy_success.jpg)


发布构件的时候，如果想把源码也一起发布出去（执行 mvn dependency:sources 可获得源码），配置如下： 

```
project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.fanlychie</groupId>
  <artifactId>proj</artifactId>
  <version>1.0.1</version>
  <packaging>jar</packaging>
  <name>proj</name>
  <url>http://maven.apache.org</url>
  <distributionManagement>
    <repository>
      <id>releases</id>
      <url>http://localhost:8088/nexus/content/repositories/releases</url>
    </repository>
  </distributionManagement>
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
        <version>2.4</version>
        <executions>
          <execution>
            <id>attach-sources</id>
            <phase>install</phase>
            <goals>
              <goal>jar-no-fork</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
</project>
```
同一个构件是不允许发布两次的，先修改一下构件的版本，然后 deploy，结果如图： 

![](/images/deploy_success_src.jpg)

- 发布快照
快照用于区分稳定与不稳定的构件（发布的版本就是稳定的，快照版本是不稳定的）。构件的升级通常会带来许多的不稳定性，需要不断的修复，
快照可以避免由于升级带来的不稳定性迫使的不停的升级版本号，最终造成版本号的泛滥的问题。快照允许重新发布，而不需要变更构件的版本号。
在 nexus 中找到如下的视图页面： 


![](/images/snapshots.jpg)

复制以上的配置，粘贴到你的 pom.xml 配置文件中： 

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.fanlychie</groupId>
  <artifactId>proj</artifactId>
  <version>2.0.0-SNAPSHOT</version>
  <packaging>jar</packaging>
  <name>proj</name>
  <url>http://maven.apache.org</url>
  <distributionManagement>
    <repository>
      <id>releases</id>
      <url>http://localhost:8088/nexus/content/repositories/releases</url>
    </repository>
    <snapshotRepository>
      <id>snapshots</id>
      <url>http://localhost:8088/nexus/content/repositories/snapshots</url>
    </snapshotRepository>
  </distributionManagement>
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
        <version>2.4</version>
        <executions>
          <execution>
            <id>attach-sources</id>
            <phase>install</phase>
            <goals>
              <goal>jar-no-fork</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
</project>
```

版本号含有 SNAPSHOT 的构件将发布到 Snapshots 仓库，否则，将发布到 Releases 仓库。执行发布命令：mvn deploy，结果如图： 

![](/images/snapshots_success.jpg)


- 发布第三方构件
步骤见下图的标注： 

![](/images/3rd.jpg)

命令行方式发布：
```
mvn deploy:deploy-file -DgroupId=自定义groupId -DartifactId=自定义artifactId -Dversion=版本号 -Dpackaging=jar 
-Dfile=JAR文件的路径 -Durl=http://127.0.0.1:8088/nexus/content/repositories/thirdparty -DrepositoryId=thirdparty
```

- 远程索引
在 nexus 中找到如下的视图页面： 

![](/images/nexus_central_062715_013023_PM.jpg)


保存之后，在左侧面板中选择 Administration --> Scheduled Tasks，如下图： 

![](/images/nexus_central_1.png)

可以看到这里有一个 Repair Repositories Index 任务，任务完成之后会自动从面板中移除，点击左上角的 Refresh 按钮来查看。任务完成
之后回到 Repositories 面板，选择 Central 仓库右键，选择 Update Index，再回到 Scheduled Tasks 面板，可以看到有一个 Update 
Repositories Index 任务，这个任务花时较长，任务完成之后，回到 Repositories 面板，在 Browse Index 中可以看到从远程仓库下载回
来的索引文件，有了索引，即使 nexus 私服还没有你想要构件，你也可以搜索出你想要查找的构件。 

![](/images/nexus_central_3.jpg)


















