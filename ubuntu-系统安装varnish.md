###1.命令安装avrnish
```
sudo apt-get install varnish
```
###2.修改varnish 的浏览器端口
```
cd /etc/default/
nano varnish
```
修改如下地方的端口号，将6081端口改为80端口，则80端口即为网页访问端口

![](/images/varnish1.jpg)


###3.varnish配置项目访问和负载均衡
```
cd /etc/varnish/ 
nano default.vcl
```
如下即为配置的demo

![](/images/varnish2.jpg)


