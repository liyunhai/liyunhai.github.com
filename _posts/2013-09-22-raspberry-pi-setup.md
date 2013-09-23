---
layout: post
title: "Raspberry Pi配置手册"
category: raspberry pi
tags: [raspberry, pi, 配置]
---
{% include JB/setup %}

## Raspbian安装与配置

### Raspbian镜像文件下载
http://www.raspberrypi.org/downloads

### Raspbian镜像文件写入（To SD Card）
	diskutil list //查看SD Card设备名称
	diskutil unmountDisk /dev/diskx //unmount SD Card
	sudo dd bs=1m if=wheezy-raspbian.img of=/dev/diskx //镜像文件写入SD Card

### Raspbian基本配置(菜单设置界面略)
	sudo vi /etc/network/interfaces //设置静态IP
	  ＃iface eth0 inet dhcp
	  iface eth0 inet static
	  address 192.168.1.x
	  netmask 255.255.255.0
	  gateway 192.168.1.1
	  #iface default inet dhcp

	sudo vi /etc/resolv.conf //设置DNS
	  nameserver 8.8.8.8
	
	sudo /etc/init.d/networking restart OR
	sudo /etc/init.d/networking reload
	
	sudo apt-get update

### 安装vim
	sudo apt-get install vim

### 添加用户并授权sudo
	sudo useradd liyunhai
	sudo visudo
	  liyunhai ALL=(ALL) NOPASSWD: ALL

### 自动获取外部IP地址
	mkdir util
	cd util
	touch oip.txt
	touch get_external_ip.log
	vi get_external_ip.py（内容略）
	sudo vi /etc/cron.d/get_external_ip
	  */5 * * * * liyunhai /home/liyunhai/util/get_external_ip.py > /home/liyunhai/util/get_external_ip.log 

## TSAR系统监控安装及配置
### 安装TSAR
	git clone https://github.com/alibaba/tsar.git
	cd tsar
	make
	sudo make install
	
### 配置TSAR
	sudo vi /etc/tsar/tsar.conf
	  mod_cpu on
	  mod_mem on
	  mod_swap off
	  mod_tcp off
	  mod_udp off
	  mod_traffic on
	  mod_io off
	  mod_pcsw off
	  mod_partition on
	  mod_tcpx off
	  mod_load off
	  mod_apache off
	  mod_lvs off
	  mod_haproxy off
	  mod_squid off
	  mod_nginx off
	  mod_swift off
	  mod_swift_store off
	  mod_swift_fwd off
	  mod_swift_code off
	  mod_tmd off
	  mod_percpu off
	  mod_tcprt off
	
	cd /etc/tsar
	sudo mkdir conf.d
	cd conf.d
	sudo vi rpi.conf
	  mod_rpi on
	  ####add it to tsar default output
	  output_stdio_mod mod_rpi
	
	
### 收集监控数据推送至YEELINK
	mkdir util
	cd util
	vi tsar_monitor.sh（内容略）
	sudo cp /etc/cron.d/tsar /etc/cron.d/tsar_monitor.sh
	vi /etc/cron.d/tsar_monitor
	  # cron tsar collect once per minute and send data to yeelink
	  # m h dom mon dow user command
	  * * * * * root /home/liyunhai/util/tsar_monitor.sh  > /dev/null 2>&1
	
## Samba服务安装与配置
### 安装Samba服务
	mkdir share
	chmod 775 -R ./share
	sudo apt-get install samba samba-common-bin
	sudo smbpasswd -a liyunhai

### 配置文件修改(/etc/samba/smb.conf)
	workgroup = HOME //for win7
	security = user //uncomment
	socket options = TCP_NODELAY //uncomment
	[homes] [printers] [print$] //comment
	[share]
	  comment = Shared Folder
	  path = /home/liyunhai/share
	  browseable = yes
	  writeable = yes
	  create mask = 0700
	  directory mask = 0700
	  public = yes
	  guest ok = no
	  valid users = liyunhai
	
### Samba服务重启
	sudo service samba restart

