#剑指Java面试-Offer直通车

### 1.计算机网络知识

	OSI七层网络参考模型:
		
		第7层 应用层 文件传输 http协议
		第6层 表示层 不同系统之间通信语法的问题。
		第5层 会话层 不同机器上用户之间建立及管理会话。
		第4层 传输层 数据片(分片) TCP/UDP协议
		第3层 网络层 段落(分组) 网络地址翻译成对应的物理地址（IP协议）。
		第2层 数据链路层  帧  定义数据格式，数据检错。
		第1层 物理层  比特。

	TCP的三次握手:
		
		A          		B
		1发出请求 SYN包,等待服务端确认 
						2确认 SYN+ACK包，确认等待
		3再次确认 ACK包,发送确认，开始发送数据(如果ACK超时,存在三次握手的隐患)
		
		之后双方开始通信

	
	TCP的四次挥手:
		
		客户端和服务端进行四次挥手，中止连接。

		服务端大量出现CLOSE_WAIT原因:
			 检查代码:连接没有及时关闭，多线程配置不准确

		linux查看命令:
		netstat -n | awk '/^tcp/{++S[$NF]}END{for(a in S) print a,S[a]}'
			too many open files

	TCP和UDP的区别

		TCP 面向连接 安全的 报头量20字节
		UDP 面向非连接 不安全的 报头量8字节

		
		1.TCP面向连接，UDP面向非连接，TCP有三次握手的过程,UDP点个点向多个点的多播发布
		2.可靠性 TCP可靠
		3.速度 TCP慢，原因面向连接
		4.数据量级 TCP大

	TCP滑动窗口:
		
		提高传输速度，保证传输可靠性。窗口大小可以依据一定策略，动态调节。

	HTTP:

		支持客户端/服务端模式
		简单快速，灵活，无连接(自动释放连接)，无状态。

		url回车,发生什么？
			DNS解析
			TCP连接，三次握手
			发送http请求
			服务端发送http报文
			浏览器渲染页面
			关闭连接

		状态码
			1xx 请求接收，正在处理
			2xx 正常
			3xx 重定向
			4xx 客户端错误 403 拒绝提供服务
			5xx 服务端错误 500 服务错误/503 不能处理客户端请求，可能连接池满

		get/post区别:

			get 请求信息放在url / post 请求信息放在报文体中 都是不安全的。
			get 在浏览器url长度有限制,post不限制数据长度
			get 幂等性和安全性 post 不符合

		cookie/session区别:
		
			cookie存在客户的浏览器上，session数据存放在服务器上
			session相对于cookie更安全
			若考虑服务器负担，应当使用cookie
			

	HTTP和HTTPS区别:

		SSL协议的作用:身份认证，数据加密。
		SSL协议使用:证书+各种加密方式。
		加密: 对称加密，非对称加密，哈希算法 MD5, 数据签名。


	1.HTTPS需要到CA申请证书，http不需要
	2.HTTPS密文传输，HTTP明文传输
	3.连接方式不同，HTTPS默认使用443端口，HTTP使用80端口
	4.HTTPS=HTTP+加密+认证+完整性保护，较HTTP安全。

		HSTS协议:更安全

	SOCKET相关:
		
		Socket是对TCP/IP协议的抽象，是操作系统对外开放的接口。

		编程，使用Socket实现tcp/udp通信



