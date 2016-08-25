- 安装haproxy
```
在ubuntu下安装haproxy使用命令
apt-get install haproxy 既可以安装
```
 - 由于初步安装的haproxy是不可以使用的，需要修改

```
#使用命令
cd /etc/default
nano haproxy
#把ENABLED改为1即可
```
![FastDFS](images/haproxy1.jpg)
- mysql 创建用户


需要给数据库创建一个无密码无任何权限的用户，用来haproxy代理
```
#创建命令
create user  haproxy
#则haproxy就是代理用户名
```
- haproxy配置代理
修改配置文件/etc/haproxy/haproxy.cfg，以下是修改后的代理配置案例
![FastDFS](images/haproxy2.jpg)

- 重启haproxy
```
#使用命令
service haproxy restart
```
