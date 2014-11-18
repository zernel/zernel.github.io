---
layout: post
title: "Ubuntu 12.10 下部署 Rails 应用"
category: Technology
tags: Deployment Linux Rails
---

## 部署说明
* 系统： Ubuntu 12.10 Server
* 服务器：Nginx + Passenger
* 数据库：PostgreSQL 9.2
* 用 RVM 进行 Ruby 版本控制

##  1. 创建 dev 用户

    adduser dev
    su dev

##  2. 安装基本插件，配置系统的 Locales

    sudo apt-get -y update
    sudo apt-get -y install curl git-core python-software-properties
    sudo apt-get -y install nodejs # Javascript Runtime

    # 配置 Locales
    apt-get install locales
    # 在.bashrc 添加下列代码
    export LANGUAGE=en_US.UTF-8
    export LANG=en_US.UTF-8
    export LC_ALL=en_US.UTF-8
    locale-gen en_US.UTF-8
    dpkg-reconfigure locales
    # 在.bash_profile 添加以下代码让其自动加载.bashrc
    if [ -f ~/.bashrc ]; then
    . ~/.bashrc
    fi

##  3. 安装 RVM & Ruby

    # RVM requirements
    sudo aptitude install build-essential openssl curl libcurl3-dev libreadline6 libreadline6-dev git zlib1g zlib1g-dev libssl-dev libyaml-dev libxml2-dev libxslt-dev autoconf automake libtool imagemagick libmagickwand-dev libpcre3-dev libsqlite3-dev


    curl https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer | bash -s stable
    echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm" # This loads RVM into a shell session.' >> ~/.bashrc
    rvm -v

    rvm install 1.9.3
    rvm use 1.9.3 --default

##  4. 安装 Passenger & Nginx

    gem install passenger

现通过passenger的nginx module来安装nginx，我习惯把nginx安装到`/etc/nginx`下，而不是默认的`/opt/nginx`，但当前用户并没有修改`/etc/nginx`的权限，而ROOT用户又没有安装该module的方法，所以需要切换到ROOT并同时改变ROOT下的PATH再安装。（若用默认路径，直接安装即可）

    # 把当前用户的PATH加到ROOT
    echo $PATH # 查看当前用户PATH并复制
    sudo passwd root
    su # 切换到root
    PATH=$PATH:xxxxxxxxxx # 把用户PATH复制到xxxx
    passenger-install-nginx-module # 安装 nginx ，位置选择到 /etc/nginx


配置类似Apache的目录结构（纯粹习惯）

    cd /etc/nginx/conf/
    在/etc/nginx/conf/nginx.conf最后添加include sites-enabled/*;
    sudo mkdir sites-enabled
    sudo mkdir sites-available


为nginx添加启动脚本，方便我们用ubuntu server的方式去管理


    cd /etc/init.d
    sudo wget https://raw.github.com/zernel/nginx-init-ubuntu-passenger/master/nginx
    sudo update-rc.d nginx defaults
    sudo chmod +x nginx
    sudo service nginx start

##  5. 安装 PostgreSQL 9.2
如果系统之前有装过 PostgreSQL 9.1 的话需要先把它删除才能装9.2。
（因为以前的项目在部署的时候装psql9.1的时候经常会遇到一个奇怪的问题，所以后来9.2出了后以后的项目都就直接装最新的）
如果之前是没有装过 PostgreSQL 的话可直接从第3步开始。

1.首先列出当前有装过哪些postgres的包

    sudo dpkg -l | grep postgres

2.如果看到在系统里已经装了以下这些包就把它们删除，这样就可以彻底移除 postgresql-9.1

    sudo apt-get --purge remove postgresql postgresql-9.1
    postgresql-client-9.1 postgresql-client-common postgresql-common
    postgresql-doc postgresql-doc-9.1

3.通过官方的库安装 postgresql-9.2

    sudo add-apt-repository ppa:pitti/postgresql
    sudo apt-get update
    sudo apt-get install postgresql-9.2
    # 如果你在添加官方库的时候出现`sudo: add-apt-repository: command not found`这个错误，你可以尝试先安装以下这个包。
    sudo apt-get install python-software-properties

4.执行`sudo netstat -lp | grep postgresql`检查一下 postgresql 是否设到默认的 5432 端口, 如果没有的话通过以下方式设置回默认端口

    sudo gedit /etc/postgresql/9.2/main/postgresql.conf
    port = 5432 # 在conf文件修改 port 的值
    sudo /etc/init.d/postgresql reload

##  6. 创建项目（在/var/www/apps）下

    sudo mkdir -p /var/www/apps/
    cd /var/www/apps/
    sudo chown dev:dev .
    git clone dev@git.xxx.xx:/repos/project_name.git
    cd project_name
    cp config/database.yml.psql config/database.yml
    bundle install

##  7. 配置Nginx

    cd /etc/nginx/conf/sites-available
    sudo vi project_name_production

    # Conf Example
    server {
            listen 80;
            server_name www.reggin.ca;
            root /var/www/apps/reggin/public;
            passenger_enabled on;
            rewrite "/hvac.htm" /services/hvac redirect;
            location  /icon/ {
                    alias  /var/www/apps/awstats/wwwroot/icon/;
            }
    }
    server {
            server_name reggin.ca;
            rewrite ^(.*) http://www.reggin.ca$1 permanent;
    }

    cd ../sites-enabled/
    sudo ln -s ../sites-available/project_name_production project_name_production
    sudo service nginx restart
