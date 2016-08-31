#本文以mysql6.5为例
###安装wegt
```
yum install wget
```
###下载mysql 的rpm文件
```
wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
```
###安装下载的文件
```
rpm -ivh mysql-community-release-el6-5.noarch.rpm
```
###yum 安装mysql
```
yum install mysql-server
```
###启动mysql
```
/etc/init.d/mysqld start
```
- Because the MySQL server is just installed it has blank mysql root password. To reset the mysql root password.

###设置密码
```
mysql_secure_installation
```
###登录mysql
```
mysql -u root -p
```
###设置mysql字符编码
```
在[client]下添加default-character-set=utf8
在[mysqld]添加character_set_server=utf8
在[mysql]下添加default-character-set = utf8
重启命令：/etc/init.d/mysqld start
```
### 授权用户访问权限
```
grant all privileges on *.* to 用户名@'%' identified by "密码" ;　
//设置用户testuser，只能访问数据库test_db的表user_infor，数据库中的其他表均不能访问 ；
grant all privileges on test_db.user_infor to testuser@localhost identified by "123456" ;　
```