### 2.数据库

	架构
		RDBMS设计:存储和程序实例
				程序实例:存储管理,缓存机制,SQL解析,日志管理(binlog),权限划分,容灾机制,索引管理,锁管理
	索引
		全表扫描,数据大时非常慢,通过索引快速查询数据。
		为什么使用索引:索引能够避免全表扫描查询数据，查询速度快。
		什么信息成为索引:主键，唯一键，普通键，一定区分性的字段都能成为索引。备注类字段不建立索引。

		索引的数据结构:

			生成索引，建立二叉树，进行二分查找，O(logn)。
						平衡二叉树:任意一个节点的高度之差不超过1。缺点:修改数据会影响数据结构哦（n）
			生成索引，建立B-tree结构进行查找。
						B-tree:根节点至少有两个孩子，每个节点最多包含根节树个孩子且大于2，非根非叶子节点孩子最少为M/2的上限，所有叶子节点都唯一同一层。关键点:数据变动不会改变成线性树。O(logn)。
			生成索引，建立B+-tree结构进行查找(mysql)。
						在B-tree基础上添加了约束:非叶子节点存储索引，叶子节点存储数据。
							优点：B+树读写代价更低(内部节点存储索引，不存在数据，IO少)，B+查询效率更高，B+树更利于对数据的扫描。
			生成索引，建立HASH结构进行查找。存储数据为hash计算的值。
						缺点:仅仅能满足 = in ,不能使用范围查询
							不能利用好部分索引键查询。
							不能避免表扫描。

			位图索引，BITMAP索引是个神器。仅仅适用存储固定的几个字段,不适合频繁的修改，查询速度快。MYSQL不支持
			

		密集索引和稀疏索引区别:
			
			密集索引文件中的每个搜索码值都对应一个索引值。
			稀疏索引文件只为索引码的某些值建立索引值。

		如何定位并优化慢查询sql
			
				show variables like '%quer%';

				show status like '%slow_queries%';
				
				-- 设置变量，重启数据库恢复默认
				set global slow_query_log =  on;
				set global long_query_time =  1;
				
				select count(id) from person; 
				-- 解释
				explain select name from person order by name desc;
				
				-- 加索引
				
				alter table person add index idx_name(name);
				
				-- 强制索引
				select count(id) from person; 
				select count(id) from person force index(primary); 

		联合索引
				最左匹配原则，如果遇到 范围调价 > < like between, 须将 范围添加放在最后。
				

		索引建立越多越好吗？
			否定，数据小的表不需要建立索引，建立索引会增加额外的开销。
				数据变更需要维护索引，因此更多的多因意外的更多的维护成本
				更多的索引需要更多的维护空间。
	锁
		MyISAM与InnoDB关于锁的区别是什么？
			MyISAM默认用的是表级锁，不支持行级锁。
			InnoDB默认用的是行级锁，也支持表级锁。
			
			InnoDB sql走索引，走行级锁，sql不走索引，走表级别锁。
			InnoDB支持事务，MyISAM不支持事务。

		悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。
		乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。

			-- 表加锁，解锁。
			lock table person read | write;
			unlock tables;
			-- 排他锁,添加for update
			select * from person for update;
			-- 行级锁 lock in share mode
			select * from person where id=3 lock in share mode;

		数据库事务的四大特性
			ACID:原子性，一致性，隔离性(脏读会产生数据不一致)，持久性。

			-- 以下都是多session操作下:

			-- 避免脏读,查询，并设置事务隔离级别read committed，会出现重复读不一致问题
			select @@tx_isolation
			set session transaction isolation level read committed;oracle默认级别
			-- 幻读 将事务隔离级别设置为seraializable,这个级别下所有的sql都会加上锁。
			--REPEATABLE-READ mysql默认级别，解决上述两个问题。
			set session transaction isolation level REPEATABLE READ;ss
			RR级别下会出现间隙锁(Gap锁)。

		InnoDB可重复读隔离级别下如何避免幻读？MVCC和Gap防止幻读。
		RC、RR级别下的InnoDB的非阻塞读如何实现？
			
	语法
		student score course 表
		group by, having, sum count max mix avg.


		#查询所有同学的学号，选课数，总成绩
		select student_id,count(course_id),sum(score) from score group by student_id

		#查询所有同学的学号，姓名，选课数，总成绩
		select s.student_id,stu.name,count(s.course_id),sum(s.score) from score s,student stu where s.student_id = stu.student_id group by s.student_id
		
		#查询平均成绩大于60分的同学的学号和平均成绩
		select student_id,avg(score) from score group by student_id having avg(score)>60

		#查询没有学全所有课程同学的学号，姓名
		select stu.student_id,stu.name from student stu,score s where stu.student_id=s.student_id group by s.student_id having count(1) < (select count(1) from course)
	
	理论范式
		
		三范式:列不可分割，属性依赖主键，要求一个数据库表中不包含已在其它表中已包含的非主关键字信息。

	面试:
		首轮:面试技术基本功，比较强的码农。
		次轮:架构技术，通过技术解决某些场景下的问题，高功、team leader。感兴趣学习过，关公面前舞大刀比较危险。
		末轮:稳定性以及未来规划,压工资。领导

		项目:主要负责模块，重点将技术，遇到问题以及解决方案。
	

