检查Cronta工具是否安装：crontab -l
检查crond服务是否启动：service crond status

centOSs安装cron
yum install vixie-cron
yum install crontabs

ubuntu安装cron
安装：apt-get install cron
启动：service cron start
检查状态：service cron status

查询cron可用的命令：service cron

编辑crontab：crontab -e

命令：*/1 * * * * date >> /log.txt

当出现保存编辑出现：