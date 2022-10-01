# 影响数据的性能

## [服务器硬件 cpu/内存/磁盘IO](#cpu)

## [服务器操作系统](#os)

## [数据库存储引擎的选择](#mysql)
	
	MYISM 不支持事务,表级锁
	
	INNODB 事务存储引擎,完美的支持行级锁,事务ACID
	
## [数据库参数配置](#mysql-param)

## [数据库结构设计和sql语句](#mysql-sql)


<a id="cpu"> 服务器硬件 cpu/内存/磁盘IO</a>

## 一.cpu资源和可利用内存大小

### cpu 密集型

	衡量数据库处理能力的指标QPS
	
	64位不要使用32的服务器版本
	
### 内存

	选择服务器主板支持的最大内存频率
	
	内存越多越好,但也是限制
	
	多余的内存会增加操作系统等其他服务性能
	
 **内存购买**
 
 		组成购买升级
		
		每个频道的内存:相同的品牌,颗粒,频率,电压,校验,技术和型号
		
		单条容量尽可能大
		
## 二.磁盘的配置和选择

	使用传统的机器硬盘
	
	使用RAID增强传统机器硬盘的性能
	
	使用固态硬盘SSD和PCI-e卡
	
	使用网络存储NAS和SAN
	
	
### 使用传统的机器硬盘

	空间大,价格低,最常见,读写速度慢
	
#### 传统机器硬盘读取数据的过程

	1.移动磁头到磁盘 表面上的正确位置
	
	2.等待磁盘旋转,使所需要大的数据在磁头之下
	
	3.等待磁盘旋转过去,所有所需的数据都被磁头读出

#### 如何使用机器硬盘
	
	存储容量
	
	传输速度
	
	访问时间
	
	主轴转速
	
	物理尺寸
	
### 使用RAID增强传统机器硬盘的性能

	磁盘冗余队列的简称
		remdundant arrays of independent disks: 简单来说,RAID的作用就是可以把多个容量较小的磁盘组成一组更大的磁盘,并提供数据冗余来保证数据完整性的技术
		
#### RAID级别

		1. RAID0最早出现,也简称数据条带
			
			组成磁盘阵列中最简单的一种,只需要2块硬盘,成本低但没有提供冗余或修复错误的功能
		
		2.RAID1磁盘镜像
			
			原理上,一个磁盘的数据镜像到另一个磁盘上,也就是说数据在写入一个磁盘中同时也会在另一个闲置的磁盘上生成镜像文件,在不影响性能的情况下最大限度的保证系统的可靠性和可修复性
			
		3.RAID5 分布式奇偶校验磁盘阵列
			
			通过分布式奇偶块把数据分散到多个磁盘上,这样任何一个盘的数据失效都可以从奇偶块中重建,但是如果两块磁盘同时失效,则整个卷的数据都无法恢复==> 磁盘损坏,IO大幅度下降
		
		RAID10 分片镜像
		
			他是先对磁盘做RAID1之后在对RAID1的磁盘做RAID0,所以对读写有良好的性能,相对RAID5重建起来更简单,速度更快
		


| 等级  |特点  | 是否冗余 |盘数 | 读 | 写|
| :------------ |:---------------:| -----:|-----:|-----:|-----:|
| RAID0     | 便宜,快速,危险 | 否 |  N | 快 | 快 |
| RAID1       | 高速度,简单,安全        |  有 |  2 | 快 | 慢 |
| RAID5  |  安全,成本折中    |  有| N+1 | 快 | 取决于最慢的磁盘|
| RAID10 (适合读写频繁)      | 贵,高速,安全      |  有 |  2N | 快 | 快 |

### 使用固态硬盘SSD和PCI-e卡

		1.相对比机械磁盘,固态硬盘有更好的随机读写功能
		
		2.更好支持的并发
		
		3.SSD容易损坏
	
**特点**
	
	一.固态硬盘SSD
	
		1.使用SATA接口,可以替换传统硬盘,不要改变
	
		2.SATA接口的SSD支持的RAID
	
	二.PCI-e卡 fusion-io
	
		s不能用SATA接口,需要独特的驱动和配置
		
		价格贵,比SSD性能好
		
		PIC-e需要占内存,如果使用则需要扩展内存
		

**固态存储场景**

	1.适用于存在大量随机I/O场景
	
	2.用于解决单线程负载的I/O瓶颈
	
	3.固态存储设备用于从服务器
	
### 使用网络存储NAS和SAN

	SAN storage area network
	
	NAS network-attached storage
	
	是两种外部文件存储设备加载到服务器的方法
	

#### SAN
	
	通过光纤连接服务器,设备通过块接访问服务器,将其当硬盘使用
	
		大量的顺序读写
		
		读写I/O缓存合并
		
		随机读写慢,不如RAID
		