### 4.redis

	数据库缓存:Memcache和Redis

	redis数据类型丰富，支持持久化，支持主从，支持分片。

	为甚么redis那么快？
		100000QPS,完全基于内存，执行效率高。
		数据结构简单，对数据操作也简单。存储结构是HASHMAP
		采用单线程，单线程处理高并发请求。
		使用多路IO复模型，非阻塞IO

	数据类型:
		String类型 set name "liqk" / get name
		HAsH类型 保存对象 hmset liqk name "liqk" age 26 title "Senior" / hget liqk name/age/title
		List列表 保存排行榜 lpush mylist aaa , lpush mylist bbb / lrange mylist 0 10
		Set集合 通过hash表结构实现,无序的，实现共同关注。 sadd myset 111, sadd myset 222/ smembers myset
		Sorted Set 从小到大排序,实现排序和存储权重  zadd myzset 3 abc,zadd myzset 1 abb,zadd myzset 2 agg/ zrangebyscore myzset 0 10


	dbsize 查看redis key数量

	-- 查询k1开头key
	keys k1* 会阻塞
	scan 0 match k1* count 10 返回可能少于10个，第一个cursor
	scan [cursor] match k1* count 10 返回十个

	通过redis实现分布式锁
		
		解决问题:互斥性，安全性，避免死锁，容错。
		
		SETNT key value 如果key不存在，则创建并赋值，返回值 1成功 0失败 时间复杂度 O(1)
		-- 使用setnx实现分布式锁
		get locknx / setnx locknx test /setnx locknx task / get locknx
		-- 解决长期有效的问题
		expire locknx 2 2秒后失效

		-- 最佳方式
		set locktarget 12345 ex 10 nx 10秒失效

		大量的key同时过期出现卡顿？过期时间后加上随机值

	如何使用Redis做异步队列(List数据结构)

		rpust testlist aaa,rpust testlist bbb,rpust testlist ccc / lpop testlist / blpop testlist 30 等待30s
		redis sub/pub 缺点:消息的发布是无状态的，无法保证可达


	Redis持久化

		RDB(快照)持久化:备份数据库状态  数据文件小，恢复时间长
			save 900 1
			save 300 10
			save 60 10000
		AOF持久化:备份数据库所接收的指令(先加载AOF文件) 文件大，恢复时间长
			
			appendfsync everysec / always

		RDB-AOF混合持久化方式 redis4.0以后
	
			集合两者的优点。BGSAVE做镜像全量持久化，AOF做增量持久化


		使用Pipeline的好处
			
			Pipeline批量执行大量命令。多次IO执行变为一次IO执行。
	
	redis的同步机制

		全同步过程
			slave发送sync命令到master
			master启动一个后台进程，将redis中的数据快照保存到文件中
			master将保存数据快照期间接收到的写命令缓存起来
			master完成写文件后，将该文件发送给slave
			使用新的aof文件替换掉旧的aof文件
			master将这期间收集二点增量写命令发送给slave端。

		增量同步
			
			master接收到用户的操作指令，判断是否需要传播到slave
			将操作记录追加到aof文件
			将操作传播到其他slave：1对齐主从库，2往响应缓存写入命令
			将缓存数据发送给slave

		主从模式:master挂了，不可用。
		sentinel哨兵模式:master挂了，自动选举master

			流言协议Gossip：在杂乱无章中寻求一致。
		
		redis集群

			分片查询数据。
			存储数据分片,一台服务挂了,自动定位到上一分片服务器,采用一致性哈希算法(哈希环),节点少时,可以设置虚拟节点。
		
