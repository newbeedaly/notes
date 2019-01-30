# 1、全文检索技术 

	全文检索技术应用:

		1.搜索引擎
		2.站内搜素  
		3.文件系统搜索

	java有哪些技术:
		
		lucence:java全文检索技术的底层实现
		solr:基于lucence,简化开发，提升性能，通过solrCould分布式搜索，近实时搜索
		es:基于lucence,更倾向于实时搜索。实时搜索：内存缓冲机制

	如何选择:

		搞清楚每个技术的特点和缺点。

	数据:结构化数据和非结构化数据。有无固定化长度和格式。

	全文检索:先建立索引，再对索引进行搜索文档的过程，就是全文检索。

	三种技术选择:

	lucence:如果使用该技术实现，需要对lucence底层原理非常了解。
	solr:使用java实现的一个web应用，可以使用rest方式的http请求，进行远程api的调用。
	lucence:可以使用rest方式的http请求，进行远程api的调用。

	当单纯的对已有数据进行搜索时，solr更快。
	当实时建立索引时，solr会产生io阻塞，查询性能较差，es具有明显优势。
	随着数据量的增加，solr搜素量会变得更低，es性能没有变化。
	大型互联网公司，solr会转为es

	最终结论:
		solr是传统搜索(在信息库中筛选信息)应用的有利解决方案，es更适合新兴的实时索引应用。

	
	全文检索流程:

		![](https://i.imgur.com/cA5vXgU.png)

		![](https://i.imgur.com/QSXv8iU.png)

		倒排索引表:索引域(包含term 和 频率) --> 文档域(Field)
					java                       name

	文档相关度排序:
		
		自然打分:
			term 出现频率越高，权重越高
			documnet 出现频率越高，权重越低

		人为因素:
			设置Boost值:加权值

	Field域属性:
		
		是否分词,是否索引,是否存储

	中文分词器:

		IkAnalyzer 以及扩展词库

# 2、solr

	
	solr是一个web服务。

	# 1安装
	
	wget http://archive.apache.org/dist/lucene/solr/4.10.3/solr-4.10.3.tgz
	tar -zxvf solr-4.10.3.tgz
	
	# more
	[http://lucene.apache.org/solr/downloads.html](http://lucene.apache.org/solr/downloads.html "solr官网下载")
	最新:wget http://www.apache.org/dyn/closer.lua/lucene/solr/7.6.0/solr-7.6.0.tgz


	Linux下Solr的安装和配置:https://www.cnblogs.com/guxiong/p/6284938.html

	# 2创建solr文件夹将tomcat解压后的文件夹拷贝到solr的tomcat文件夹中
	mkdir /app/solr
	cp apache-tomcat-7.0.47 /app/solr -r

	# 3把solr的压缩包上传到服务器后并解压，然后把solr-4.10.3/dist/solr-4.10.3.war包部署到tomcat下。并改名为solr.war
	tar -zxf solr-4.10.3.tgz
	cp solr-4.10.3.war /app/solr/tomcatsolr/webapps/
	# 4.1修改端口为8899
	# 4.2解压war包。启动tomcat自动解压。然后关闭tomcat，删除war包

	# 5把solr-4.10.3/example/lib/ext 目录下所有的jar包复制到solr工程中
	cp * /app/solr/tomcatsolr/webapps/solr-4.10.3/WEB-INF/lib/

	# 6创建solrhome文件夹，该文件夹用来存放solr服务器所有配置文件。将/root/solr-4.10.3/example中的solr文件夹拷贝至solrhome
	cd solr-4.10.3/example/
	cp -r solr /app/solr/solrhome/
	# 7在solr工程中的web.xml文件中指定solrhome的位置
	cd /app/solr/tomcatsolr/webapps/solr-4.10.3/WEB-INF
	vim web.xml 修改为:

		<env-entry>
       		<env-entry-name>solr/home</env-entry-name>
       		<env-entry-value>/app/solr/solrhome/solr</env-entry-value> 此处目录请注意
       		<env-entry-type>java.lang.String</env-entry-type>
    	</env-entry>

	# 配置IkAnalyzer

	# 1把分析器的jar包添加到solr工程中,可以使用rz命令上传，sz命令下载
	cp IKAnalyzer2012FF_u1.jar /app/solr/tomcatsolr/webapps/solr/WEB-INF/lib/
	
	# 2IKAnalyzer需要的扩展词典及停用词词典、配置文件复制到solr工程的WEB-INF/classes文件夹中
	如果没有classes文件，创建此文件夹 mkdir classes
	cp IKAnalyzer.cfg.xml ext_stopword.dic mydict.dic /app/solr/tomcatsolr/webapps/solr/WEB-INF/classes

	# 3在solrhome/solr/collection1/conf/schema.xml中配置filedType

	<!--
	<fieldType name="text_greek" class="solr.TextField">
      <analyzer class="org.apache.lucene.analysis.el.GreekAnalyzer"/>
    </fieldType>
    -->
	改为:
    <fieldType name="text_ik" class="solr.TextField">
         <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>
    </fieldType>

	# 4在solrhome/solr/collection1/conf/schema.xml中配置，根据业务需要选择相应的字段对应solr的字段
	<field name="user_name" type="text_ik" indexed="true" stored="true"/>
	<field name="user_address"  type="text_ik" indexed="true" stored="true"/>
	
	<field name="item_keywords" type="text_ik" indexed="true" stored="false" multiValued="true"/>
	<copyField source="user_name" dest="item_keywords"/>
	<copyField source="user_address" dest="item_keywords"/>

	# 5重启tomcat，访问测试
   

# 3、solrCloud搭建

	# 1zookeeper集群搭建，参考其他笔记
	
	# more
	scp -r tomcatsolr/ root@x.x.x.x:/app/solr  远程主机copy文件夹
	
	# 2复制单机版solr,并修改端口，一个solrCore对应一个solrhome,复制单机版solrHome到不同机器。
	# 3修改每个solr服务的web.xml,分别指定solrhome路径

	# 4修改每个solrhome下的solr.xml,指定对应的tomcat ip和port.
		
		在solrcloud标签下，修改端口号就可以
		<solrcloud>	<int name="hostPort">8888</int>	</solrcloud>

	# 5设置tomcat启动参数，在tomcat目录下的bin/catalina.sh,添加以下内容：
		
		在JAVA_OPTS下，添加一行
		JAVA_OPTS="-DzkHost=x.x.x.x:port,x.x.x.x:port,x.x.x.x:port"
	
	# 6将solr配置文件上传到zookeeper中，进行统一配置。

	# 7复制solr的解压文件夹到不同机器上。
	# 8使用solr-4.10.3/example/scripts/cloud-scritpt的zkcli.sh命令
		将solrhome/collection1/conf目录上传到zookeeper进行配置
		
		进入到solrhome/collection1/conf目录
			
		命令: 只需要一个主机执行就可以
			./zkcli.sh -zkhost 192.168.x.x:2181,192.168.x.x:2181,192.168.x.x:2181 -cmd 
			upconfig -confdir /xx/xx/solrhome/collection1/conf -confname myconf

	# 9启动所有的tomcat,访问任一个节点的tomcat就可以

	# 10 将4个sorl节点，分为两片，每片两个副本
		
		# http命令:
		http://x.x.x.x:port/solr/admin/colletions?action=CREATE&name=collection2&numShards=2&replicationFactor=2
		
		#more 删除原来collection
		http://x.x.x.x:port/solr/admin/colletions?action=DELETE&name=collection1
	# 11 solr ok


# 3、代码实现solr

	参考springboot-solr项目



# 4、es

	# 1介绍
	ElasticSearch是一个基于lucence的搜索服务器，它提供了一个分布式多用户能力的全文搜索引擎，基于restful web接口。
	优点:
		速度快，近实时查询，最迟1秒
		索引和检索的数据量大，支持TB级数据
		高扩展、高可用
	概括:基于Restful标准的高扩展高可用的实时数据分析的全文搜索服务器
		跟solr一样可以使用文档来存储数据

	es的基本概念
		
		关系数据库  		es
		数据库DB			索引Index
		表Table			类型Type
		数据行Row		文档Document
		数据列Column		字段Field
		表结构Schema		映射Mapping

	接近实时:最少延迟1秒

	ES结构图和架构

	# 2安装

	# 下载
	cd /app/elasticsearch
	wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.5.3.tar.gz
	wget https://artifacts.elastic.co/downloads/kibana/kibana-6.5.3-linux-x86_64.tar.gz
	wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.5.3.tar.gz

	# 解压
	tar -zxvf elasticsearch-6.5.3.tar.gz 

	vim elasticsearch-6.5.3/config/elasticsearch.yml 

		network.host: 192.168.0.1 -- > 0.0.0.0(127.0.0.1)

	vim /etc/sysctl.conf // 进程拥有的虚拟内存数据
		添加内容:
		vm.max_map_count=655360

	sysctl -p //生效
	
	vim /etc/security/limits.conf // 允许打开的最大文件描述符数量
		
		添加
		esuser soft nofille 65535
		esuser hard nofille 65535
		esuser soft nproc 4096
		esuser hard nproc 4096

	vim /etc/security/limits.d/20-nproc.conf

		将 * 改为 ecuser
		esuser soft nproc 4096

	# 创建用户和组
			groupadd esgroup
			useradd esuser -g esgroup -p 111111

	# 更改elastic文件夹及内部文件的所属用户和组
		chown -R esuser:esgroup /app/elasticsearch/elasticsearch-6.5.3

	# 切换esuser用户

		su esuser
		   111111

	# 启动
		vim /app/elasticsearch/elasticsearch-6.5.3/config/jvm.options
				修改如下内容（默认为1G）
				-Xms256m
				-Xmx256m

		后台启动
		/app/elasticsearch/elasticsearch-6.5.3/bin/elasticsearch -d
		可以在logs文件看下elasticsearch.log文件


	# 上面步骤安装三台后，下面配置集群


		修改elasticsearch.yml配置集群,可以直接在文件后添加以下内容
			
			# 集群名称要唯一
			cluster.name: newbeedaly-es
			# 集群节点不同（每台机器都要改）
			node.name: node-xx
			# 设置master和data节点
			node.master: true
			node.data: true
			# 节点将绑定到主机名或ip地址，我们设置成0.0.0.0为当前主机，允许ip映射访问
			network.host: 0.0.0.0
			# http访问端口
			http.port:9200
			# tcp访问端口
			transport.tcp.port:9300
			# 自动发现的路由节点
			discovery.zen.ping.unicast.hosts:["192.168.0.1:9300","192.168.0.1:9300","192.168.0.1:9300"]
			# 集群中最小主节点数，防止脑裂
			discovery.zen.mininum_master_nodes:2
			# head 跨域访问
			http.cors.enabled: true
			http.cors.allow-origin:"*"
			


		jps 查看进程
		kill -9 pid 关闭es进程

		使用esuser用户启动集群，查看日志

		检查是否正确启动命令
		curl -i -XGET -H 'Content-Type: application/json' 'http://192.168.10.135:9200/_cat/health?v'
	
		
	
	