一、crontab 安装

检查Cronta工具是否安装：crontab -l/crontab -help
检查crond服务是否启动：service crond status

centOS安装cron

	yum install vixie-cron
	yum install crontabs

ubuntu安装cron

	安装：apt-get install cron
	启动：service cron start
	检查状态：service cron status

查询cron可用的命令：service cron

编辑crontab：crontab -e

输入命令：* * * * * date >> /app/tmp/crontab.log


附录：

一、Ubuntu设置root用户密码：

		sudo passwd;

二、apt-get工具包安装：

		apt-get install;
		apt-get update;