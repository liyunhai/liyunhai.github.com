---
layout: post
title: "在Ubuntu Server上安装ChiliProject"
category: ubuntu
tags: [ubuntu, chiliproject]
---
{% include JB/setup %}

## Ubuntu环境设置
### 基本系统设置（用户，组，目录）
	sudo adduser --system --home /var/www/chiliproject --group --shell /bin/sh --disabled-login chiliproject

### 安装Apache，Ruby及基本依赖
	sudo apt-get update
	sudo apt-add-repository ppa:brightbox/ruby-ng
	sudo apt-get install apache2 ruby rubygems build-essential curl git subversion apache2-mpm-prefork mysql-server mysql-client
	sudo apt-get install libopenssl-ruby1.8 libmysqlclient-dev libpq-dev libsqlite3-dev libssl-dev zlib1g-dev libreadline6-dev libxml2-dev libapache2-mod-passenger libmagick++-dev ruby1.9.1-dev libxslt-dev

### 安装Bundle
	sudo gem install bundler

## 数据库配置
### 配置MYSQL（创建数据库，用户及赋予权限）
	mysql -uroot -p
	create database chiliproject character set utf8;
	create user 'chiliproject'@'localhost' identified by 'my_password';
	grant all privileges on chiliproject.* to 'chiliproject'@'localhost';
	exit

## ChiliProject安装
### 从GitHub下载及Checkout源代码
	cd /var/www/chiliproject/
	sudo git clone git://github.com/chiliproject/chiliproject.git
	cd chiliproject
	sudo git checkout stable
	
### 使用Bundle安装所有需要的Gems
	sudo bundle install （为加速，可修改gem source为http://ruby.taobao.org）

### 修改ChiliProject的数据库配置文件
	sudo cp config/database.yml.example config/database.yml
	sudo vi config/database.yml
	
	production:
	adapter: mysql2
 	database: chiliproject
 	host: localhost
 	port: 3306 # (not needed for socket connection)
 	username: chiliproject
 	password: my_password
 	encoding: utf8

### 修改ChiliProject的系统配置文件
	sudo cp config/configuration.yml.example config/configuration.yml
	
### 生成一个Session Store Secret
	sudo bundle exec rake generate_session_store

### 生成基本数据库结构
	sudo bundle exec rake db:migrate RAILS_ENV=production
	sudo bundle exec rake redmine:load_default_data RAILS_ENV=production

### 设置文件及目录权限
	sudo chown -R chiliproject:chiliproject /var/www/chiliproject
	sudo chmod -R 755 files log tmp public/plugin_assets

## Apache2 Web服务设置
### 设置Apache2的Passenger（mod_rails）
	sudo vi /etc/apache2/mods-enabled/passenger.conf
	
	<IfModule mod_passenger.c>
	  PassengerRoot /usr
	  PassengerRuby /usr/bin/ruby

	  PassengerDefaultUser chiliproject
	</IfModule>
	
	sudo a2enmod passenger
	sudo service apache2 restart

### 设置Apache2的虚拟主机
	sudo vi /etc/apache2/sites-available/chiliproject
	
	<VirtualHost _default_:80>
      ServerAdmin webmaster@localhost

      DocumentRoot /var/www/chiliproject/chiliproject/public
      <Directory /var/www/chiliproject/chiliproject/public>
        Options FollowSymLinks
        AllowOverride None
        Order deny,allow
        Allow from all
      </Directory>

      ErrorLog /var/log/apache2/error.log

      # Possible values include: debug, info, notice, warn, error, crit,
      # alert, emerg.
      LogLevel warn

      CustomLog /var/log/apache2/chiliproject.log combined
	</VirtualHost>

	sudo a2dissite default
	sudo a2ensite chiliproject
	sudo service apache2 restart