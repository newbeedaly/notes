

	ubuntu18.04 
	
	uname -r 查看内核版本 3.0 以上
	
	sudo su 切换root用户
		
	apt-get update
	
	# 安装docker
	apt-get install -y docker.io 

	# 第二种方式,最新版本
	curl -s https://get.docker.com|sh

	docker version
		client & server

	docker pull [opt] name[:tag]
	docker images [opt] [name[:tag]]

	网易镜像:
	https://c.163yun.com/hub#/m/home/


	docker pull hello-world
	docker images 
	docker run hello-world

	docker pull hub.c.163.com/library/nginx:latest

	docker ps 查询进程信息

	后台运行nginx服务器
	docker run -d hub.c.163.com/library/nginx

	 docker exec -it 5355 bash  / docker exec -it 5355 /bin/sh
	 exit

	docker stop 5355
	# 指定端口
	docker run -d -p 80:80 hub.c.163.com/library/nginx 
	# 随机分批端口
	docker run -d -P hub.c.163.com/library/nginx 

	 vim Dockerfile

		from hub.c.163.com/library/tomcat

		MAINTAINER liqk newbeedaly@163.com

		COPY jpress.war /usr/local/tomcat/webapps
	 wq

	 docker build .
	docker build -t jpress:latest .

	docker run -d -p 10000:8080 jpress
	netstat -na |  grep 80


	docker pull hub.c.163.com/library/mysql:latest

	docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=docker_mysql_123 -e MYSQL_DATABASE=jpress hub.c.163.com/library/mysql:latest
	

	docker restart tomcat_id

	网站账号和密码
	jpress
	jpress_123