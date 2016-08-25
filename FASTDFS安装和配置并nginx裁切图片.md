<h1>单机安装部署(CentOS6 环境)</h1>
软件准备
- FastDFS_v5.05.tar.gz
- fastdfs-nginx-module_v1.16.tar.gz
- libfastcommon-1.0.7.tar.gz
- libfastcommon-master.zip(github最新下载)
<h1>安装</h1>
<h3>安装libfastcommon-master</h3>
```
#解压
unzip libfastcommon-master.zip
#进入解压后目录
cd libfastcommon
./make.sh
./make.sh install
设置软件链接需要
ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
```
<h3>FastDFS_v5.08.tar.gz</h3>
```
tar zxvf FastDFS_v5.08.tar.gz -C /usr/local/src/
./make.sh
./make.sh install
- make.sh 安装前可修改的一些参数
WITH_LINUX_SERVICE=1 #注释取消表示使用内置web server
TARGET_PREFIX=/usr/local #安装的目录
TARGET_CONF_PATH=/etc/fdfs #配置文件目录
```



