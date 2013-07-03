---
layout: post
title: "Ubuntu Server配置手册"
category: ubuntu
tags: [ubuntu, 配置]
---
{% include JB/setup %}

## Ubuntu安装与配置

### 安装版本选择
Ubuntu Server 12.04 LTS (ubuntu-12.04-server-amd64.iso)

### 安装服务选择
* OpenSSH Server
* LAMP Server
* PostgreSQL Database
* Samba File Server

## Crontab设置(自动获取外部IP地址)
	crontab -e
	*/5 * * * * /home/liyunhai/util/net_stat.py >>/home/liyunhai/util/net_stat.log 

## Transmission-Daemon安装与配置

### 服务安装
	sudo apt-get update
	sudo apt-get install transmission-common transmission-cli transmission-daemon
> Ubuntu Server 12.04 LTS软件仓库里的transmission-daemon版本为2.5.1（2.6.1），WebUI非常丑陋，所以退回到版本2.3.3。（需要libminiupnpc5&libnatpmp1）

### 目录创建
	mkdir ~/Downloads
	mkdir ~/Downloads/Completed
	mkdir ~/Downloads/Incomplete
	mkdir ~/Downloads/Torrents

### 权限配置
	sudo usermod -a -G debian-transmission liyunhai
	sudo chgrp -R debian-transmission ~/Downloads
	sudo chmod -R 775 ~/Downloads

### 配置文件修改(/etc/transmission-daemon/settings.json)
	"download-dir": "~/Downloads/Completed",
	"incomplete-dir": "~/Downloads/Incomplete",
	"incomplete-dir-enabled": true, 
	"rpc-whitelist-enabled": false,
	"rpc-password": "password",
	"watch-dir": "~/Downloads/Torrents",
	"watch-dir-enabled": true
> 实际配置文件中不可使用~/代表用户目录，需要列出完整路径。

### 配置文件重新加载
	sudo service transmission-daemon reload

## Samba服务安装与配置

### 配置文件修改(/etc/samba/smb.conf)
	#add lines
	[share]
	  comment = Shared Folder
	  valid users = liyunhai
	  path = /home/liyunhai/Share
	  browseable = yes
	  read only = no
	  create mask = 0700
	  directory mask = 0700
	  guest ok = no

### 服务重启
	sudo service smbd restart

## Vsftp服务安装与配置

### 服务安装
	sudo apt-get install vsftpd

### 配置文件修改(/etc/vsftpd.conf)
	anonymous_enable=no
	local_enable=YES
	write_enable=YES
	local_umask=022
	idle_session_timeout=6000
	pam_service_name=ftp (Ubuntu 12.04版本BUG)
	
	chroot_local_user=YES
	allow_writeable_chroot=YES

### 服务启动
	sudo service vsftpd restart

## Ruby on Rails环境安装与配置

### GCC编译环境安装
	sudo apt-get install build-essential

### Git安装
	sudo apt-get install git-core

### Ruby安装(ver:1.9.3)
	sudo apt-get install ruby1.9.3

### RubyGems更新
	sudo gem update --system (Debian系统上被禁止执行)
	sudo gem install rubygems-update
	sudo update_rubygems

### Rails安装
	sudo gem install rails

### 数据库驱动安装
	sudo apt-get install libsqlite3-dev
	sudo gem install sqlite3
	sudo apt-get install mysql-server mysql-client
	sudo apt-get install libmysql-ruby libmysqlclient-dev
	sudo gem install mysql

### Javascript Runtime安装
	sudo apt-get install nodejs

### FFMPEG安装
	sudo apt-get install ffmpeg

### STREAMIO-FFMPEG安装
	sudo gem install streamio-ffmpeg

### 修改transcoder.rb以解决seek_time参数后置引起的性能问题
	#command = "#{FFMPEG.ffmpeg_binary} -y -i #{Shellwords.escape(@movie.path)} #{@raw_options} #{Shellwords.escape(@output_file)}"
	seek_time = @raw_options.delete(:seek_time)
	seek_time = '-ss %d' % seek_time if seek_time
	command = "#{FFMPEG.ffmpeg_binary} -y #{seek_time} -i #{Shellwords.escape(@movie.path)} #{@raw_options} #{Shellwords.escape(@output_file)}" 

### RMagick安装
	sudo apt-get install imagemagick
	sudo apt-get install graphicsmagick-libmagick-dev-compat libmagickwand-dev
	sudo gem install rmagick

### ImageScience安装
	sudo gem install image_science

## Python(Django)环境安装与配置

### 确认Python安装及版本
	python -V

### 安装pip
	sudo apt-get install python-pip
	sudo pip install -U pip

### 安装Django
	sudo pip install Django
	
### 安装MySQL For Python
	sudo pin install -U distribute (Ubuntu12.04.2上的distribute版本不满足要求)
	sudo apt-get install python-dev libmysqlclient-dev
	sudo pip install MySQL-python	

	


