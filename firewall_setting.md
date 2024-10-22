---
title: iptables/firewall 防火墙
description: 关于 Linux 防火墙 iptables/firewall 的一些设置
tag:
 - iptables
 - firewall
categories:
 - linux-tools
 - linux
---

# 防火墙 iptables

查看所有规则

	iptables -L -n

添加规则

	iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
	iptables -A OUTPUT -p tcp --sport 8080 -j ACCEPT
	iptables -A INPUT -p udp --dport 8080 -j ACCEPT
	iptables -A OUTPUT -p udp --sport 8080 -j ACCEPT


- -A , 就是添加新的规则
- -p tcp , udp 就写 udp，tcp 就用 tcp
- --dport , 就是目标端口 当数据从外部进入服务器为目标端口
- --sport , 数据从服务器出去 则为数据源端口
- -j , 就是指定是 ACCEPT 接收 或者 DROP 不接收使用 `service iptables save` 进行保存如果出现 找不到 iptables.services 安装 iptables-services `sudo yum install iptables-services`

# 防火墙 firewall

使用防火墙firewall 的设置

开启端口 2323

	firewall-cmd --zone=public --add-port=2323/tcp --permanent
	firewall-cmd --zone=public --add-port=2323/udp --permanent


查看所有开放端口

	firewall-cmd --zone=public --list-ports


更新防火墙规则

	firewall-cmd --reload


查看防火墙状态

	systemctl status firewalld


关闭防火墙

	systemctl stop firewalld


开启防火墙

	systemctl start firewalld


重启防火墙

	systemctl restart firewalld
