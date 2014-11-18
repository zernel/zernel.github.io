---
layout: post
title: "Brew Install Postgresql on OS X Lion"
category: Technology
tags: Database
---

## Installion

安装[homebrew](http://mxcl.github.com/homebrew/)

执行`brew install postgresql`，DONE!

## Problem

安装完后我尝试进入psql时遇到以下错误

    psql: could not connect to server: Permission denied
        Is the server running locally and accepting
        connections on Unix domain socket "/var/pgsql_socket/.s.PGSQL.5432"?

这是因为当前用户没有权限启动psql服务器，当我尝试创建用户时

    createuser: could not connect to database postgres: could not connect to
    server: Permission denied
        Is the server running locally and accepting
        connections on Unix domain socket "/var/pgsql_socket/.s.PGSQL.5432"?

## Solution

经过询问全能的google，找到一个简单的处理方法，只要执行以下指令就可以了。

    curl http://nextmarvel.net/blog/downloads/fixBrewLionPostgres.sh | sh

以上指令所执行的脚本如下：

    BREW_POSTGRES_DIR=`brew info postgres | awk '{print $1"/bin"}' | grep "/postgresql/"`
    LION_POSTGRES_DIR=`which postgres | xargs dirname`
    LION_PSQL_DIR=`which psql | xargs dirname`
     
    sudo mkdir -p $LION_POSTGRES_DIR/archive
    sudo mkdir -p $LION_PSQL_DIR/archive
     
    for i in `ls $BREW_POSTGRES_DIR`
    do
        if [ -f $LION_POSTGRES_DIR/$i ]
        then
            sudo mv $LION_POSTGRES_DIR/$i $LION_POSTGRES_DIR/archive/$i
            sudo ln -s $BREW_POSTGRES_DIR/$i $LION_POSTGRES_DIR/$i
        fi
     
        if [ -f $LION_PSQL_DIR/$i ]
        then
            sudo mv $LION_PSQL_DIR/$i $LION_PSQL_DIR/archive/$i
            sudo ln -s $BREW_POSTGRES_DIR/$i $LION_PSQL_DIR/$i
        fi
    done

问题解决，希望能帮到大家：）

参考资料：

*  [Installing PostgreSQL With Brew on Mac OS
   X](http://zanshin.net/2011/02/17/installing-postgresql-with-brew-on-mac-os-x/)

*  [Brew Install Postgresql on OS X
   Lion](http://nextmarvel.net/blog/2011/09/brew-install-postgresql-on-os-x-lion/)
