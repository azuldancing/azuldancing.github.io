一、安装 Node.js

首先下载 Node.js 点我下载
解压到任意目录

1
tar -zxvf node-v4.2.4-linux-x64.tar.gz /usr/local/node
配置环境变量

1
2
3
4
5
6
# 编辑 /etc/profile (使用vim)
vim /etc/profile
# 在底部添加 PATH 变量
export PATH=$PATH:/usr/local/node/bin
# 最后保存并使其生效即可
source /etc/profile
二、安装 Hexo

创建博客所在目录

1
mkdir hexo
安装 Hexo

1
2
3
4
5
6
7
8
# 切换目录
cd hexo
# 安装Git(已安装可跳过)
yum install git-core
# 安装 Hexo
npm install -g hexo-cli
# 初始化 hexo
hexo init
启动 Hexo 测试 是否安装成功

1
2
3
4
5
# 启动测试
hexo server
# 此时控制台应该打印 如下语句 
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
# 测试访问 http://IP:4000

安装扩展程序包
Hexo 提供了其他许多扩展插件，比如 git插件、快捷命令插件等

1
2
3
4
5
6
7
8
9
10
11
12
13
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked --save
npm install hexo-renderer-stylus --save
npm install hexo-generator-feed --save
npm install hexo-generator-sitemap --save
三、Hexo 相关命令

新建文章(会在 hexo/source/_post 下生成对应.md 文件)

1
hexo n “文章名称”
生成静态文件(位于 hexo/public 目录)

1
hexo g
启动 Hexo 预览

1
hexo s
提交部署(需要相关配置)

1
hexo d
四、Hexo相关设置

直接上配置文件 (hexo/_config.yml)，里面包括主题、github等参数设置，主题、Github配置请看下面

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/
# Site 站点相关配置
title: 烟雨平生
subtitle: 一蓑烟雨任平生，也无风雨也无晴。
description: 记录生活点滴，不断学习，每走一步，在这里印下足迹。
author: azuldancing
language: zh-CN
timezone:
# URL 网站URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yuming.com
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:
# Directory 相关文件夹设置
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:
# Writing 文章设置
new_post_name: :title-:year-:month-:day.md # 新文章(post)生成为文件名
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: true
  tab_replace:
# Category & Tag
default_category: uncategorized
category_map:
tag_map:
# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss
# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page
# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: yilia  #主题设置
stylus:
  compress: true
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy: #部署插件设置(目前只自动部署到github)
  type: git
  repo: git@github.com:azuldancing/azuldancing.github.io.git
  branch: master
  message: '站点更新: {{ now("YYYY-MM-DD HH:mm:ss") }}'
五、主题设置

建议使用next主题

主题安装
1、 无论什么主题，先 Download 到本地，拿 yilia 为例，由于其托管于github，直接git clone即可

1
2
# 任意目录下执行会生成 yilia 文件夹
git clone https://github.com/litten/hexo-theme-yilia.git yilia
2、 根据作者教程更改一些参数，主要更改 主题目录 下的 _config.yml文件 (yilia/_config.yml)，我的配置样例如下

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
# Header
menu:
  主页: /
  所有文章: /archives
  随笔: /categories/随笔
  IT: /categories/IT
  相册: /tags/相册
# SubNav
subnav:
  github: "https://github.com/azuldancing/"
  #weibo: "#"
  #rss: "#"
  #zhihu: "#"
  #douban: "#"
  mail: "mailto:azuldancing@gmail.com"
  #facebook: "#"
  google: "#"
  #twitter: "#"
  #linkedin: "#"
rss: /atom.xml
# Content
excerpt_link: more
fancybox: true
mathjax: true
# 是否开启动画效果
animate: true
# 是否在新窗口打开链接
open_in_new: false
# Miscellaneous
google_analytics: ''
favicon: /favicon.ico
#你的头像url
avatar: /logo.jpeg
#是否开启分享
share: true
share_addthis: false
#是否开启多说评论，填写你在多说申请的项目名称 duoshuo: duoshuo-key
#若使用disqus，请在博客config文件中填写disqus_shortname，并关闭多说评论
duoshuo: 'azuldancing'
#是否开启云标签
tagcloud: true
#是否开启友情链接
#不开启——
#friends: false
#开启——
friends:
#是否开启“关于我”。
#不开启——
#aboutme: false
#开启——
aboutme: 社会三好青年，祖国未来栋梁，世界未来领袖......前面都是吹牛逼的，我就是个逗比......
3、配置完成后将主题目录复制到 hexo/themes 目录下

1
cp -r yilia hexo/themes
4、修改hexo主配置文件为对应的主题

1
2
3
4
## Themes: http://hexo.io/themes/
theme: yilia  #主题名称
stylus:
  compress: true
5、重新生成静态文件 访问测试

1
2
3
hexo clean   # 清除缓存
hexo g       # 生成静态文件
hexo s       # 启动服务器预览
六、部署到Github

新建 Github 项目
首先注册 Github 账户，这里掠过，然后创建一个新项目，项目名称为 用户名.github.io ，比如我的Github用户名是azuldancing，则创建的项目名为 azuldancing.github.io

hexo_githubproject

生成密钥
服务器生成秘钥；如果在 $HOME/.ssh 下有 id_rsa、id_rsa.pub 则可忽略创建过程

1
2
3
4
# 执行以下命令然后一路回车 创建秘钥
ssh-keygen
# 复制 公钥内容 稍后加入Github 账户的 sshkey中 
less ~/.ssh/id_rsa.pub
设置 Github SSH Key
登录 Github后访问 https://github.com/settings/ssh，选择 Add SSH key ,取个名字然后把内容粘进去，保存即可。

配置 Hexo 部署到 Github
修改 Hexo 配置文件即可 如下

1
2
3
4
5
6
7
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy: #部署插件设置(目前只自动部署到github)
  type: git
  repo: git@github.com:azuldancing/azuldancing.github.io.git # 你新建的Github项目地址(用户名.github.io)
  branch: master
  message: '站点更新: {{ now("YYYY-MM-DD HH:mm:ss") }}' #每次部署后更新信息
部署到Github

1
2
# 执行以下命令将自动更新到Github
hexo d