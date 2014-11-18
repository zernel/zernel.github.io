---
layout: post
title: "利用friendly_id对URLs进行SEO优化"
category: Technology
tag: Rails
---


根据RESTful的约定，我们经常会在URL上加上id来定位资源，而这样是不利于SEO的，为了增加我们网站被google、百度等搜索引擎收录的条目，我们可以对URL进行优化，[friend_id](https://github.com/norman/friendly_id)就是基于这样的需求产生的。


通过`friend_id`可以很简单地把

    http://zernel.github.com/post/3

替换成

    http://zernel.github.com/post/create-pretty-urls-by-friendly-id

## 官方Quickstart

直接安装gem

    gem install friendly_id

或者在`Gemfile`里面加入gem并执行`bundle install`

    gem "friendly_id", "~> 4.0.1"

用脚手架创建一个RESTful资源

    rails generate scaffold user name:string slug:string

修改`db/migrate/*_create_users.rb`为slug字段添加索引

    add_index :users, :slug, unique: true

然后执行`rake db:migrate`

修改`app/models/user.rb`

    class User < ActiveRecord::Base
      extend FriendlyId
      friendly_id :name, use: :slugged
    end

启动console并执行
    User.create! name: "Joe Schmoe"

启动服务器
    rails server

现在你就能通过`http://localhost:3000/users/joe-schmoe`访问到这个资源的`show`页面。

如果你是在一个已经在使用的项目中使用`friendly_id`，那你需要为已经存在的记录添加slugs，按照上面的例子，你可以打开console执行以下代码或者把它加到你的rake任务中

    User.find_each(&:save)

值得注意的是`friendly_id`的slugs不支持中文，这个问题暂时也没有特别推荐的解决方案，希望以后能有这方面的best practices。

## 其他做法

By the way, 实现以上这种friendly
URLs除了使用`friendly_id`外还有一种更便捷的方法，就是通过重写`ActiveRecord`的`to_param`来实现。

    class User < ActiveRecord::Base
      def to_param
        "#{name.strip.gsub(' ', '-')}"
      end
    end

然后我们就可以通过`http://localhost:3000/users/joe-schmoe`访问到这个资源，在这种做法中如果`name`这个字段有重复的话就会出问题，为了解决这问题我们可以在前面加上它的`id`来避免重复

    class User < ActiveRecord::Base
      def to_param
        "#{id}-#{name.strip.gsub(' ', '-')}"
      end
    end

这样刚才那个资源的访问路径就变成`http://localhost:3000/users/1-joe-schmoe`，相对起来没那么简洁。
