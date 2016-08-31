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