### 5.linux

		linux体系结构
			体系结构主要分为用户态(用户上层活动)和内核态。
			内核:本质是一段管理计算机硬件设备的程序。
			系统调用:内核的访问接口，是一种能再简化的操作。
			公共函数库:系统调用的组合拳。
			shell:命令解释器，可编程。

		shell 
			ls -lrt 
			which ls
			cat memory.sh
			less memory.sh q退出，滚动显示后续内容
			more memory.sh 空格，显示下一页
			vi/vim memory.sh
			echo $SHELL
			chsh -s /bin/bash #切换shell指令

		查找文件
			
			find -name 'target.java' -- 当前目录递归查询
			find / -name 'target.java' -- 全文查询
			find ~ -name "target*"  -- 支持通配符
			find ~ -iname "target*" -- 忽略大小写

		检索文件内容

			grep "moo" target* # 在target开头的文件中查询moo内容

			管道操作符 |

				find ~ -name "target*"  
				find ~ | grep "target*"
			grep "order_no:xxxx" log.log
			grep "order_no:xxxx" log.log | grep -o 'engine\[[0-9][a-z]*\]'

			ps -ef | grep tomcat | grep -v "grep" # grep -v 过滤掉grep
				
			awk '{print $1,$4}' netstat.txt 打印第1列和第四列的数据。
			awk '$1=="TCP" && $2==1{print $1}' netstat.txt
			awk '{enginearr[$1]++}END{for(i in enginearr)print i "\t" enginearr[i]}'

			awk指令:做统计的指令
			sed指令:批量替换

		细节:
			
			面试要偷偷摸摸地进行
			面试时间不要一味将就对方
			提离职要谨慎
			离职时好聚好散
			跳槽时间衔接:社保缴纳

### 6.jvm

		谈谈你对java的理解
			平台无关性:一次编译，到处运行。
			GC:垃圾自动回收
			语言特性
			面向对象
			类库
			异常处理
			
		平台无关性:
			先编译成字节码，再执行。
			javac编译成二进制字节码class文件 执行java Class.class


		jvm如何加载.class文件
			classLoader将符合格式要求的class文件加载到内存,Execution Engine解释器对命令进行解析,提交给操作系统执行。
			Native Interface原生接口，用来调用本地库函数和其他语言库函数。
			Runtime Data Area:jvm内存结构模型。
			

		谈谈反射
			java在运行状态中，对于任何一个类，都能知道这个类的所有属性和方法;对于任何一个对象
			都能够调用它的任意方法和属性，这种动态获取信息以及动态调用方法的功能称为java语言的反射机制。
		
			rc = Class.forName("cn.newbeedaly.reflect.Robot");
			
			Class;Method;
			rc.newInstance;rc.getDeclareMothod();rc.getDeclareMothod.invoke();rc.getMothod();rc.getMethod().invoke();// 私有方法调用 name.getAccessible(true);


		forName和loadClass区别
			loadClass只是加载了类，类的信息并为初始化，forClass类的信息都已经初始化完毕。

			可以写static代码块进行测试。

		jvm内存模型
			线程私有:程序计数器(线程独立,不会泄露,执行命令)、虚拟机栈(局部变量表等信息)、本地方法栈（native）
			线程共享:MetaSpace、java堆（包含字符串常量池）

			栈:栈会溢出，递归调用层数过高，栈帧创建过多，出现栈溢出异常，解决:使用循环方法。栈过多，也会出现内存溢出。栈内存使用后会自动释放，不需要gc回收。

			元空间和永久代区别
				都是用来存储class信息
				元空间使用本地内存，永久代使用jvm内存。

			metaSpace相比PermGen的优势
				字符串常量池存在永久代中，容易出现性能问题和内存不足。
				类和方法的信息大小难以确认，给永久代的大小指定带来困难，
				永久代会为gc带来不必要的复杂性。
				方便hotspot与其它jvm集成,如jrockit

			java堆（heap）

				存放对象实例。Xms Xmx配置堆大小。堆按垃圾回收gc可以分为新生代和老年代。

			
		常见问题
			jvm三大性能调优参数-Xms(堆的初始值) -Xmx(堆能达到的最大值) -Xss(每个线程虚拟机堆栈的大小)的含义
				java -Xms128m -Xmx128m Xss256k -jar xxxx.jar
			

			堆和栈的区别？栈的引用地址指向堆

				管理范式：栈自动释放，堆需要gc
				空间大小：栈比堆小
				碎片相关：栈的碎片远小于堆。
				分配方式：栈支持静态和动态分配，而堆仅支持动态分配。
				效率:栈的效率比堆高，栈 操作 入栈出栈，后进先出。堆:双向链表结构,操作复杂

			String s = new String("a"); // 常量池 
			s.inten();ss = "a"; // 堆 
			s==ss // false
			 1.6 set到字符串常量池，1.6+也放到堆中，如果数据存在只放引用，不受永久代约束。

