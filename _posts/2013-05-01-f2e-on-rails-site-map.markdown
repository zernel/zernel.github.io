---
layout: post
title: "为 F2E 设计简单的 Rails 站点地图"
category: Technology
tags: Rails, Front-end
---

每个 Rails 团队的 Front End Engineer(F2E) 根据具体分工的不同因而或多或少地需要接触一些后台的知识，就我们团队而言主要需要了解的是：

1. Views 的[渲染机制](http://guides.rubyonrails.org/layouts_and_rendering.html)（layouts, template, partial 三层之间的关系）
2. Erb 的基本写法，简单的要能写，复杂点的（[Form](http://guides.rubyonrails.org/form_helpers.html)）至少要能看懂。
3. Rails 的[路由机制](http://guides.rubyonrails.org/routing.html)，可以通过`config/routes.rb`和`rake routes`找到相应页面所在的文件位置。

上面3项相对比较难理解是路由部分，尤其是`resources`的概念对于初学者会比较难理解，针对这种情况，我简单设计了一个站点地图(doc/site-map.yml)

    root:
      name: "Home page"
      url: "/"
      route_helper: root_url
      files:
        layout: "layouts/application" # By default
        header: "shared/headers/main"
        template: "sites/index"

    contact:
      name: "Contact Us"
      url: "/contacts/new"
      route_helper: new_contact_url
      files:
        header: "shared/headers/main"
        template: "contacts/new"

    sport_facility:
      overview:
        name: "Facility Overview"
        url: "/sport-facilities"
        route_helper: sport_facilities_url
        files:
          header: "shared/headers/sport_facility"
          template: "sport_facilities/overview"
      basketball_courts:
        name: "Basketball Courts"
        url: "/sport-facilities/basketball-courts"
        route_helper: basketball_courts_url
        files:
          header: "shared/headers/sport_facility"
          template: "sport_facilities/basketball_courts"
      ball_hockey:
        name: "Ball Hockey"
        url: "/sport-facilities/ball-hockey"
        route_helper: ball_hockey_url
        files:
          header: "shared/headers/sport_facility"
          template: "sport_facilities/ball_hockey"

主要有以下几个作用：

* 在项目初期就先去写这份文档，在一定程度上可以增加我们对项目的整体认识，同时也可以在这个过程设计出对 SEO 友好的 URL(在项目中后期再设计的话会花更多的时间)。
* 把项目所有页面都结构化地整理了出来，刚接触这个项目的成员可以通过这个站点地图很快能对这个项目的结构有宏观的了解。
* 建立了页面名字、页面 URL、生成该 URL 的 Helper 方法还有这个页面的相关文件之间的联系，这样 F2E 就不需要去理解`config/routes.rb`的代码，可以直接通过这份文档去找到他们需要做的页面位置，他们在写链接的时候也可以直接用上面的route_helper，我们修改了页面的结构之类的了只需要维护这份文档就可以省去他们的很多疑问。

也许通过团队之间必要的沟通和互动可以很好地简化上述的过程，并不需要维护这样的一份文档，不过以我们团队的情况而言，我们是以远程协助开发为主，由于时差的问题，团队成员并不能全部在同一时间内工作，因而必要的规范可以节省高额的沟通成本。这个只是我们为了提高团队开发效率所做的一种摸索，欢迎交流。
