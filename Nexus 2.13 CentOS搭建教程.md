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

##6.maven jar包管理
参考网络地址：<a href="http://www.blogjava.net/fancydeepin/archive/2015/06/27/maven-nexus.html">点我</a>