### 7.gc
		
		jvm:对象内存分配，回收分配给对象的内存。
		垃圾:没有被其他对象引用
		判定方法:
			引用计数算法:使用引用计数器计数，堆中的每个对象，会被引用，任何引用计数器为0的对象，都可以被回收。
				优点：执行效率高，缺点：无法识别出循环引用，导致内存泄漏。
			可达性分析算法:判断对象的引用链是否可达来决定对象是否可以回收。从gc root开始计算是否可达。离散数据的图论引出的。

		垃圾回收算法
			标记-清除算法，可达性算法进行标记，不可达的对象进行回收,会产生大量内存碎片。
			复制算法：不可率内存碎片化问题。
			标记-整理算法：解决了内存碎片的问题。
			分代收集算法:jdk8 没有永久代，只有老年代和年轻代，年轻代：存活率低使用复制算法，老年代存活率高使用标记复制算法或者是标记整理算法。

				年轻代：eden区和两个survivor区。Eden:s0:s1 8:1:1。对象年龄一般大于15,被放到老年代，大对象放到老年代，eden和servivor放不下时放到老年代。
			常见的调优参数
				-XX:ServivorRatio:Eden和Survivor的比值，默认 8:1
				-XX:NewRatio:老年代和年轻代内存大小的比例。
				-XX:MaxTenuringThreshold:对象从新生代晋升到老年代的gc最大阈值。
	
			Major GC:年轻代垃圾清理
			Full GC:老年代gc时，也对年轻代gc,很慢，老年代空间不足，永久代空间不足jdk7-，调用System.gc()，是否会回收在于gc。
			
			finalizal()方法不会立即回收对象，却给对象一次重生的机会。

			强引用（不会垃圾回收），软引用（会垃圾回收），弱引用（随时垃圾回收），虚引用（不容易检测是否被回收）。


### 8.多线程与并发

		进程和线程区别?
		
				进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。
			线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，
			但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。
		
			1) 简而言之,一个程序至少有一个进程,一个进程至少有一个线程.

			2) 线程的划分尺度小于进程，使得多线程程序的并发性高。
			
			3) 另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。
			
			4) 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
			
			5) 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。
		
		run方法和start方法区别?

			run() 使用主线程, start() 创建一个线程。

		实现多线程方法?

			继承Thread类，实现Runnable接口，此时调用需要创建Thread类，调用start方法

		多线程返回值获取?

			1. 设置属性变量: 等待线程结束判断属性值，t.join()方法 / Thread.sleep()方法 
			2. 跟callable接口获取返回值

		
		

