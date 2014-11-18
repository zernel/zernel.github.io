---
layout: post
title: "通过对象数组生成路径"
category: Technology
tags: Rails
---

我们在使用Rails的路由辅助方法时，我们可以通过包含参数的数组来生成。假设你有以下路由：

  resources :magazines do
    resources :ads
  end

当你使用`magazine_ad_path`时，你可以传入两个实例来替代直接传入id。

    <%= link_to "Ad details", magazine_ad_path(@magazine, @ad) %>

同时你也可以使用`url_for`方法来替代，Rails会自动决定你想要的路由。

    <%= link_to "Ad details", url_for([@magazine, @ad]) %>

在这里，Rails会知道`@magazine`和`@ad`是`Magazine`和`Ad`的实例，因此会使用`magazine_ad_path`方法。
如果你使用的是`link_to`这个helper方法，你可以直接写对象数组，而不需要写`url_for`。

    <%= link_to "Ad details", [@magazine, @ad] %>

如果只有一个对象，可以连数组都不用写。

    <%= link_to "Magazine details", @magazine %>
