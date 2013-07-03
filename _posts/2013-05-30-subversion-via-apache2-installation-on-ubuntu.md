---
layout: post
title: "Subversion Via Apache2 Installation On Ubuntu"
description: ""
category: 
tags: []
---
{% include JB/setup %}

	sudo apt-get install subversion apache2
	sudo apt-get install libapache2-svn
	sudo adduser --home /home/svn --shell /bin/bash svn
	sudo usermod -G svn -a www-data
	sudo vi /etc/sudoers
		svn ALL=(ALL) ALL
	
	sudo su svn
	mkdir /home/svn/repos
	svnadmin create /home/svn/repos/yt_art_resource
	vi /home/svn/yt_art_resource/conf/svnserve.conf
		anon-access = none
		password-db = passwd //uncomment
	
	vi /home/svn/yt_art_resource/conf/passwd
		username = password
	
	svnserve -d --foreground -r /home/svn/repos
	
	sudo vi /etc/init.d/svnserve
	sudo chmod +x /etc/init.d/svnserve
	sudo update-rc.d svnserve defaults
	sudo /etc/init.d/svnserve start
	
