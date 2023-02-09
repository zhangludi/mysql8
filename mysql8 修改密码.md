https://www.cnblogs.com/Extnet/p/10044559.html

问题是这样

刚霍霍了一台腾讯云服务器需要安装mysql

然后就选择了8+这个版本。

安装步骤网上有的是。

我只写最主要的部分 绝对不出错 外网可访问 .net java都可以调用

其实不指望有人看 就是怕自己下一次忘记了

登录不解释

mysql -uroot -p
更改加密方式

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
更改密码：

1
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
　　

刷新：

FLUSH PRIVILEGES;
// 如果报错   ERROR 1396 (HY000): Operation ALTER USER failed for 'root'@'%' ：

则是远程访问权限不正确，先选择数据库，查看一下再更改：

复制代码
use mysql;
Database changed

update user set host = 'localhost' where user ='root';

把上面的再来一遍

update user set host = '%' where user ='root';

远程链接也直接就解决了

FLUSH PRIVILEGES;


GRANT ALL PRIVILEGES ON *.* TO root@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