#### NAS

	使用网络连接,通过基于文件的协议如NFS或者SMB来访问
	
		数据库备份
		
**影响**

		延迟吞吐量
		
		网络宽带对性能的影响
		
		网络质量对性能的影响
		
**建议**

	采用高性能和搞宽带的网络设备和交换机
	
	对多个网卡进行绑定,增加可用性和带宽
	
	尽可能进行网络隔离
	
# 总结

## CPU
	
	64位cpu一定要工作在64位 系统下
	
	对于高并发下cpu的数量比频率重要
	
	对于cpu密集型场景和复杂sql,则频率越高越好
	
## 内存

	选择主板,使用最高频率 的内存
	
	内存的大小对性能很重要,所以尽可能的大
	
## I/O子 系统

	PIC-e卡->SSD->RAID10->磁盘->SAN(网络存储)
	
<a id="os"> 操作系统</a>

### windows

### FreeBSD

### Solaris

### linux centos ubuntu

### centos 参数优化

#### centos 内核参数优化 /etc/systctl.conf

	 net.core.somaxconn = 65535
	 
	 net.core.netdev_max_backlog = 65535
	 
	 net.ipv4.tcp_max_syn_backlog = 65535

#### 控制tcp等待状态,加快tcp回收
	
		net.ipv4.tcp_fin_timeout = 10
		
		net.ipv4.tcp_tw_reuse = 1
		
		net.ipv4.tcp_tw_recycle = 1
		
### tcp连接和发送缓冲区默认最大值
	
		net.core.wmen_default = 87380
		
		net.core.wmen_max = 16777216
		
		net.core.rmen_defalut = 87380
		
		net.core.rmen_max =  16777216
		
#### 加快资源,减少失联tcp的效率,网络参数

	
		net.ipv4.tcp_keeplive_time = 120
		
		net.ipv4.tcp_keeplive_intlvl = 30
		
		net.ipv4.tcp_keeplive_probes = 3
		

### 其他

#### kernel.shmmax = 4294967295
	
**注意**

		1.这个参数应该设置的足够大,以使能在一个共享内存段下容纳整个INNODB缓冲池的大小
		
		2.这个值对64位,可取最大值为物理值-1byte ,建议值为大于物理内存的一半,一般取值 大于INNODB缓存值大小即可,可以取物理值 -1byte
		
#### vm.swappiness = 0

	在mysql服务器上保留交换区还是很有必要,但是要如何控制交换区,这个参数告诉linux内核,除非虚拟内存被占满,否则不要使用交换区
	
	交换区swap:当操作系统没有足够的内存将一些虚拟内存写到磁盘的交换区中,这样就会发生内存交换
	
**不保留分区风险**

	降低操作系统性能
	
	容易造成内存溢出崩溃或被操作系统kill掉
	

#### 增加资源限制 /etc/security/limit.conf

放入末尾,重启才能生效

	soft nofile 65535
	
	hard nofile 65535 
	
**参数说明**
	
	* 表示对所有用户有效
	
	soft 表示当前系统生效
	
	hard 系统中所能设定的最大值
	
	nofile 所限制的资源是打开文件的最大数目
	
	65535 限制的数量
	
#### 磁盘调度策略 /sys/block/devname(磁盘的名称)/queue/scheduler

	cat  /sys/block/devname/queue/scheduler
	
	noop anticipatory deadline [cfg]
	
**参数说明**


	noop 电梯调度策略 : 对于闪存设备/RAM嵌入系统最好的,倾向于饿死读而利于写
	
	deadline 对于数据库应用是最好的
	
	anticipatory 预料I/O调度策略 适用于写入较多的环境
	
**eg**

	echo deadline > /sys/block/sda/queue/scheduler
	
### 文件系统对I/O性能

	windows : FAT NTFS
	
	linux : ext3 ext4 xfs(性能大于其他)
	
		ext3/ext4 系统挂在参数 /etc/fstab
		
		data = writeback(最好选择)/ordered(元数据)/journal(原子日志)
		
		noatime nodiratime 减少写操作
		
		/dev/sda1/ext4 notime,nodiratime,data = writeback 1 1


<a id="mysql"> mysql体系结构</a>

		插件式存储引擎
		
	mysql服务层
	
		链接管理器->查询缓存
		
		链接管理器->查询分析->查询优化
		
##### 存储引擎层 插件式(针对表不针对库)

		INNODB MYISAM CSV mermony xtraDB MRG_MYISAM archive  federated tokuDB 
		
		
		
### MYISAM 系统表,临时表(在排序,分组等操作中,当数量超过一定大小之后,由查询优化器建立临时表)

	将表myd和myi 组成
	
