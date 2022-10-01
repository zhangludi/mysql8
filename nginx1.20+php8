https://blog.csdn.net/weixin_46115601/article/details/120986923

## 1. 下载所需安装包

	//关闭防火墙和selinux
	[root@localhost ~]# systemctl disable --now firewalld.service
		Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
	Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
	[root@localhost ~]# setenforce 0
	setenforce: SELinux is disabled

### //下载并解压nginx、mysql、php安装包
	[root@localhost src]# wget https://www.php.net/distributions/php-8.0.10.tar.gz
	[root@localhost src]# wget http://nginx.org/download/nginx-1.20.1.tar.gz
	[root@localhost src]# ls
	debug  kernels  mysql-5.7.34-linux-glibc2.12-x86_64.tar.gz  nginx-1.20.1.tar.gz  php-8.0.10.tar.gz
	[root@localhost src]# tar -xf mysql-5.7.34-linux-glibc2.12-x86_64.tar.gz -C /usr/local
	[root@localhost src]# tar -xf nginx-1.20.1.tar.gz -C /usr/local
	[root@localhost src]# tar -xf php-8.0.10.tar.gz -C /usr/local
	[root@localhost local]# ls
	bin  etc  games  include  lib  lib64  libexec  mysql-5.7.34-linux-glibc2.12-x86_64  nginx-1.20.1  php-8.0.10  sbin  share  src

## 2. 安装nginx

	// 创建系统用户nginx
	[root@localhost ~]# useradd -r -M -s /sbin/nologin nginx

	// 安装依赖关系
	[root@localhost ~]# yum -y install pcre-devel openssl openssl-devel gd-devel make gcc gcc-c++
	[root@localhost ~]# yum -y groups mark install 'Development Tools'

	// 创建日志存放目录
	[root@localhost ~]# mkdir -p /var/log/nginx
	//更改属组
	[root@localhost ~]# chown -R nginx.nginx /var/log/nginx

	// 编译安装
	[root@localhost local]# cd nginx-1.20.1
	[root@localhost nginx-1.20.1]# ./configure \
	--prefix=/usr/local/nginx \
	--user=nginx \
	--group=nginx \
	--with-debug \
	--with-http_ssl_module \
	--with-http_realip_module \
	--with-http_image_filter_module \
	--with-http_gunzip_module \
	--with-http_gzip_static_module \
	--with-http_stub_status_module \
	--http-log-path=/var/log/nginx/access.log \
	--error-log-path=/var/log/nginx/error.log

	[root@localhost nginx-1.20.1]# make && make install

	// 配置环境变量
	[root@localhost nginx-1.20.1]# echo 'export PATH=/usr/local/nginx/sbin:$PATH' > /etc/profile.d/nginx.sh
	[root@localhost nginx-1.20.1]# . /etc/profile.d/nginx.sh
	[root@localhost nginx-1.20.1]# which nginx
	/usr/local/nginx/sbin/nginx

