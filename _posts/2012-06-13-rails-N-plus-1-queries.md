---
layout: post
title: "Rails中使用Bullet优化N+1查询"
category: Technology
tags: Rails
---

今天在维护一个老项目的时候发现里面有不少地方出现N+1查询，ActiveRecord使用起来很方便，但很容易就会写出以下的代码：

### Model
    class User < ActieRecord::Base
      has_one :account
    end

    class Account < ActiveRecord::Base
      belongs_to :user
    end

### Controller
    def index
      @users = User.page(params[:page]).per(ADMIN_DATA_PER_PAGE)
    end

### View
    <% @users.each do |user| %>
      <%= user.account.balance %>
    <% end %>

我们在view中读取`user.account.balance`就会导致n+1查询，假如数据库中有10个user，就会发起11个查询，第一个是查出所有user，然后就是一个个user分别查询account，这样会严重拖慢性能。

    SELECT * FROM `users` LIMIT 10 OFFSET 0
    SELECT * FROM `accounts` WHERE (`users`.`id` = 1)
    SELECT * FROM `accounts` WHERE (`users`.`id` = 2)
    SELECT * FROM `accounts` WHERE (`users`.`id` = 3)
    ...
    ...
    ...
    SELECT * FROM `accounts` WHERE (`users`.`id` = 10)

我们要处理这个n+1查询也很简单，只需在controller中改成`@users = User.includes(:account).page(params[:page]).per(ADMIN_DATA_PER_PAGE)`

    SELECT * FROM `users` LIMIT 20 OFFSET 0
    SELECT * FROM `cars` WHERE (`users`.id IN('1','2','3','4','5','6','7','8','9','10'))


今天由于那个老项目中不知道有多少地方有n+1查询，所以我用[bullet](https://github.com/flyerhzm/bullet)来帮我检测，安装配置好这个gem后跑上面那个例子时可以在日志中看到以下信息：

    The request has unused preload associations as follows:
    None
    The request has N+1 queries as follows:
    model: User => associations: [account]

我就把项目中的主要页面跑一次，然后再到`log/bullet.log`里面一个个去修，虽然有点笨，不过管用，现在项目跑起来感觉快了不少，比较明显的是有一个页面里面有好几个n+1查询，没优化前跑起来是需要130ms左右，优化后变成差不多50m，那个action里面include了四个关联Model，其中有一个不是直接关联，写法如下：

    @auctions = Auction.includes(:event, :auction_status, [:vehicle => :user]).page(params[:page]).per(ADMIN_DATA_PER_PAGE)

有需要可以参考一下，希望能帮到大家：）