## Vsftp服务安装与配置

### 服务安装
	sudo apt-get install vsftpd

### 配置文件修改(/etc/vsftpd.conf)
	anonymous_enable=no
	local_enable=YES
	write_enable=YES
	local_umask=022
	idle_session_timeout=6000

### 服务启动
	sudo service vsftpd restart

## Aria2（迅雷离线）安装与配置
### 安装及配置Aria
	sudo apt-get install aria2
	mkdir ~/download
	mkdir ~/download/aria2
	mkdir ~/.aria2
	touch ~/.aria2/aria2.session
	vi ~/.aria2/aria2.conf（内容略）
	
### 安装及配置YAAW／Apache2
	sudo apt-get install apache2
	git clone https://github.com/binux/yaaw.git
	sudo cp -R ./yaaw /var/www/
	sudo chown www-data:www-data -R /var/www/yaaw/

### 加入Aria2到系统启动项目
	sudo vi /etc/init.d/aria2c（内容略）
	sudo chmod 755 /etc/init.d/aria2c
	sudo update-rc.d aria2c defaults

## Transmission-Daemon安装与配置

### 安装依赖库
	sudo apt-get install build-essential libssl-dev libcurl4-openssl-dev intltool pkg-config ca-certificates checkinstall
	wget https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
	tar zxvf libevent-2.0.21-stable.tar.gz
	cd libevent-2.0.21-stable
	./configure
	make
	sudo make install

### 安装Transmission-Daemon
	wget http://download-origin.transmissionbt.com/files/transmission-2.33.tar.bz2
	tar jxvf transmission-2.33.tar.bz2
	cd transmission-2.33
	./configure
	make
	sudo make install

### 相关目录创建（下载／配置／LOG）
	mkdir ~/download/completed
	mkdir ~/download/incomplete
	mkdir ~/download/torrents
	sudo mkdir /usr/local/share/transmission/config
	sudo mkdir /usr/local/share/transmission/logs

### 加入Transmission-Daemon到启动项目
	sudo vi /etc/init.d/transmission-daemon（内容略）
	sudo chmod 755 /etc/init.d/transmission-daemon
	sudo update-rc.d transmission-daemon defaults

### 修改配置文件及重新加载
	sudo vi /usr/local/share/transmission/config/settings.json（内容略）
	sudo service transmission-daemon reload

## Python环境安装与配置

### 基本环境安装
	sudo apt-get install python-dev
	sudo apt-get install mysql-server
	sudo apt-get install mysql-client
	sudo apt-get install libmysqld-dev
	sudo apt-get install mediainfo

### 安装及更新包管理工具
	sudo apt-get install python-pip
	sudo pip install -U pip
	
	sudo pip install -U distribute OR
	sudo easy_install -U distribute	
	
### 安装使用的Python库
	sudo pip install feedparser
	sudo pip install peewee
	sudo pip install flask
	sudo pip install CherryPy （废止）
	sudo pip install flask-peewee
	sudo pip install mysql-python
	sudo pip install pymediainfo （废止）

### mysql数据库root用户开启远程访问设置
	sudo vi /etc/mysql/my.cnf 
	#bind-address = 127.0.0.1 (comment)

	mysql -uroot -p
	use mysql;
	update user set host='%' where user='root' and host='localhost';
	select host, user from user;
	flush privileges;

### 数据库配置
	mysql -uroot -p
	create database res_auto character set utf8;
	create user 'res_auto'@'localhost' identified by 'no1Knows';
	grant all privileges on res_auto.* to 'res_auto'@'localhost';
	exit

### Github环境设置

#### over HTTPS
	git config --global user.name "liyunhai"
	git config --global user.email "liyunhai@cloudsoaring.com"
	git config --global credential.helper 'cache --timeout=3600'

#### over SSH
	ssh-keygen -t rsa -C "liyunhai@cloudsoaring.com"
>在GitHub的帐户设置中／SSH Keys中，追加一个SSH Key，把~/.ssh/id_rsa.pub的内容拷入。