## 4. 安装php

	// 下载依赖包
	[root@localhost ~]# yum -y install sqlite-devel libzip-devel libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libicu-devel libjpeg libjpeg-devel libpng libpng-devel openldap-devel pcre-devel freetype freetype-devel gmp gmp-devel readline readline-devel libxslt libxslt-devel

	// 编译安装
	[root@localhost]# cd /usr/local/php-8.0.10/
	[root@localhost php-8.0.10]#  ./configure --prefix=/usr/local/php8  \
	--with-config-file-path=/etc \
	--enable-fpm \
	--disable-debug \
	--disable-rpath \
	--enable-shared \
	--enable-soap \
	--with-openssl \
	--enable-bcmath \
	--with-iconv \
	--with-bz2 \
	--enable-calendar \
	--with-curl \
	--enable-exif  \
	--enable-ftp \
	--enable-gd \
	--with-jpeg \
	--with-zlib-dir \
	--with-freetype \
	--with-gettext \
	--enable-mbstring \
	--enable-pdo \
	--with-mysqli=mysqlnd \
	--with-pdo-mysql=mysqlnd \
	--with-readline \
	--enable-shmop \
	--enable-simplexml \
	--enable-sockets \
	--with-zip \
	--enable-mysqlnd-compression-support \
	--with-pear \
	--enable-pcntl \
	--enable-posix

	此时会出现下面这个报错，
	No package 'oniguruma' found
	//需要从网上下载，然后再安装
	[root@localhost php-8.0.10]# wget http://mirror.centos.org/centos/7/cloud/x86_64/openstack-queens/Packages/o/oniguruma-6.7.0-1.el7.x86_64.rpm
	[root@localhost php-8.0.10]# wget http://mirror.centos.org/centos/7/cloud/x86_64/openstack-queens/Packages/o/oniguruma-devel-6.7.0-1.el7.x86_64.rpm
	[root@localhost php-8.0.10]# yum -y install oniguruma*

	又会有下面的报错,这里是说对libzip版本有要求
	Requested 'libzip >= 0.11' but version of libzip is 0.10.1

	//先删除老版本
	[root@localhost php-8.0.10]# yum remove libzip-devel libzip

	//下载新的，编译安装
	[root@localhost php-8.0.10]# wget https://libzip.org/download/libzip-1.2.0.tar.gz --no-check-certificate
	[root@localhost php-8.0.10]# tar -zxvf libzip-1.2.0.tar.gz
	[root@localhost php-8.0.10]# cd libzip-1.2.0
	[root@localhost php-8.0.10]# ./configure 
	[root@localhost php-8.0.10]# make && make install

	找一下/usr/local/lib下有没有pkgconfig目录,有的话设置环境变量
	[root@localhost php-8.0.10]# export PKG_CONFIG_PATH="/usr/local/lib/pkgconfig/"

	//重新编译
	+--------------------------------------------------------------------+
	| License:                                                           |
	| This software is subject to the PHP License, available in this     |
	| distribution in the file LICENSE. By continuing this installation  |
	| process, you are bound by the terms of this license agreement.     |
	| If you do not agree with the terms of this license, you must abort |
	| the installation process at this point.                            |
	+--------------------------------------------------------------------+

	Thank you for using PHP.
	看到欢迎页面表示成功

	[root@localhost php-8.0.10]# make && make install


	//配置环境变量
	[root@localhost php-8.0.10]#  echo 'export PATH=/usr/local/php8/bin:$PATH' > /etc/profile.d/php.sh
	[root@localhost php-8.0.10]# source /etc/profile.d/php.sh
	[root@localhost php-8.0.10]# which php
	/usr/local/php8/bin/php
	[root@localhost php-8.0.10]# php -v
	PHP 8.0.10 (cli) (built: Oct 27 2021 11:29:54) ( NTS )
	Copyright (c) The PHP Group
	Zend Engine v4.0.10, Copyright (c) Zend Technologies

	// 配置php-fpm
	[root@localhost php-8.0.10]# cp -f /usr/local/php-8.0.10/php.ini-production /etc/php.ini
	[root@localhost php-8.0.10]# cp /usr/local/php-8.0.10/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm -f
	[root@localhost php-8.0.10]# chmod +x /etc/init.d/php-fpm 
	[root@localhost php-8.0.10]# cp -f /usr/local/php8/etc/php-fpm.conf.default /usr/local/php8/etc/php-fpm.conf
	[root@localhost php-8.0.10]# cp -f /usr/local/php8/etc/php-fpm.d/www.conf.default /usr/local/php8/etc/php-fpm.d/www.conf

	// 配置service文件
	[root@localhost php-8.0.10]# vim /usr/lib/systemd/system/php-fpm.service
	[root@localhost php-8.0.10]# cat /usr/lib/systemd/system/php-fpm.service
	[Unit]
	Description=Php-fpm server daemon
	After=network.target

	[Service]
	Type=forking
	ExecStart=service php-fpm start
	ExecStop=service php-fpm stop

	[Install]
	WantedBy=multi-user.target

	[root@localhost php-8.0.10]# service php-fpm start
	Starting php-fpm  done
	[root@localhost php-8.0.10]# ss -antl
	State       Recv-Q Send-Q                                 Local Address:Port                                                Peer Address:Port              
	LISTEN      0      128                                        127.0.0.1:9000                                                           *:*                  
	LISTEN      0      128                                                *:22                                                             *:*                  
	LISTEN      0      100                                        127.0.0.1:25                                                             *:*                  
	LISTEN      0      80                                                :::3306                                                          :::*                  
	LISTEN      0      128                                               :::22                                                            :::*                  
	LISTEN      0      100                                              ::1:25                                                            :::*       

  ## 5. nginx配置
  	[root@localhost ~]# vim /usr/local/nginx/conf/nginx.conf
        location / {
             root   html;
            index  index.php index.html index.htm;    ##添加index.php并放在第一位

         location ~ \.php$ {  #取消这几行的注释
              root           html;
            fastcgi_pass   127.0.0.1:9000;
             fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /usr/local/nginx/html$fastcgi_script_name; #将路径改为nginx的网页路径
            include        fastcgi_params;
        }


	//写一个php测试页面
	[root@localhost ~]# vim /usr/local/nginx/html/index.php
	[root@localhost ~]# cat /usr/local/nginx/html/index.php
	<?php 
	  phpinfo(); 
	?>
  
## 6. php配置

	[root@localhost ~]# vim /usr/local/php8/etc/php-fpm.d/www.conf
	 ; Unix user/group of processes
	 ; Note: The user is mandatory. If the group is not set, the default user's group
	 ;       will be used.
	 user = nginx            #改为nginx
	 group = nginx           #改为nginx

	; The address on which to accept FastCGI requests.


 ##  7. 重启服务并测试

	[root@localhost ~]# nginx -s stop;nginx
	[root@localhost ~]# service mysqld restart 
	Shutting down MySQL.. SUCCESS! 
	Starting MySQL. SUCCESS! 
	[root@localhost ~]# service php-fpm restart
	Gracefully shutting down php-fpm . done
	Starting php-fpm  done
	[root@localhost ~]# ss -antl
	State       Recv-Q Send-Q                                 Local Address:Port                                                Peer Address:Port              
	LISTEN      0      128                                        127.0.0.1:9000                                                           *:*                  
	LISTEN      0      128                                                *:80                                                             *:*                  
	LISTEN      0      128                                                *:22                                                             *:*                  
	LISTEN      0      100                                        127.0.0.1:25                                                             *:*                  
	LISTEN      0      80                                                :::3306                                                          :::*                  
	LISTEN      0      128                                               :::22                                                            :::*                  
	LISTEN      0      100                                              ::1:25                                                            :::*             

  
  
  
  