### 9.多线程与并发原理

		互斥锁:同一时间只允许一个线程持有某个对象锁，原子性。
			
		锁的分类:对象锁和类锁。
			对象锁：同步代码块 synchrozide(this/new Class){}
				   同步非静态方法 synchronized method
			类锁:同步代码块 synchronized(类.class){}
				同步静态方法 synchronized static method

		synchronized底层实现原理
	
			    Java 虚拟机中的同步(Synchronization)基于进入和退出管程(Monitor)对象实现， 无论是显式同步(有明确的 monitorenter 和 monitorexit 指令,即同步代码块)还是隐式同步都是如此。
			在 Java 语言中，同步用的最多的地方可能是被 synchronized 修饰的同步方法。
			同步方法 并不是由 monitorenter 和 monitorexit 指令来实现同步的，而是由方法调用指令读取运行时常量池中方法的 ACC_SYNCHRONIZED 标志来隐式实现的。

		线程池
			并发的请求比较多，频繁的创建和销毁线程时间较多，线程池可以重复利用线程。
			Executors java线程池 ThreadExecutors
			Thread t = new Thread();
			Executors.execute(t);
	
			线程池状态:running,shutdown,stop,tidying,terminated
			线程池大小:cpu密集型:线程数=cpu核数+1
					  io密集型:线程数=cpu核数*(1+平均等待时间/平均工作时间)
			
### 10.java常用类库及技巧

		String,StringBuffer,StringBuilder的区别
		
		数据结构:
			数组和链表的区别，链表操作，二叉树的遍历方式递归和非递归实现，红黑树的旋转,队列和栈的应用。

		算法
			内部排序:递归，交换（冒泡、快排）,选择排序，插入排序
			外部排序:有限的空间处理较大的数据集。

			快排、堆排序是不稳定。
	
		异常
	
			Throwable接口 : Error Exception.
				Error:系统错误,编译器不做检查，程序无法处理。找不到Class定义的异常，内存溢出溢出，栈溢出异常。
				Exception:程序可以处理的异常。
	
			RuntimeException:运行时异常。不可预知的，空指针异常,类型强制转换异常，下标越界，数字格式异常，传递非法参数
			CheckedException:可检查异常。找不到Class异常，IO异常。
			
			注:try catch性能不如if else高。


		java集合框架

			Collection:List,Set,Queue

		![集合图片](https://i.imgur.com/FHPCXiS.png)

			List
				特点:有序，可重复，可通过索引值操作元素
				分类:底层是数组，查询快，增删慢.ArrayList:线程不安全,效率高;Vector:线程安全,效率低。
				     底层是链表,查询慢，增删快,LinkedList:线程不安全效率高

			Set:
				特点:无序(存储和取出的顺序),元素唯一
				分类：底层是哈希表,HashSet：保证元素唯一。
					 底层是二叉树,TreeSet:保证元素排序：1.自然排序，让对象所属的类去实现comparable接口，无参构造。2.比较接口comparator,带参构造。

			hashmap 数组+链表 1.8+红黑色。 O(1)~O(n)-->O(1)~O(logn)

		

			
		

### 11.java框架-spring

		spring大家族:spring-core,spring-data,spring-security,spring-boot,spring-cloud...
		
		IOC原理:
			即“控制反转”，不是什么技术，而是一种设计思想。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。

			依赖注入含义:把底层类作为参数传递给上层类，实现上层对下层的控制。
			非依赖注入:new Object().

		springBean的作用域
			singleton,prototype,request,session,globlaSession.

		bean的生命周期容器管理：主要是创建和销毁。

		@Resource与@Autowired的区别

		
		spring-AOP:面向切面编程。
		关注点分离:不同的问题交给不同的部分去解决。
		业务功能代码与日志代码分离。

		AOP
		切面
		切入点
		通知:before前置,After最终,AfterReturning后置,AfterThrowing异常,Around环绕通知
		

		AOP原理

			JDKProxy和cglib 接口-代理类-真实类

		string事务
			隔离级别，事务传播


### 12.总结和展望

