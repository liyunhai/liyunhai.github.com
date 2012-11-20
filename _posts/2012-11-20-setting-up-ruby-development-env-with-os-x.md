---
layout: post
title: "在OS X下设置Ruby开发环境"
category: ruby
tags: [os x, ruby, 配置]
---
{% include JB/setup %}

##前提条件
* 从App Store安装最新版本XCode。
* 安装XCode Command Line Tools(XCode->Preferences->Downloads->Components->Command Line Tools)。

##安装Homebrew
	ruby -e "$(curl -fsSkL raw.github.com/mxcl/homebrew/go)"

##安装最新版本Ruby
	brew install ruby

##增加Ruby路径到$PATH环境变量
	vi ~/.bash_profile
	export PATH=/usr/local/Cellar/ruby/1.9.3-p327/bin:$PATH

