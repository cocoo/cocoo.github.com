---
layout: post
title: "rabbitmq环境搭建"
date: 2013-02-19 10:11
comments: true
categories: [rabbitmq,linux] 
---
## 目录
*  [简介](#introduction)
*  [安装](#install)
	*   [erlang 安装](#erlang-install)
	*   [rabbitmq 安装](#rabbitmq-install)

<!-- more-->
<div id="introduction"></div>
## 简介
MQ全称为Message Queue, 消息队列（MQ）是一种应用程序对应用程序的通信方法。应用程序通过写和检索出入列队的针对应用程序的数据（消息）来通信，而无需专用连接来链接它们。消 息传递指的是程序之间通过在消息中发送数据进行通信，而不是通过直接调用彼此来通信，直接调用通常是用于诸如远程过程调用的技术。排队指的是应用程序通过 队列来通信。队列的使用除去了接收和发送应用程序同时执行的要求。其中较为成熟的MQ产品有IBM WEBSPHERE MQ。

<div id="install"></div>
## 安装
<div id="erlang-install"></div>
### erlang 安装
erlang的安装不在这里赘述，网上很多这方面的教程。注意下把环境变量配置下即可，我装的使R15B01	
<div id="rabbitmq-install"></div>
### rabbitmq 安装
	wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.0.2/rabbitmq-server-generic-unix-3.0.2.tar.gz
	tar xvzf rabbitmq-server-generic-unix-3.0.2.tar.gz
	cp -R rabbitmq_server-3.0.2 /usr/local/rabbitmq
### rabbitmq 基本配置和使用
修改默认配置文件路径
	vi /usr/local/rabbitmq/sbin/rabbitmq-defaults
	SYS_PREFIX=
新建配置文件
	vi /etc/rabbitmq/rabbitmq-env.conf
	#Rename the node
	#NODENAME=bunny@myhost
	#Config file location and new filename bunnies.config
	CONFIG_FILE=/etc/rabbitmq/rabbitmq.config

	vi /etc/rabbitmq/rabbitmq.config

	[
	  {rabbit,                    [ {tcp_listeners,               [5672]},
	                                {collect_statistics_interval, 10000} ] },
	  {rabbitmq_management,       [ {http_log_dir,          "/tmp/rabbit-mgmt"},
	                                {listener,              [{port, 55672}]} ] },
	  {rabbitmq_management_agent, [ {force_fine_statistics, true} ] }
	].

	

开启web管理
	/usr/local/rabbitmq/sbin/rabbitmq-plugins enable rabbitmq_management 
