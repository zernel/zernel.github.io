---
layout: post
title: "Ruby 中的 作用域"
category: Technology
tags: Ruby scope
---

《Ruby 元编程》学习笔记

**作用域**可以理解成变量在此位置上的可视区域。一般而言，可运行的代码是由代码体本身及一组 *Binding* 组成的，*Binding* 就是一个用对象表示的完整作用域，它们之间的关系可见下图：

![Binding]({{ site.url }}/assets/images/scope-in-ruby.png)

那么，怎么样才算是一个作用域呢？Ruby 会在以下三种情况关闭前一个作用域，同时打开一个新的作用域：

* 类定义
* 模块定义
* 方法

这三种情况分别用 **class**, **module** 和 **def** 作为关键词，这几个关键词也可以称为**作用域门(Scope Gate)**。请看以下示例代码：

    v1 = 1
    
    class MyClass  # 作用域门：进入class
      v2 = 2
      local_variables  #=> [:v2]
      
      def my_method  # 作用域门：进入 def
        v3 = 3
        local_variables  #=> [:v3]
      end  # 离开 def
      
      local_variables  #=> [:v2]
    end  # 离开 class
    
    obj = MyClass.new
    obj.my_method #=> [:v3]
    obj.my_method #=> [:v3]
    local_variables #=> [:v1, :obj]

上述的代码中执行的两次 my_method，第一次执行时会打开一个。Ruby 不像 Java 那样有内部作用域（inner scope）和外部作用域（outer scope）的概念，它一旦进入一个新的作用域，原来的 *Binding* 就会被一组新的 *Binding* 替换。也就是说上述的代码总共创建了4个独立的作用域：顶级作用域、进入 MyClass 时创建的作用域和两次执行 my_method 时创建的作用域。

正如上面所说，我们在切换作用域的时候会把原来的 *Binding* 也一同替换，也就是说我们进入一个新的作用域时局部变量会即时失效，但往往我们想要在不同的作用域中共用局部变量，如下面的例子：

    my_var = "Success"
  
    class MyClass
      # 你想要在这里得到 my_var
    
      def my_method
        # 还有这里
      end
    end
  
在这种情况下，我们想要让 *Binding* 跨越多个不同的作用域门从而共享局部变量，我们可以把作用域门的关键字替换为非作用域门的东西： `Class.new` 代替 `class`，`Module.new` 代替 `module`，还有用 `Module#define_method()` 代替 `def`，这样我们就可以在闭包中取得局部变量的值，然后把这个闭包传递给替换后的方法。如下：

    my_var = "Success"
    
    MyClass = Class.new do
      puts "#{my_var} in the class definition!"
      
      define_method :my_method do
        puts"#{my_var} in the method!"
      end
    end
    
    MyClass.new.my_method
    
    => Success in the class definition!
       Success in the method!
       
这样的方法称做**扁平化作用域(flattening the scope)**。另外我们也可以在一个扁平作用域中定义多个方法，则这些方法可以用同一个作用域门进行保护，并共享绑定，这种技术叫做**共享作用域**。示例如下：

    def define_methods
      shared = 0
      
      Kernel.send :define_method, :counter do
        shared
      end
      
      Kernel.send :define_method, :inc do |x|
        shared += x
      end
    end
    
    define_methods
    
    counter  #=> 0
    inc(4)
    counter  #=> 4

