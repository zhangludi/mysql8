
https://blog.csdn.net/Wanguyunxiaodaniu/article/details/105903435

## 一、从哪里可以下载到sysbench：

	https://github.com/akopytov/sysbench【别人的一个链接地址】

## 二、sysbench的一些安装依赖：

	yum -y install  make automake libtool pkgconfig libaio-devel vim-common
	
## 四、验证sysbench是否安装成功

	sysbench --version

## 五、测试

### 1）测试cpu: 
	--cpu-max-prime=N upper limit for primes generator [10000]
	
		sysbench --test=cpu --cpu-max-prime=2000 run
		
			sysbench 通过素数运算来测试 cpu，--cpu-max-prime 参数指定计算到最大的素数范围。默认是在十秒内计算，因此这种情况下，events 越大说明 cpu 的性能越好。一个 event 为从最小素数计算到最大素数（--cpu-max-prime）。
			
			Latency 和 General statistics 可以知道开启 15 线程时，延迟 (查看 avg) 提高了，但是吞吐量(total events)增加了。通过开启不同线程的测试，列出数据，可以找到最佳线程数量，低延迟，高吞吐。




### 2）测试线程：

	sysbench  --test=threads --num-threads=500 --thread-yields=100 --thread-locks=4 run，结果如下图：
	
	


### 3）测试IO：

	sysbench fileio help
	数据库的瓶颈就是 磁盘 io，这块需要我们好好测试。异步读写，读写模式（随机/顺序） 等。

		—file-test-mode 指定文件的读写模式。参考《Mysql DBA 修炼之道》。

		seqwr ：顺序读写。
		seqrewr： 顺序重写。
		seqrd：顺序读。
		rndrd：随机读。
		rndwr：随机写。
		rndrw：随机读写。

	# 生成测试文件
	sysbench fileio prepare --threads=15 --file-num=50 --file-total-size=15G --file-test-mode=rndrw

	# 测试数据
	sysbench fileio run --threads=15 --file-num=50 --file-total-size=15G --file-test-mode=rndrw --time=60

	# 删除生成的测试文件
	sysbench fileio cleanup --threads=15 --file-num=50 --file-total-size=15G --file-test-mode=rndrw --time=60
	
	
	
	--num-threads 开启的线程    --file-total-size 总的文件大小

		1，prepare阶段，生成需要的测试文件，完成后会在当前目录下生成很多小文件。

		sysbench --test=fileio --num-threads=16 --file-total-size=2G --file-test-mode=rndrw prepare

	2，run阶段

		sysbench --test=fileio --num-threads=20 --file-total-size=2G --file-test-mode=rndrw run

	3，清理测试时生成的文件

		sysbench --test=fileio --num-threads=20 --file-total-size=2G --file-test-mode=rndrw cleanup
		
		

### 4)测试内存：
	--memory-block-size 定义一次 event 操作的大小，--memory-total-size 指定总的大小。
	
	sysbench memory run --memory-block-size=1M --memory-total-size=50G --memory-access-mode=rnd --memory-oper=write --threads=20
	每次操作 1M ，total number of events:51200，total time:7.4958 说明 10 秒内完成了操作。
	
	sysbench memory run --memory-block-size=1M --memory-total-size=100G --memory-access-mode=rnd --memory-oper=write --threads=20
	
	10 秒传输的总的数据量为 69G 左右。每次操作 1M ，total number of events:69217，total time:10.019` 说明 10 秒内只传输了 69G 的数据量。



	sysbench --test=memory --memory-block-size=8k --memory-total-size=1G run



### 5)测试mutex：
	sysbench –test=mutex –num-threads=100 –mutex-num=1000 –mutex-locks=100000 –mutex-loops=10000 run
	
## 分析的sh  analzy.sh
	#!/bin/bash
	awk '
	   BEGIN {
		 printf "#ts date time load QPS";
		 fmt=" %.2f";
	   }
	   /^TS/ {
	   ts = substr($2,1,index($2,".")-1);
	   load = NF -2;
	   diff = ts - prev_ts;
	   printf "\n%s %s %s %s",ts,$3,$4,substr($load,1,length($load)-1);
	   prev_ts=ts;
	   }
	   /Queries/{
	   printf fmt,($2-Queries)/diff;
	   Queries=$2
	   }
	   ' "$@"
	   
## get_test_info.sh

	#!/bin/bash
	INTERVAL=5   #收集时间
	PREFIX=/home/imooc/benchmarks/$INTERVAL-sec-status  #把收集的信息记录在哪里
	RUNFILE=/home/imooc/benchmarks/running   #记录运行标识,删除这个文件停止运行
	echo "1" > $RUNFILE  #生成运行标识的文件
	MYSQL=/usr/local/mysql/bin/mysql   #mysql 命令所在的位置
	$MYSQL -e "show global variables" >> mysql-variables  #记录mysql 的设置信息
	while test -e $RUNFILE; do  #循环 只要标示文件存在,循环就会一直继续
		file=$(date +%F_%I)     # 9-11 定义了脚本的运行时间,文件名,每隔多长时间运行
		sleep=$(date +%s.%N | awk '{print 5 - ($1 % 5)}')
		sleep $sleep
		ts="$(date +"TS %s.%N %F %T")"
		loadavg="$(uptime)"   # 收集系统的负载情况
		echo "$ts $loadavg" >> $PREFIX-${file}-status   # 收集系统的负载情况 记录到文件中
		$MYSQL -e "show global status" >> $PREFIX-${file}-status &    #收集了mysql全局的状态信息
		echo "$ts $loadavg" >> $PREFIX-${file}-innodbstatus  #收集了mysql全局的状态信息,记录到文件中
		$MYSQL -e "show engine innodb status" >> $PREFIX-${file}-innodbstatus &   #收集了innnodb的状态信息
		echo "$ts $loadavg" >> $PREFIX-${file}-processlist
		$MYSQL -e "show full processlist\G" >> $PREFIX-${file}-processlist &   #收集当前连接线程的情况
		echo $ts
	done
	echo Exiting because $RUNFILE does not exists
