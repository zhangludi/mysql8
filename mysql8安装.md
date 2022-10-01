https://www.cnblogs.com/oldboy666/p/15558992.html


老男孩MySQL书籍外链地址。
 
## 1.安装环境
	系统：CentOS Linux7.9
	[root@oldboy opt]# cat /etc/redhat-release
	CentOS Linux release 7.9.2009 (Core)

	数据库：mysql8.0.26

### 1)关闭selinux:
	setenforce 0
	getenforce
	sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/selinux/config

 
## 2.下载mysql软件包
	注意：下载地址为https://mirrors.aliyun.com/mysql/MySQL-8.0/
	cd /opt
	wget https://mirrors.aliyun.com/mysql/MySQL-8.0/mysql-community-client-8.0.27-1.el7.x86_64.rpm
	wget https://mirrors.aliyun.com/mysql/MySQL-8.0/mysql-community-devel-8.0.27-1.el7.x86_64.rpm
	wget https://mirrors.aliyun.com/mysql/MySQL-8.0/mysql-community-client-plugins-8.0.27-1.el7.x86_64.rpm 
	wget https://mirrors.aliyun.com/mysql/MySQL-8.0/mysql-community-libs-8.0.27-1.el7.x86_64.rpm
	wget https://mirrors.aliyun.com/mysql/MySQL-8.0/mysql-community-common-8.0.27-1.el7.x86_64.rpm
	wget https://mirrors.aliyun.com/mysql/MySQL-8.0/mysql-community-server-8.0.27-1.el7.x86_64.rpm

	[root@oldboy opt]# ls
	mysql-community-client-8.0.27-1.el7.x86_64.rpm         
	mysql-community-devel-8.0.27-1.el7.x86_64.rpm
	mysql-community-client-plugins-8.0.27-1.el7.x86_64.rpm 
	mysql-community-libs-8.0.27-1.el7.x86_64.rpm
	mysql-community-common-8.0.27-1.el7.x86_64.rpm         
	mysql-community-server-8.0.27-1.el7.x86_64.rpm

	注意：安装mysql-community-server前，需要先安装下面依赖包：
	mysql-community-client        
	mysql-community-client-plugins
	mysql-community-common        
	mysql-community-libs          
	net-tools                     

 
 
## 3.删除已有的mariadb，防止和mysql冲突
	[root@oldboy opt]# rpm -qa|grep mariadb
	mariadb-libs-5.5.68-1.el7.x86_64
	[root@oldboy opt]# yum remove mariadb-libs -y
 
## 4.按顺序安装如下软件包（有依赖关系，切记顺序不能乱）
	[root@oldboy opt]# yum install net-tools libaio-devel -y
	[root@oldboy opt]# rpm -ivh mysql-community-common-8.0.27-1.el7.x86_64.rpm
	[root@oldboy opt]# rpm -ivh mysql-community-client-plugins-8.0.27-1.el7.x86_64.rpm
	[root@oldboy opt]# rpm -ivh mysql-community-libs-8.0.27-1.el7.x86_64.rpm
	[root@oldboy opt]# rpm -ivh mysql-community-client-8.0.27-1.el7.x86_64.rpm
	[root@oldboy opt]# rpm -ivh mysql-community-server-8.0.27-1.el7.x86_64.rpm

	[root@oldboy opt]# rpm -qa|grep mysql
	mysql-community-client-8.0.26-1.el7.x86_64
	mysql-community-common-8.0.26-1.el7.x86_64
	mysql-community-libs-8.0.26-1.el7.x86_64
	mysql-community-client-plugins-8.0.26-1.el7.x86_64
	mysql-community-server-8.0.26-1.el7.x86_64
 
## 5.初始化，并启动MySQL
	[root@oldboy opt]# mysqld --initialize-insecure --user=mysql
	[root@oldboy opt]# systemctl start mysqld
	[root@oldboy opt]# netstat -lntup|grep 330
	tcp6       0      0 :::33060                :::*                    LISTEN      17874/mysqld       
	tcp6       0      0 :::3306                 :::*                    LISTEN      17874/mysqld

## 6.启动并检查端口存在后，则输入mysql登录查看。
	[root@oldboy opt]# mysql
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 8
	Server version: 8.0.26 MySQL Community Server - GPL
	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
	mysql> create database oldboy;
	Query OK, 1 row affected (0.00 sec)

	mysql> show databases;
	+--------------------+
	| Database           |
	+--------------------+
	| information_schema |
	| mysql              |
	| oldboy             |
	| performance_schema |
	| sys                |
	+--------------------+
	5 rows in set (0.01 sec)

	mysql> quit
	over，成功。
