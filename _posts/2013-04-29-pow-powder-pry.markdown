---
layout: post
title: "3P（Pow & Powder & Pry）"
category: Technology
tags: Rails
---

好吧，我承认标题有点小邪恶，还是直入正题吧:-)

### Pow
[Pow](http://pow.cx)是[37signals](http://37signals.com)出品的一款简洁的 Rack 服务器，尤其在多应用同时开发时会更便捷。Pow 的安装很简单：

	curl get.pow.cx | sh
	cd ~/.pow
	ln -s /path/to/myapp

### Powder
现在你就可以通过`http://myapp.dev`打开我们的应用了。
同时，使用 [powder](https://github.com/Rodreegez/powder) 来管理 Pow 会更简洁。安装如下：
	
	gem install powder
	
安装后我们就可以在项目目录里面执行`powder link`，powder 会帮我们自动在 `~/.pow/<current_directory>` 的目录下生成快捷方式，接下来执行`powder open`就可以直接打开应用了。你可以在项目目录执行`powder`查看它的其他命令。

### Pry
在开发的过程中我们可以执行`powder applog`查看项目日志，另外我比较喜欢用 [Pry](https://github.com/pry/pry) 进行debug，在 Pry 的 [wiki](https://github.com/pry/pry/wiki/FAQ#wiki-pow) 上有说明如何跟 Pow 配合使用。

简单点说，就是先安装 [Pry-remote](https://github.com/mon-ouie/pry-remote) 这个 gem，然后在需要加断点的地方加上`binding.remote_pry`，等页面停留在断点的位置时就在项目的目录执行`pry-remote`，其他的就跟 Pry 完全一样了。

说到 Pry 顺带提一下 [jazz_hands](https://github.com/nixme/jazz_hands) 这个 gem，装了后在 pry 的终端里面显示的效果好很多 :-)
