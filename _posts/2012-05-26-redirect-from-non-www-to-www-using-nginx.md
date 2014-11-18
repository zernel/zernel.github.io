---
layout: post
title: "使用nginx进行www域名跳转"
category: Technology
tag: Deployment
---

平时我们访问的www开头的其实都是二级域名，我们从域名注册商处买到的顶级域名是不带www开头的，我们也称顶级域名为裸域名，当裸域名和www开头的二级域名同时作为首页时可能会导致cookies处理出错，因为cookies是区分域名的，顶级域名和二级域名会认为是两个不同的域名，同时这样也不利于SEO。

对此，我们有一种简单的解决方案，只要当用户使用域名访问时自动跳转回www开头的二级域名就可以了，现在有不少网站都是采用这种做法的，例如[36氪](http://36kr.com/)、[豆瓣](http://douban.com)等。那我们应该怎么样做呢？

我们可以简单地配置nginx进行域名的跳转，在配置里写两个server，第一个server使用带www的二级域名

    server {
      listen 80;
      server_name www.bayspaceevents.com;
      ...

然后在后面加上

    server {
      server_name my-domain-name.com;
      rewrite ^(.*) http://www.my-domain-name.com$1 permanent;
    }

就这样，当你访问`my-domain-name.com`时会自动跳转到`www.my-domain-name.com`，希望能帮到大家：）

参考资料：
* [nginx 301跳转域名到带www的上面](http://shangpan.com/archives/194.html)
* [使用nginx进行裸域名的跳转](http://blog.meiweier.com/2010/01/23/redirect-from-non-www-to-www-using-nginx.html)
