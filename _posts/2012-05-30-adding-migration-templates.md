---
layout: post
title: "Migration Template抛出NotImplementedError"
category: Technology
tag: Rails
---

今天想利用项目的空闲时间把现在项目中的Authentication部分抽出来写成Gem，名叫[simple_login](https://github.com/zernel/simple_login),因为这个项目在登录认证方面的业务逻辑比较特殊，需要有很强的可定制性，先后尝试了[devise](https://github.com/plataformatec/devise),[clearance](https://github.com/thoughtbot/clearance)和[omniauth-identity](https://github.com/intridea/omniauth-identity)这几种认证方案都不能达到想要的效果，最后决定全部手工写。

考虑到以后项目可能也会有这样的需求，因而打算写成Generator，在写的过程中发现已经有人跟我有一样的想法并封装成[Gem](https://github.com/designium/simple-login)，不过我fork下来后发现里面存在些问题，而且考虑到我以后可能定制得比较多，所以我就直接基于它新建了一个gem，在调试的时候发现在使用`migration_template`迁移 migration模板时抛出了以下错误

    'next_migration_number': NotImplementedError (NotImplementedError)

经过debug后发现是由于我项目中还没有migration，所以在执行`next_migration_number`时找不到先前migration的时间截，最后通过重写了该方法解决

    def self.next_migration_number(dirname)
      if ActiveRecord::Base.timestamped_migrations
        Time.now.utc.strftime("%Y%m%d%H%M%S")
      else
        "%.3d" %(current_migration_number(dirname) + 1)
      end
    end

希望能帮到大家：）
