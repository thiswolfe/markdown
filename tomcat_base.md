---
title: Tomcat的使用
description: Tomcat 服务器是一个免费的开放源代码的Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP 程序的首选。
tag:
 - tomcat
categories:
 - linux-tools
---

# wsl 安装 tomcat 以及部署 war 应用

	mkdir /usr/local/tomcat/
	cd /usr/local/tomcat/
	wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.70/bin/apache-tomcat-9.0.70.tar.gz
	tar -zxvf apache-tomcat-9.0.70.tar.gz
	cd apache-tomcat-9.0.70
	sh ./startup.sh

# 开启 tomcat 端口 （不一定不需要这个步骤）
	iptables  -I  INPUT  -p  tcp  --dport  8080  -j  ACCEPT

# 安装 jdk
	sudo apt search openjdk

# 启动 tomcat
	sh /usr/local/tomcat/apache-tomcat-9.0.70/bin/shutdown.sh
	sleep 1
	sh /usr/local/tomcat/apache-tomcat-9.0.70/bin/startup.sh

# 获取 drawio  （画流程图的工具）
 - 浏览器打开即可 https://github.com/jgraph/drawio/releases
 - 下载 draw.war 包 releases 版本
 - 复制到 apache-tomcat-9.0.70/webapps 目录下
 - 重启tomcat即可
 - 进入应用的网址：http://127.0.0.1:8080/draw
