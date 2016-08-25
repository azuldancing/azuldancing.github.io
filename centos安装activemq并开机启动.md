<h2>一.安装jdk(activemq依赖jdk)</h3>

<h3>1.下载jdk.tar.gz</h3>

<h3>2.配置环境变量</h3>
```
#命令
nano /etc/profile
#环境变量
export JAVA_HOME=/usr/local/jdk1.7.0_79
export PATH=$PATH:$JAVA_HOME/bin
```
<h3>3.使用命令判断成功与否</h3>
```
#命令
echo $JAVA_HOME
#打印信息
/usr/local/jdk1.7.0_79
```
<h2>二.安装activemq</h2>
