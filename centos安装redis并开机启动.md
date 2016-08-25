- 下载安装包redis-3.0.7.tar.gz
- 安装到指定目录make PREFIX=/usr/local/redis install
- 安装好之后，由于没有配置文件可以指定配置文件动
```
./redis-server ../conf/redis.conf & ，其中&符号是为了退出，还是启动状态
```
- 配置redis的密码和地址在redis.conf，拷贝redis.conf到redis/conf目录下(conf目录自己创建)
- 设置redis默认后端启动，修改redis.conf 的daemonize为yes
- 设置redis开机自启动(2种方案)

#####方案1


```
#修改/etc/rc.local文件
vim /etc/rc.local
#在最后加入下面一行代码
 /usr/local/redis/bin/redis-server   /usr/local/redis/conf/redis.conf
 ```
#####方案2
推荐在生产环境中使用启动脚本方式启动redis服务。启动脚本redis_init_script 位于位于Redis的 /utils/ 目录下。
大致浏览下该启动脚本，发现redis习惯性用监听的端口名作为配置文件等命名，我们后面也遵循这个约定。
```
#redis服务器监听的端口
REDISPORT=6379
#服务端所处位置，在make install后默认存放与`/usr/local/bin/redis-server`，如果未make install则需要修改该路径，下同。
EXEC=/usr/local/bin/redis-server
#客户端位置
CLIEXEC=/usr/local/bin/redis-cli
#Redis的PID文件位置
PIDFILE=/var/run/redis_${REDISPORT}.pid
#配置文件位置，需要修改
CONF="/etc/redis/${REDISPORT}.conf"
```
配置环境
- 根据启动脚本要求，将修改好的配置文件以端口为名复制一份到指定目录。需使用root用户。

```
mkdir /etc/redis
cp redis.conf /etc/redis/6379.conf
```
 - 将启动脚本复制到/etc/init.d目录下，本例将启动脚本命名为redisd（通常都以d结尾表示是后台自启动服务）。
```
cp redis_init_script /etc/init.d/redisd
```
- 设置为开机自启动

```
#设置为开机自启动服务器
chkconfig redisd on
#打开服务
service redisd start
#关闭服务
service redisd stop
```
