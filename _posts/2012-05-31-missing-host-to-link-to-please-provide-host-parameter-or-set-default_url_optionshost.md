---
layout: post
title: "Missing host to link to!"
category: Technology
tag: Rails
---

今天在调试使用`action_mailer`发邮件时出现了以下错误

    Missing host to link to! Please provide :host parameter or set
    default_url_options[:host] when sending emails.

原因是我没有给`action_mailer`配置默认的host

解决方案1：

可直接在`config/application.rb`中添加

    config.action_mailer.default_url_options = { :host => "localhost:3000" }

解决方案2：

在`application_controller.rb`中添加以下代码

    # application_controller.rb
    before_filter :mailer_set_url_options
    ...
    def mailer_set_url_options
      ActionMailer::Base.default_url_options[:host] = request.host_with_port
    end

问题解决：）
