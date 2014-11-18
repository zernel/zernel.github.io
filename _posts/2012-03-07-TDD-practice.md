---
layout: post
title: "浅淡Rails测试驱动开发"
category: Technology
tags: Rails Test
---

最近在做毕设，打算开发一个专门用来发起聚会的平台，我把项目命名为[ruby-tuesday](https://github.com/zernel/ruby-tuesday)，开源在Github上，打算做完第一个版本应付毕设后觉得还可以的话就继续开发下去，不然就当给初学者用来研究也好，希望最后能把它上线使用吧XD。

之前在公司开发时是用BDD，但这个项目我感觉需求还是比较明确，而且毕设也有点赶，所以就把模拟用户行为进行需求分析这部分去掉，用[Mockup](http://balsamiq.com/products/mockups)大概分析了一下需求就开始写验收测试，也算是TDD了，上个周末搭好环境实现了一个scenario，今天就打算借这个作为例子描述一下TDD的开发过程。这个测试用例只是第一个版本，当时写得匆忙还有很多条件没有考虑到，测试也缺乏完备性，不过只是借它来描述一下我所理解的TDD开发思想，欢迎大家拍砖。

这个scenario要实现的场景是一个已注册的用户要发起一次聚会，用户要完成这个场景首先要访问创建聚会这个页面，然后在这个页面中创建聚会的表单里面填写必要的聚会信息，然后点击创建，就这样完成一个聚会的创建。我们的验收测试要模拟用户的这个过程，然后断言聚会是否已经成功创建。在写验收测试的过程其实也是我们在设计的过程，当我们写完验收测试后我们会很清晰我们现在要做什么，代码如下：

    #encoding: UTF-8
    require 'spec_helper'

    feature 'create party' do
      background do
        user = FactoryGirl.create(:user)
        login_as(user, :scope => :user)
      end

      scenario 'by user' do
        visit new_party_path

        within('#new-party') do
          #select('杭州', :from => 'district')
          fill_in 'party_topic', with: 'Javascript MVC'
          fill_in 'party_presenter', with: '@nowazhu'
          fill_in 'party_date', with: '2012年03月06日（周二） 晚上7:00点至8:30'
          fill_in 'party_address', with: '塘苗路18号华星产业园A座 4 楼'
          fill_in 'party_introduction', with: '欢迎 ruby 爱好者参加！'
          click_on('创建聚会')
        end

        Party.all.should have(1).item
      end
    end

这里定义的一些id,class或者input-name都是根据需求去设定好，并不是页面已经存在的，现在页面还不存在，例如前面写的`within('#new-party')`只是假定有一个叫这个id的表单或作用域，用户在这个作用域填写必要的信息，接下来就可以跑一下测试，结果如下图：
![TDD00](/assets/images/posts/tdd-00.png)

上面的测试结果是告诉我们找不到`new_party_path`这个路径，这时候我们就应该去给它配一下route，在`config/routes.rb`中加入`resources :parties`，然后我们再跑测试，结果如下图

![TDD01](/assets/images/posts/tdd-01.png)

这里的测试结果是告诉我们PartiesController这个对象还没进行实例化，现在我们就知道我们要建一个PartiesController的控制器，这时候我们就应该开始写controller的测试，用户要发起一个聚会首先会进入到一个创建聚会的页面，对应RESTful的new方法，我们现在应该写这个方法的测试，要测试这个方法我们首先要构造一个环境，有权限要进入到这个页面肯定是已登录的用户，然后在这个方法里面应该会调用new方法创建了一个Party的实例，在这个方法中我们只需要断言有调用到这个方法并返回对应的状态就行。

就这样由测试一步步驱动你去开发，当然如果我们熟悉的话在这个过程思路会很清晰，不用每一步都跑测试，只在你困惑的时候跑一下测试，测试结果就会告诉你还有哪里没做好，下面的步骤我就不这么一步步地说明了，女朋友最近在嫌我啰嗦XD，我把整个思路说一下，有兴趣的朋友看下代码应该就懂了，遇到问题欢迎给跟我联系。下面是控制器的测试：

    require 'spec_helper'

    describe PartiesController do
      login_user
      before(:all) do
        @party = Party.new
      end
      let(:party) { double(:party) }

      describe 'GET new' do
        before(:each) do
          Party.should_receive(:new).and_return(@party)

          get :new, format: :json
        end

        it { should respond_with :ok }
        it { should respond_with_content_type /json/ }
      end
    end

在控制器的测试中一般只测跳转有没有正确就可以了，至于里面调用的方法是否正确就由单元测试去保证，这样可以避免重复测试，所以我们在这里只是测方法有没有正确被调用，而调用返回的结果是由我们自己去构造的。如果我们这个方法需要调用其他方法去完成这个功能的话我们在这里可以用OO的思想设计一下接口，看下应该调用哪个对象的方法去实现，正如前面所说，我们可以在这里只需要断言这个方法有被正常的调用，然后设定它的返回值，等控制器的测试写好后就可以开始写这个方法的单元测试了。


我们现在要实现的这个场景暂时还不需要写单元测试，我们只是调用到new方法，这个方法的正确性已经由rails去帮我们保证了，我们要相信只要按它提供的接口去调用就能返回我们想要的值就行了，所以像平常用到的一些gem提供的方法一般不需要另外写单元测试，但要通过验收测试或整合测试去保证这些方法配合使用的结果是我们预期的。


当你写完单元测试后就可以开始正式写实现了，你写过单元测试你会很清楚这个方法要做些什么事，等你把单元测试跑过后你就可以再回头跑控制器的测试，它会提示你要去实现控制器的方法，你就以自己的思路为主，测试的提示为辅去写控制器的实现。



等你控制器的测试跑过后，你再回头跑验收测试，它会提示你找不到parties#new这个页面，这时你再创建页面再跑，它就会提示你找不到#new-party这个元素，然后你就按当初的设定把表单写好后它会提示你断言没有通过，因为聚会没有创建成功，你只是能让它输入创建聚会的必要信息发到后台，但后台实现创建聚会的功能还没写，这时我们就要进入下一轮迭代，像刚才实现new这个方法一样去实现create方法，也是先写控制器测试，再写单元测试，再实现model的方法实现单元测试，再回头跑控制器测试，再实现控制器对应代码，再回头跑验收测试，一直这样迭代下去，才能控制好开发的节奏，代码的质量也能得到一定保证。

具体实现的代码在[Github](https://github.com/zernel/ruby-tuesday)上，有兴趣的朋友可以看一下。上面只是我在开发中的一些体会，如果大家有什么问题的话，欢迎交流学习。