**特性**

	1.并发性和锁级别,读取和写入互斥,读写混合不友好
	
	2.表损坏修复,会造成数据丢失
	
		create table myisam(id int ,c1 varchar(10))
		
		ls -l myisam
		
		myisamchk -help(命令行修复,但是有可能 损坏更严重)
		
	3.myisam支持索引,全文索引
	
	4.支持数据压缩,不能写 操作,只能读
	
		myisampack 压缩
		
		myisam -b -f MyIsam.MYI
		
			限制:支持<5.0,默认表大小<4G,若存储大表max_rows和avg_row_length
			
			>5.0 支持256TB
	
**适用场景**

	非事务型应用
	
	报表应用,不涉及财务的应用
	
	只读类型应用
	
	空间类型应用 GPS数据
	
### INNODB >5.58

	支持事务引擎,适用表空间进行数据存储
	
#### 参数

	innodb_file_per_table
		
		on 独立表空间 tblname.idb
		
		off 系统表空间 ibdataX
	
	set global innodb_file_per_table = off/on
	
	ls -lh tablename*
	
**系统表空间和独立表空间如何选择**

	1.系统表空间无法简单的收缩文件大小 ,独立表空间可以通过optimize table命令收缩系统文件
	
	2.系统表空间会产生I/O瓶颈,独立表空间可以同时向多个文件刷新数据
	
**建议**

	>5.6 默认 使用独立表空间

**系统表空间转为独立表空间**

	1.使用mysqldump导出所有数据库表数据
	
	2.停止mysql服务修改参数,并删除 INNODB相关文件
	
	3.重启mysql服务,重建INNODB系统表空间
	
	4.重新导入数据
	
#### INNODB存储引擎如何选择

	innodb数据字典信息
	
	btree进行管理
	
	undo回滚段
	
	-frm mysql服务器字典
	
**特性**

	1.innodb事务存储引擎
	
	2.支持ACID
	
	3.redo log(已提交数据) 和 undo log(未提交数据) =>单独存储到SSD
	
		show variable like `innodb_log_buffer_size`
		
		show variable like `innodb_log_files_in_group` 
	
	4.支持行级锁
	
		行级锁可以最大程度支持并发
		
		行级锁有存储引擎层实现
		
### 行级锁

	管理共享资源并发访问
	
	锁用于实现事务的隔离性
	
	锁的类型:共享锁(读锁) 独占锁(写锁)
	
	desc tablename 查看表结构
	
	锁的粒度:表级锁 并发低
	
		lock table tbl_name write
	
	阻塞和死锁
	
		阻塞:不同锁兼容关系,慢查询
		
		死锁:两个或者两个以上事务占用相互等待资源,系统 自动处理
		
	INNODB 状态检查
	
		show engine innodb status 
		
### CSV存储引擎 文件系统存储特点

	以文本形式存储在文件中
	
	.csv文件存储表内容
	
	.csm文件存储表元数据,例如表的状态和表数量
	
	.frm文件存储表结构
	
**特点**

	以csv文件进行表存储
	
	所有列都必须不能为null
	
	不支持索引
	
	不适合大表,不适合在线处理
	
	可以对数据文件进行直接编辑
	
	more mycsv.csv
	
	flush tables 刷新文件
	
### Archive存储引擎

**特点**

	以zlib对表进行压缩,磁盘I/O更少
	
	数据存储在ARZ为后缀的文件
	
	只支持insert和select操作
	
	只支持在自增ID列上增加索引
	
**使用场景**

	日志和数据采集类应用
	
### memory存储引擎

	HEAP 存储引擎,数据保存在内存中
	
**特点**

	Hash(值查找)和btree(范围查找)索引
	
	所有的字段都为固定长度varcahr(10)=char(10)
	
	不能使用blog和txt
	
	表级锁
	
	最大大小由 max_heap_table_size 参数决定
	
	show index from tbl-name 查看索引
	
	show create table tbl-name\G
	
**容易混淆的概念**

	临时表:
	
	1.系统临时使用临时表,超过限制使用myism临时表,未超过限制使用memory
	
	2.create temporary table 建立索引
	
**场景**

	用于查找或者是影射 表,如邮编和地区的对应表
	
	用于保存数据分析中产生的中间表
	
	用于缓存同期性聚合数据的结果表
	
	memory数据易丢失,要求数据可再生性
	
### Frederated存储引擎

**特点**

	提供了访问远程mysql服务器表的方法
	
	本地不存储数据,数据全部放在远程服务器上
	
	本地需要保存表结构和远程服务器的连接信息
	
**如何使用**

	默认禁止 ,需要在启动是增加frederated=1参数
	
	mysql://severname[:passworrd]@hostname[:port_name]/db_name/table_name

	在mysql.conf增加frederated=1
	
