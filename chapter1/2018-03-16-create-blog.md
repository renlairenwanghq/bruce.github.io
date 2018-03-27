---
layout:     post
title:      "github pages"
subtitle:   " \"这世界我来了\""
date:       2018-03-15 19:00:00
author:     "bruce"
header-img: "img/head1.jpg"
catalog: true
tags:
    - 生活
---

## 使用github pages建立自己的博客  

### 1.简介
GitHub是一个面向[开源](https://baike.baidu.com/item/%E5%BC%80%E6%BA%90/20720669)及私有[软件](https://baike.baidu.com/item/%E8%BD%AF%E4%BB%B6/12053)项目的托管平台，因为只支持git 作为唯一的版本库格式进行托管，故名GitHub。

 GitHub pages 是Github提供的一个服务，我们可以免费的在上面搭建自己的网站，所以很多人利用GitHub pages作为自己的个人博客站点。


### 2搭建方式

 * 开通自己的`github.io` repo
 * 设置jekyll主题
 * 发布博客

#### 2.1 开通github帐号

 访问官网地址，开通github帐号。gitbub官网：https://github.com/

#### 2.2 创建repo

 创建新的repo， 注意Repository name的格式保持**格式<username.github.io** 其中<username与你的github账户名一致。

 

 ![](../img/2018-03-16-create-blog/user-repo@2x.png)

#### 2.3 编写简单的博客首页

 其实经过上一步，已经存在博客地址了，博客的地址格式为`https://<username.github.io`，但是需要自己便也博客的页面。在这个库中完全可以只上传一个`index.html`，但是这样会丧失很多灵活性。

 * 将创建的repo clone到本地

   ```
   $ git clone https://github.com/tobiasalin/tobiasalin.github.io
   ```

 * 编写博客的首页页面

   ```
   $ cd tobiasalin.github.io
   $ echo "Hello World!"  index.html
   ```

 * 提交到远程的仓库

   ```
   $ git add index.html
   $ git commit -m "Init commit"
   $ git push origin master
   ```

   此时打开博客地址https://<username.github.io，发现页面为index.html展示的页面。

   ​

#### 2.4 设置jekyll主题 

 Jekyll 是目前非常流行的静态网站生成器(static website generator)。静态网站即只包含HTML, CSS 和 Javascript，`github.io`默认采用`Jekyll`作为建站工具。`Jekyll`已经有一个非常庞大的社区，这就意味着，你完全可以借鉴别人已经造好的轮子。

 * 首先下载别人已经设计好的主题

   ```
   $ bcompare huxpro.github.io/ renlairenwang/renlairenwanghq.github.io/
   ```

 * 将别人已经写好的主题放到自己的repo中，我是通过becompare对比合入的，也可以直接拷贝到自己的目录下，除了 `.git` `.gitignore` `LICENSE`  `README.MD` 这些原有文件外，其他都可以拷贝到自己目录。

   ```
   $ git clone https://github.com/Huxpro/huxpro.github.io.git
   ```

 * 提交到自己的远端repo

   ```
   $ git add .
   $ git commit -m "Init commit"
   $ git push origin master
   ```

 此时访问博客主页`https://<username.github.io` ，显示的内容即为一个已经相对完善的博客页面。

 * 针对自己的博客，做针对性修改

   目录结构中主要文件介绍：

   _config.yml 是配置文件，最为重要，包含了所有配置信息

   _includes 文件夹包含了将被反复利用的文件，比如footer，header

   _layouts 文件夹包含了主页面的排版布局

   _posts 文件夹将包含所有的日志文件，Markdown格式

   ​

   修改`_config.yml`文件

   ```
   # Site settings

   title: 窗外蟋蟀
   SEOTitle: 窗外蟋蟀的博客 | Keysaim Blog
   header-img: img/home-bg-hill.jpg
   email: keysaim@gmail.com
   description: "描述"
   keyword: "窗外蟋蟀, keysaim"
   url: "https://keysaim.github.io"
   baseurl: ""
   ...
   ```

   ​

   修改结束之后提交修改

   ```
   $ git add .
   $ git commit -m "modify"
   $ git push origin master
   ```

   ​

#### 2.5 书写自己的博客

   进入_posts目录，新建一个markdown文件即可编辑，编辑结束同上述方式提交即可。

   ```
   $ cd _posts
   $ vi tmp.markdown
   $ git add .
   $ git commit -m "modify"
   $ git push origin master
   ```

   ​

参考链接：

[github pages部署静态网页](http://www.cnblogs.com/JsonShare/p/5522473.html)    

[jekyll介绍](https://www.jianshu.com/p/3f355c7872d5) 

[创建jekyll主题](http://blog.csdn.net/garfielder007/article/details/50224513)

[如何使用Github Pages免费搭建网站](https://www.jianshu.com/p/6cabb41495c8)

[如何快速搭建自己的github.io博客](http://blog.csdn.net/Walkerhau/article/details/77394659)



