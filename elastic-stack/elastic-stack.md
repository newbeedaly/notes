#Elastic-stack入门

	#1 es安装
	安装jdk8
		sudo apt-get install openjdk-8-jdk

	安装elasticsearch
	 	wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.0.0-linux-x86_64.tar.gz
	
		Run bin/elasticsearch (or bin\elasticsearch.bat on Windows)
		Run curl http://localhost:9200/ or Invoke-RestMethod http://localhost:9200 with PowerShell

	配置文件config
			elasticsearch.yml
			jvm.options
			log4j2.properties

	本地启动集群的方式(测试使用)

		修改jvm.options的配置 XMS XMX 1g --> 256m
		bin/elasticsearch
		bin/elasticsearch -Ehttp.port=8200 -Epath.data=node2
		bin/elasticsearch -Ehttp.port=7200 -Epath.data=node3

		集群查看
		http://127.0.0.1:9200/_cat/nodes?v
		http://127.0.0.1:9200/_cluster
		http://127.0.0.1:9200/_cluster/stats


	elasticsearch常用术语

		Index 索引  -- > 数据库
		Document 文档数据  -- > 数据库中的一条数据
		Type 索引中的数据类型 -- > 数据库中的表
		Filed 字段，文档的属性
		Query DSL  查询语言

	Elasticsearch CRUD操作

		POST /accounts/person/1
		{
			"name":"newbeedaly",
			"lastname":"li",
			"job_description":"java&python developer",
		}

		GET /accounts/person/1
		
		POST /accounts/person/1/_update
		{
			"doc":{
				"job_description":"java&python&go developer"
			}
			
		}

		DELELE /accounts/person/1

		DELELE /accounts
	

		Elastic Query
			#1Query String
				GET /accounts/person/_serach?q=newbeedaly
			#2Query DSL
				GET /accounts/person/_serach
				{
					"query":{
						"match":{
							"name","newbeedaly"			
						}
					}
				} 

		

	# kibana安装
		安装kibana
			https://artifacts.elastic.co/downloads/kibana/kibana-7.0.0-linux-x86_64.tar.gz
		
		配置config中kibana.yml
			server.host/server.port 访问kibana用的地址和端口
			elasticsearch.url 待访问的elasticsearch的地址

		常用功能
			Discover 数据搜索查看
			Visualize 图表制作
			Dashboard 仪表盘制作
			Timilion 时序书的高级可视化分析
			DevTools 开发者工具
			Management 配置

	#Beats入门

		轻量级数据传送者 Lightweight Data Shipper
			Filebeat   日志文件
			Metricbeat 度量数据
			Packetbeat 网络数据
			Winlogbeat Windows数据
			Heartbeat 健康检查

		Filebeat处理流程
			输入Input 处理Filter(处理能力较弱,过滤效果，可以与elasticsearch Ingest node合作处理数据) 输出Output(es,logstack,kafka,redis,file)

			执行启动:head -n 2 /logs/nginx.log|./filebeat -e -c nginx.yml
					./filebeat -e -c nginx.yml
		Filebeat Module:日志格式统一化处理(nginx,apache,mysql,filebeat.yml,ingest node pipeline,kibana dashboard)
		
		Packetbeat:可以收集es请求日志等
			执行启动:sudo ./packetbeat -e -c es.yml -strict.perms=false
				es.yml配置网卡、请求地址和端口，标准输出，先启动es

	#logstash入门

		数据传送者 Data Shipper

		介绍:开源的服务端软件，可以提取数据，转换数据，发送数据。


		输入Input  file  redis beats kafka
		处理Filter grok(基于正则提供丰富可重用的模式,可以讲非结构化数据转换为结构化) mutate(进行增删改查数据) drop date
		输出Output stdout es redis kafka

		执行启动: head -n 2 /logs/nginx.log|bin/logstack -f nginx_logstash.conf
				
				bin/logstack -f nginx_logstash.conf

	#实战演练:分析elasticsearch查询语句

		目标：收集es查询语句，分析常用语句、响应时长等
		方案：应用packetbeat+logstash 完成数据收集工作，使用kibana+es完成数据分析工作、

			Production Cluster  正常启动
				Elasticsearch http://127.0.0.1:9200
				Kibana http://127.0.0.1:5601
			Monitoring Cluster 
				Elasticsearch http://127.0.0.1:8200
					bin/elasticsearch -Eluster.name=sniff_search -Ehttp.port=8200 -Epath.data=sniff_search
				Kibana http://127.0.0.1:8601
					bin/kibana -e http:127.0.0.1:8200 -p 8601

			
				Production与Monitoring集群同一个集群会陷入死循环
		
			方案之logstash
				input{ beats {prot => 5044}}

				filter{
					if "search" in [request]{
						grok {match => {"request" => ".*\n\{(?<query_body>.*)"}}
						grok {match => {"request" => ".*\n\{(?<query_body>.*)"}}
					if[index]{}else{ mutate{ add_field => {"index" => "All"} }}
						mutate{update => {"query_body" => "{%{query_body}}"}}
						}
					}
				}
				output{

					if "search" in [request]{
						elasticsearch { hosts => "127.0.0.1:8200" }
					}
				
				}
			方案之packetbeat
				
				packetbeat.interfaces.device:Llo0(linux可以是any)
				packetbeat。protocols.http: ports: [9200]
				 send_request:Ltrue
				 include_body_for:["application/json","x-www-form-urlencoded"]
				output.logstash:hosts:["127.0.0.1:5044"]

			kibana可视化分析
				
		附加
			学会查阅和搜索官方文档
				www.elastic.co/learn
			学会在社区正确地提问问题
				www.elasticsearch.cn
			开阔视野的方法--Elastic 日报
				elasticsearch.cn/explore/category-18
			
		
			

#Elastic Stck从入门到实践

	为什么是使用Elastic-stack?

		搭建快，数据实时，扩展方便。

	Elastic-stack组成:

		Kibana:数据探索与可视化分析
		Elasticsearch:数据存储、查询与分析 核心搜索引擎
		Beats&Logstash:日志收集与处理 ETL:支持多样数据格式

	完备的数据分析工具集合
		搜索引擎
		日志分析
		指标分析

	

	