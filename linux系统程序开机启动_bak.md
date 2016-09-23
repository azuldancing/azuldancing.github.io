###程序开机启动
1.启动命令加入/etc/rc.local中
```
[root@iZ22wd7jmjgZ bin]# ll /etc/rc.local 
lrwxrwxrwx 1 root root 13 May 30 13:14 /etc/rc.local -> rc.d/rc.local
```
由于rc.local实际指向rc.d/rc.local
```
[root@localhost ~]# ll /etc/rc.d/rc.local
-rw-r--r--. 1 root root 477 6月  10 13:35 /etc/rc.d/rc.local
```
/etc/rc.d/rc.local没有执行权限，于是按说明的内容执行
```
chmod +x /etc/rc.d/rc.local
```
重启后发现/etc/rc.local能够执行了。