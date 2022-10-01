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
		
		
















