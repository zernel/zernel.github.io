---
layout: post
title: "Ruby 中的 mixins 扩展"
category: Technology
tags: Ruby Rails mixins
---

通过 module 为类引入新的对象、方法会使我们的代码变得更加灵活，可扩展性更强，载入 module 一般使用 include 和 extend。

### include & extend

当你定义好一个 module 后，你可以在你的类定义中指明 include 这个 module，让 module 的实例方法和变量成为类本身的实例方法和类变量。而 extend 这个 module 会把对应 module 的实例方法作为类方法加入到类中。


    module Base 
     def class_type 
       “This class is of type: #{self.class}”
      end 
    end 
      
    class TestIncludeClass 
      include Base 
    end 
    
    class TestExtendClass
     extend Base
    end
       
    TestIncludeClass.new.class_type    #=>This class is of type: TestIncludeClass
    TestExtendClass.class_type    #=>This class is of type: Class

** P.S. include 只是引用了 module 的变量和实例方法，并不会把它们复制到类中，所有包含这个 module 的不同类都会指向同一个对象。 **

有时，我们希望能够在 module 里面同时定义类方法和实例方法，这时就可以通过`included`来实现。

    module Base  
     def hello  
        puts "This is a instance method!"  
      end  
   
      # 扩展类方法  
      def self.included(base)  
        base.extend(ClassMethods)  
      end  
     
      # 类方法  
      module ClassMethods  
        def hello  
        puts "This is a class method!"  
        end  
      end   
    end  
   
    class TestClass  
      include Base  
    end
    
    TestClass.hello #=> "This is a class method!"
    TestClass.new.hello #=> "This is a instance method!" 

在 Rails 中，我们可以很方便地使用 ActiveSupport::Concern 来简化这种实现，上述的 module 代码可写成：

    require 'active_support/concern'
    
    module Base
      extend ActiveSupport::Concern
      
      def hello  
        puts "This is a instance method!"  
      end
      
      module ClassMethods
        def hello  
          puts "This is a class method!"  
        end
      end
    end

### require & load

一般情况下，我们在开发的时候不一定会把 module 和 class 定义在同一个文件，为了方便管理，我们往往会把 module 定义在另一个文件，然后在类定义的文件中加载，这时就需要用到`require`和`load`。

require方法让你加载一个库，并且只加载一次，如果你多次加载会返回false。只有当你要加载的库位于一个分离的文件中时才有必要使用require。使用时不需要加扩展名，一般放在文件的最前面：

    require ‘test_library’


load用来多次加载一个库，你必须指定扩展名：

    load ‘test_library.rb’


#### 参考：

* [Ruby Require VS Load VS Include VS Extend](http://ionrails.com/2009/09/19/ruby_require-vs-load-vs-include-vs-extend/)
* [Ruby Mixin Tutorial](http://juixe.com/techknow/index.php/2006/06/15/mixins-in-ruby/)
* [ActiveSupport::Concern - Rails API](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html)

