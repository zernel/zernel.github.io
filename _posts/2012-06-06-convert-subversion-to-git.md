---
layout: post
title: "把项目从svn迁移到git"
category: Technology
tags: Git
---

今天开始要为一个老项目进行二次开发，该项目当时用svn的，首要的任务是把它切换为git，用git提供的命令能很方便完成这个，现把迁移的过程分享一下。

### Create the authors file
首先创建一个`authors.txt`的文件，把该项目所有svn提交者的email和name映射到git，记得要列出所有有提交过代码的用户，不然一会迁移的过程中会出错，执行一次可能需要很长时间，半路中断会挺痛苦。文件内的格式如下：

    1  zernel = zernel <itzernel@gmail.com>
    2  svnuser2 = Another User <anotheruser@whatever.com>

### Clone the repository
下一步在跟刚刚创建的`authors.txt`同级目录下执行命令把远程的svn版本库导入为本地的git版本（假定你的svn版本库有标准的 /trunk,/tags 以及/branches）。

    ➜  ~  git svn clone <svn repo url> --no-metadata -A authors.txt -t tags -b branches -T trunk <destination dir name>

执行完后应该就能通过`git log`查看所有提交过的历史记录了。

### Convert branches to tags
接下来，我们还可以做进一步的处理。现在我们在svn下打的tags都已经变成git的分支了，我们需要手动把它们重新变回tags，再把对应的分支删掉。

我们可以执行`git branch -r`把它们列出来。

然后我们把需要切换成tags的分支执行以下命令进行处理(假设需要切换的分支名为`tags/tagname`)：

    ➜  ~  git tag tagname tags/tagname
    ➜  ~  git branch -r -d tags/tagname

### Push to a public repo (optional)
最后，我们可以把我们已经切换好的提交到已经创建好的git版本库中。
例如Github:
    ➜  ~  git remote add origin git@github.com:userid/project.git  
    ➜  ~  git push origin master --tags

Ps: 默认push的时候是不包含tags的，所以我们需要加上`--tags`选项。

这里有一篇从svn过渡到git的[教程](http://git.or.cz/course/svn.html)，有需要的也可以看下：）

参考资料：

*  [How to convert from Subversion to Git](http://pauldowman.com/2008/07/26/how-to-convert-from-subversion-to-git/)
