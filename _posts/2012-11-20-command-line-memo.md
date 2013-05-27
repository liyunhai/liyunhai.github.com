---
layout: post
title: "常用命令行备忘"
category: commandline
tags: [command line]
---
{% include JB/setup %}

## OS命令行
	df -hT    //查询磁盘分区使用状况
	du -hs ./Share    //查询目录占用磁盘空间

## Ruby(On Rails)命令行
	rails new projectname    //创建一个新的rails项目
	rails command -h    //查看rails所有命令的参数
	rails server    //启动rails web服务器
	rails generate scaffold Vendor column:type
	rake db:migrate RAILS_ENV=production

## 其他命令行
	rake post title="Hello World"    //Jekyll-Bootstrap中创建一个Post

## 疑难问题命令行

### mysql数据库root用户开启远程访问设置
	sudo vi /etc/mysql/my.cnf 
	#bind-address = 127.0.0.1 (comment)
	
	Method 1:
	mysql -u root -p
	use mysql;
	update user set host='%' where user='root' and host='localhost';
	select host, user from user;
	flush privileges;
	
	Method2:
	grant all privileges on *.* to 'root'@'%' with grant option;
	grant all privileges on *.* to 'root'@'%' identified by 'password' with grant option;
	grant all privileges on *.* to 'root'@'192.168.1.100' identified by 'password' with grant option;
	flush privileges;
	
### Mac上制作Ubuntu USB启动安装盘
	$ hdiutil convert -format UDRW -o /path/to/generate/img/file /path/to/your/iso/file //将iso文件转换为img文件
	$ diskutil list //查看USB的盘符
	$ diskutil unmountDisk /dev/disk1 //卸载USB磁盘
	$ sudo dd if=ubuntu.img.dmg of=/dev/rdisk1 bs=1m //将镜像写入USB
	$ diskutil eject /dev/disk1 //弹出USB磁盘
	
### Ubuntu系统设定静态IP
	sudo vi /etc/network/interfaces
	//iface eth0 inet dhcp
	# The primary network interface
	iface eth0 inet static
	address 192.168.1.10
	netmask 255.255.255.0
	gateway 192.168.1.1
	
	sudo vi /etc/resolv.conf //设置DNS
	nameserver 202.96.134.133
	nameserver 202.106.0.20
	
	sudo ifdown eth0
	sudo ifup eth0
	
### Ubuntu系统修改主机名
	sudo vi /etc/hosts
	127.0.1.1 newhostname
	
	sudo vi /etc/hostname
	newhostname
	
	hostname newhostname
	

