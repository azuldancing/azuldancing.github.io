配置LeanCloud
打开LeanCloud官网，注册并完成邮箱激活后，进入控制台，创建一个新应用

![](/images/lean1.jpg)

进入我们新建的应用，进入存储分页，创建名为Counter的Class。

![](/images/lean2.jpg)

![](/images/lean3.jpg)

在这里，我们创建的Class名字必须为Counter，因为NexT会固定访问名字为Counter的Class，这个名字应该可以通过修改配置来实现更改，但是目前我找不到修改的方法。

配置Hexo
打开/theme/next/_config.yml，在如下对应位置填入app_id和app_key，并将enable设置为true。
```
leancloud_visitors:
enable: true
app_id: # your app_id
app_key: # your app_key
```
AppID和AppKey可以通过LeanCloud应用的设置分页下的应用key页面找到。

![](/images/lean4.jpg)


设置安全域名
有时候我们的会在本地通过locahost:4000浏览并编辑我们的页面，在这种情况下，LeanClound会记录很多没有意义的浏览次数。为了让统计的浏览次数有意义，我们可以在应用->设置->安全中心->Web安全域名中设置自己博客的域名，只有该域名可以访问LeanCloud系统，因此只会记录在这个域名下的访客数据。

![](/images/lean5.jpg)
配置footer.swig