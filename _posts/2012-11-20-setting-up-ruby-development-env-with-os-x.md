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

## 安装Homebrew
	ruby -e "$(curl -fsSkL raw.github.com/mxcl/homebrew/go)"

## 安装最新版本Ruby
	brew install ruby

## 增加Ruby路径到$PATH环境变量
	vi ~/.bash_profile
	export PATH=/usr/local/Cellar/ruby/1.9.3-p327/bin:$PATH

## 安装Rails
	gem install rdoc
	gem install rails

## 安装FFmpeg
### 下载FFmpeg Mac OS X Build
	http://ffmpeg.org/download.html
### 拷贝FFmpeg到/usr/bin目录
	sudo cp ffmpeg /usr/bin

## 安装STREAMIO-FFMPEG
	gem install streamio-ffmpeg

## 修改transcoder.rb以解决seek_time参数后置引起的性能问题
	#command = "#{FFMPEG.ffmpeg_binary} -y -i #{Shellwords.escape(@movie.path)} #{@raw_options} #{Shellwords.escape(@output_file)}"
	seek_time = @raw_options.delete(:seek_time)
	seek_time = '-ss %d' % seek_time if seek_time
	command = "#{FFMPEG.ffmpeg_binary} -y #{seek_time} -i #{Shellwords.escape(@movie.path)} #{@raw_options} #{Shellwords.escape(@output_file)}" 
	
## RMagick安装
	brew install imagemagick
	gem install rmagick