**授权**
	
	grant select,update,insert,deleted on remote.remote_fred to fred_link@'127.0.0.1' identified by '1234567'
	
**远程**
	
	create table 'remote_fred'(
	
	id  int(11) auto_increment,
	
	c1 varchar(10) not null default '',
	
	c2 char(10) not null default '',
	
	primary key(id)
	
	)
	
**本地**
	
	create table 'remote_fred'(
	
	id  int(11) auto_increment,
	
	c1 varchar(10) not null default '',
	
	c2 char(10) not null default '',
	
	primary key(id)
	
	) engine=frederated connection="mysql://fred_link:1234567@127.0.0.1:3306/remote/remote_fred"
	
**场景**

	偶尔统计分析及查询

### 如何选择存储引擎

	事务
	
	备份 在线备份INNODB
	
	崩溃恢复
	
	存储引擎的存储特性
	
<a id="mysql-param"> 数据库参数设置</a>

### 服务器参数

#### mysql获取参数配置信息路径

**命令行参数**
	
		mysql_safe --datadir = /data/sql_data
	
**配置文件**
	
		mysql --help --verbose | grep -A 1 'Default options'
		
		/etc/my.cnf /etc/mysql/my.cnf /home/mysql/my.cnf ~/.my.cnf
		
**全局参数 全局配置需要重启服务器**

	set global 参数名=参数值
	
	set @@global:参数名=参数值
	
**回话参数**

	set [session] 参数名=参数值
	
	set @@session:参数名=参数值
	
	show variables where variable_name='wait_timeout' or variable_name='interactive_timeout'
	
	需要同时设置,否则取最大值
	
		set global wait_time = 3600
	
		set global interactive_timeout = 3600
	
### 内存相关参数
	
	1.确定可使用的内存上限
	
	2.确定mysql每个连接使用内存
	
	倍数级别
		
		sort_buffer_size 缓存区内存的排序
		
		join_buffer_size 连接缓冲区的大小,设置小
		
		read_buffer_size myisam全文扫描
		
		read_rnd_buffer_size 索引内存
		
	3.确定需要操作系统需要多少内存
	
	4.如何为缓存池分配内存 ,需要重启
	
		innnodb_buffer_pool_size
		
		总内存-(每个线程所需要的内存*连接数)-系统保留内存
		
		key_buffer_size myisam存储引擎
		
	select sum(index_length) from information_schema.tables where engin=engin=myisam
	
### I/O配置参数

	决定mysql同步缓存池的数据到磁盘上,以实现数据持久化的保存,对性能影响比较大
	
#### INNODB

	innodb_log_file_size 控制单个日志的大小  32-128M
	
	innodb_log_files_in_group 个数
	
	事务日志大小 = innodb_log_files_in_group*innodb_log_file_size
	
	innodb_flush_log_at_trx_commit
	
		1[默认]每秒进行一次log写入flush log到磁盘,默认在每次事务比较执行log写入cacahe,并flush log到磁盘
		
		2[建议]每次事务提交执行log数据写入到cache,每秒执行一次flush log到磁盘[进程失败,数据不丢失]
		
	innodb_flush_method = O_Direct 不缓存不预读
	
	innodb_file_per_table = 1 每个表空间
	
	Innodb_doublewrite = 1 避免数据损坏

#### myisam 

	delay_key_write
	
		off 每次写操作后刷新键缓冲中的脏块到磁盘
		
		On 只对在键表时指定了delay_key_write 选项的表使用了延迟刷新
		
		All 对所有myisam表使用延迟键入
		
#### 安全相关参数

	expire_logs_days 指定自动清理bin log的天数
	
	max_allowed_packet 控制mysql可以接收包的连接数(32M)
	
	skip_name_resolve 禁用DNS查找,启用需要对ip设定访问
	
	sysdate_is_now sysdate()返回确定性日期
	
	read_only 禁用非super权限用户权限,从数据使用
	
	skip_slave_start 禁用slave自动恢复,从数据库使用
	
	sql_mode 设置mysql所使用的sql模式
	
		strict_trans_tables
		
		no_engine_subtitution
		
		no_zero_date 0年写入,不可以
		
		no_zero_in_date 
		
		only_full_group_by
		
##### 其他

	sync_binlog =1 成本最高,主机,控制mysql如何向磁盘刷新binlog
	
	tmp__table_size 和 max_heap_table_size 控制内存表临时表大小
	
<a id="mysql-sql"> 数据设计结构</a>

	过分的反范式化为表建立太多的列
	
	太多的范式化造成太多标的关联,最多关联61个表
	
	在OLP环境中使用不恰的分区表
	
	使用外键保证数据的完整性
	
	
# 顺序

mysql设计结构和sql语句 
->存储引擎选择和参数配置
->系统选择及优化
->硬件升级





























