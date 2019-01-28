# 			RabbitMQ学习

#### 一、简介

​	RabbitMQ是开源的消息代理和队列服务器，用来通过普通协议在完全不同的应用之间共享数据，RabbitMQ是使用Erlang语言来编写的，并且RabbitMQ是基于AMQP协议的。

​	能与SpringAMQP集成，提供丰富的Api。

​	保证数据不丢失的前提做到高可靠性、可用性。

​	AMQP协议模型。

#### 二、RabbitMQ的安装与使用

	#安装erlang语言环境
	sudo apt-get install erlang-nox

	#安装rabbitmq命令
	sudo apt-get update
	sudo apt-get install rabbitmq-server

	#可能有错误,停止服务，修改配置文件
	/usr/lib/rabbitmq/bin$ invoke-rc.d rabbitmq-server stop

	进入安装文件夹/usr/lib/rabbitmq/bin修改rabbitmqctl文件

	RABBITMQ_USE_LONGNAME=${RABBITMQ_USE_LONGNAME} \
	HOME=/var/lib/rabbitmq  \  添加的一行
	exec ${ERL_DIR}erl \
	
	启用后台管理器
	/usr/lib/rabbitmq/bin$ sudo ./rabbitmq-plugins enable rabbitmq_management 


	#添加用户并赋予权限

	安装完成后在rabbitMQ中添加用户
	命令： sudo rabbitmqctl add_user 用户名 密码
	将用户设置为管理员（只有管理员才能远程登录）
	命令：sudo rabbitmqctl set_user_tags 用户名  administrator
	同时为用户设置读写等权限

	#启动、停止、重启、状态rabbitMq命令：

	启动：sudo rabbitmq-server start
	关闭： sudo rabbitmq-server stop
	重启： sudo rabbitmq-server restart
	查看状态：sudo rabbitmqctl status

	
	可以参考:https://www.cnblogs.com/web424/p/6761153.html



