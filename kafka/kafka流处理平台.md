#kafka流处理平台

	1.kafka概念:

		LinkedIn开发-->2012年apache基金会开源

	kafka流处理平台三个特性

		1.发布与订阅数据记录，类似于一个消息队列或者企业消息系统。
		2.存储数据流。
		3.数据产生时，可以对数据处理。

	kafka应用场景

		实时流的处理管道和处理平台。

	Producer:消息和数据的生产者，向kafka的一个topic发送消息的进程/代码/服务
	Consumer:消息和数据的消息者，订阅数据topic并且处理其发布的消息的进程/代码/服务

	Consumer Group:逻辑概念，对于同一个topic,会广播给不同的group，一个group中，只有一个consumer可以消费该消息。

	Broker:物理概念，kafka集群中的每个kafka节点
	Topic:逻辑概念，对数据进行区分、隔离。消息的分类。
	
	Partition:物理概念，kakfa下数据存储的基本单元。一个topic数据，会被分散存储到多个partition,每一个Partition是有序的。

	Replication:Partition的副本，一个Partition有多个Replication。

	Replication Leader:Replication的交换节点，Partition的生产者/消费者的交互。

	ReplicaManager:负责管理当前broker所有分区和副本的信息，处理kafkaController发起的请求，副本状态的切换、添加、读取消息等。



	2.kafka结构设计
		
		kafkaAPI和消息结构/流处理。
	
		kakfa特点:
				  分布式:多分区、多副本、多订阅者、基于zookeeper调度。
				  高性能:高吞吐量、低延迟、高并发、时间复杂度O(1)
				  持久性与扩展性：数据可持久化、容错性、支持在线水平扩展、消息自动平衡
				

	3.kafka场景与应用

		一、消息队列
		二、行为跟踪
		三、元数据监控
		四、日志收集
		五、流处理
		六、事件源(状态回溯等)
		七、持久性日志(commit log)
		
	4.kafka案例

		  不需要:安装jdk(sudo apt-get install openjdk-8-jdk)
		  不需要:下载zookeeper

		下载kafka
		wget http://mirror.bit.edu.cn/apache/kafka/2.2.0/kafka_2.11-2.2.0.tgz

		tar -zxvf kafka_2.11-2.2.0.tgz

		cd kafka_2.11-2.2.0/config

		vim server.properties 
			broker.id=1
			listeners=PLAINTEXT://localhost:9092
			advertised.listeners=PLAINTEXT://localhost:9092
			log.dirs=/app/kafka/kafka_2.11-2.2.0/kafka-logs

		#启动zookeeper
		bin/zookeeper-server-start.sh -daemon config/zookeeper.properties 

		#启动kafka
		bin/kafka-server-start.sh config/server.properties
		
		错误提示：
			Java Hotspot(TM) 64-Bit Server VM warning: INFO: os::commit_memory(0x00000000c5330000, 986513408, 0) failed; error='Cannot allocate memory' (errno=12)
			#
			# There is insufficient memory for the Java Runtime Environment to continue.
			# Native memory allocation (malloc) failed to allocate 986513408 bytes for committing reserved memory.
			# An error report file with more information is saved as:
			# hs_err_pid5535.log
			解决办法：
			将 kafka-server-start.sh的
			export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
			修改为
			export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
			因为我的内存是1G

		#创建topic
		./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic newbeedaly-kafka-topic
			
			Created topic newbeedaly-kafka-topic.

		#查看topic列表
		bin/kafka-topics.sh --list --zookeeper localhost:2181
	
		#创建生产者，产生消息
		bin/kafka-console-producer.sh --broker-list localhost:9092 --topic newbeedaly-kafka-topic
		#创建消费者，消费消息
		bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic newbeedaly-kafka-topic
		bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic newbeedaly-kafka-topic --from-beginning


		#查看topic消息
		bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic newbeedaly-kafka-topic

	5.kafka高级特性

		kafka消息事务
			最少一次，最多一次，精确一次。
			事务保证:Procedure幂等处理，多分区原子写入。
		零拷贝
			内核空间与交互空间实现零拷贝,实际并不是零拷贝。

	6.简单代码实例

		D:/praticespace/kafka

