

# mysql基础

## MySQL5.5安装配置

###  1.xshell连接虚拟机CentOS8(桥接模式)



### 2.CentOS 8下安装mysql5.5

1.下载mysql-server和mysql-client的rpm包

```
https://downloads.mysql.com/archives/community/
```

2.使用xftp将两个rpm包上传到/opt目录下

3.检查当前系统是否安装过mysql,没有安装则什么都不显示

```
rmp -qa|greap -i mysql
```

4.如果装过mysql,删除命令

```
rpm -e RPM软件包名

注:软件包名是rmp -qa|greap -i mysql 查询出来的名字
```

5.先在/opt目录下安装mysql-server 

```
##安装mysql服务server
[root@localhost opt]# rpm -ivh MySQL-server-5.5.48-1.linux2.6.x86_64.rpm 
警告：MySQL-server-5.5.48-1.linux2.6.x86_64.rpm: 头V3 DSA/SHA1 Signature, 密钥 ID 5072e1f5: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:MySQL-server-5.5.48-1.linux2.6   ################################# [100%]
210925  8:05:42 [Note] /usr/sbin/mysqld (mysqld 5.5.48) starting as process 7367 ...
210925  8:05:43 [Note] /usr/sbin/mysqld (mysqld 5.5.48) starting as process 7374 ...

PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:

/usr/bin/mysqladmin -u root password 'new-password'
/usr/bin/mysqladmin -u root -h localhost.localdomain password 'new-password'

Alternatively you can run:
/usr/bin/mysql_secure_installation

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the manual for more instructions.

Please report any problems at http://bugs.mysql.com/
```



6.安装mysql-client

```
##安装mysql客户端client
[root@localhost opt]# rpm -ivh MySQL-client-5.5.48-1.linux2.6.x86_64.rpm 
警告：MySQL-client-5.5.48-1.linux2.6.x86_64.rpm: 头V3 DSA/SHA1 Signature, 密钥 ID 5072e1f5: NOKEY
Verifying...                          ################################# [100%]
准备中...                          ################################# [100%]
正在升级/安装...
   1:MySQL-client-5.5.48-1.linux2.6   ################################# [100%]

```



> Tips:如果安装MYSQL-client出现依赖错误:

```
警告：MySQL-client-5.5.48-1.linux2.6.x86_64.rpm: 头V3 DSA/SHA1 Signature, 密钥 ID 5072e1f5: NOKEY
错误：依赖检测失败：
	libncurses.so.5()(64bit) 被 MySQL-client-5.5.48-1.linux2.6.x86_64 需要
```

解决方法:

需要安装两个依赖

```
ncurses-c++-libs-6.1-7.20180224.el8.x86_64                  
ncurses-compat-libs-6.1-7.20180224.el8.x86_64
```

```
安装命令:
yum -y install libncurses*
```

![image-20210924210240885](C:\Users\tom\AppData\Roaming\Typora\typora-user-images\image-20210924210240885.png)



7.查看Mysql安装时创建的mysql用户组和mysql组

```
##查看Mysql安装时创建的mysql用户组
[root@localhost opt]# cat /etc/passwd|grep mysql
mysql:x:975:974:MySQL server:/var/lib/mysql:/bin/bash
##查看Mysql安装时创建的mysql组
[root@localhost opt]# cat /etc/group|grep mysql
mysql:x:974:
```





8.查看mysql版本

```
##查看mysql版本
[root@localhost opt]# mysqladmin --version
mysqladmin  Ver 8.42 Distrib 5.5.48, for Linux on x86_64
```



### 3. 启动关闭Mysql

```
##后台启动mysql服务
[root@localhost opt]# service mysql start
Starting MySQL.... SUCCESS! 
```

```
##查看mysql启动状态
[root@localhost opt]# ps -ef|grep mysql
root      7450     1  0 08:09 ?        00:00:00 /bin/sh /usr/bin/mysqld_safe --datadir=/var/lib/mysql --pid-file=/var/lib/mysql/localhost.localdomain.pid
mysql     7543  7450  0 08:09 ?        00:00:00 /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --user=mysql --log-error=/var/lib/mysql/localhost.localdomain.err --pid-file=/var/lib/mysql/localhost.localdomain.pid
root      7606  6859  0 08:11 pts/1    00:00:00 grep --color=auto mysql
```

```
##此时没有设置mysql root密码　，可以直接登录．用来验证mysql是否启动成功
[root@localhost opt]# mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.5.48 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```



>  Tips:CentOS8启动MySQL时报错:

```
Starting MySQL. ERROR! The server quit without updating PID file
```

产生原因:

?

解决方法:

```
?
```



```
##关闭mysql服务
[root@localhost opt]# service mysql stop
Shutting down MySQL. SUCCESS! 

##或者通过脚本关闭
/etc/init.d/mysql stop
```



### 4.设置root密码

```
##设置root密码
[root@localhost opt]# /usr/bin/mysqladmin -u root password 123456

##设置完密码后,进行mysql无密码登录会失败
[root@localhost opt]# mysql
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)

##输入root用户和密码后可以使用mysql
[root@localhost opt]# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 6
Server version: 5.5.48 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```



### 5.设置开机自启动

```
##设置开机自启动
[root@localhost opt]# chkconfig mysql on

##查看mysql的自启动情况
[root@localhost opt]# chkconfig --list|grep mysql

注：该输出结果只显示 SysV 服务，并不包含
原生 systemd 服务。SysV 配置数据
可能被原生 systemd 配置覆盖。 

      要列出 systemd 服务，请执行 'systemctl list-unit-files'。
      查看在具体 target 启用的服务请执行
      'systemctl list-dependencies [target]'。

mysql          	0:关	1:关	2:开	3:开	4:开	5:开	6:关

##图形化展示查看当前系统自启动项,如果mysql自启动,则展示为 [*]mysql
[root@localhost opt]# ntsysv
```



### 6.Mysql安装位置

```
##通过 ps -ef|grep mysql 查看mysql安装目录
[root@localhost opt]# ps -ef|grep mysql
root      7662     1  0 08:14 ?        00:00:00 /bin/sh /usr/bin/mysqld_safe --datadir=/var/lib/mysql --pid-file=/var/lib/mysql/localhost.localdomain.pid
mysql     7753  7662  0 08:14 ?        00:00:00 /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --user=mysql --log-error=/var/lib/mysql/localhost.localdomain.err --pid-file=/var/lib/mysql/localhost.localdomain.pid
root      8275  6859  0 08:38 pts/1    00:00:00 grep --color=auto mysql
```

Linux下mysql的路径解释:

| 路径              | 解释                  | 备注                             |
| ----------------- | --------------------- | -------------------------------- |
| /var/lib/mysql/   | mysql数据库的存放路径 | /var/lib/mysql/atguigu.cloud.pid |
| /usr/share/mysql  | 配置文件目录          | mysql.server命令及配置文件       |
| /usr/bin          | 相关命令目录          | mysqladmin  mysqldump等命令      |
| /etc/init.d/mysql | 启动停止相关脚本      |                                  |



### 7.修改配置文件位置

#### 1.查看配置文件/usr/share/mysql目录下

```
##查看mysql配置文件列表
[root@localhost share]# cd /usr/share/mysql/
[root@localhost mysql]# ll
总用量 1268
-rwxr-xr-x. 1 root root   1153 1月  15 2016 binary-configure
drwxr-xr-x. 2 root root   4096 9月  25 08:05 charsets
-rwxr-xr-x. 1 root root   4528 1月  15 2016 config.huge.ini
-rwxr-xr-x. 1 root root   2382 1月  15 2016 config.medium.ini
-rwxr-xr-x. 1 root root   1626 1月  15 2016 config.small.ini
drwxr-xr-x. 2 root root     24 9月  25 08:05 czech
drwxr-xr-x. 2 root root     24 9月  25 08:05 danish
drwxr-xr-x. 2 root root     24 9月  25 08:05 dutch
drwxr-xr-x. 2 root root     24 9月  25 08:05 english
-rwxr-xr-x. 1 root root 470520 1月  15 2016 errmsg-utf8.txt
drwxr-xr-x. 2 root root     24 9月  25 08:05 estonian
-rwxr-xr-x. 1 root root 673773 1月  15 2016 fill_help_tables.sql
drwxr-xr-x. 2 root root     24 9月  25 08:05 french
drwxr-xr-x. 2 root root     24 9月  25 08:05 german
drwxr-xr-x. 2 root root     24 9月  25 08:05 greek
drwxr-xr-x. 2 root root     24 9月  25 08:05 hungarian
drwxr-xr-x. 2 root root     24 9月  25 08:05 italian
drwxr-xr-x. 2 root root     24 9月  25 08:05 japanese
drwxr-xr-x. 2 root root     24 9月  25 08:05 korean
-rwxr-xr-x. 1 root root    773 1月  15 2016 magic
-rwxr-xr-x. 1 root root   4697 1月  15 2016 my-huge.cnf
-rwxr-xr-x. 1 root root  19779 1月  15 2016 my-innodb-heavy-4G.cnf
-rwxr-xr-x. 1 root root   4671 1月  15 2016 my-large.cnf
-rwxr-xr-x. 1 root root   4682 1月  15 2016 my-medium.cnf
-rwxr-xr-x. 1 root root   2846 1月  15 2016 my-small.cnf
-rwxr-xr-x. 1 root root   1061 1月  15 2016 mysqld_multi.server
-rwxr-xr-x. 1 root root    789 1月  15 2016 mysql-log-rotate
-rwxr-xr-x. 1 root root  10815 1月  15 2016 mysql.server
-rwxr-xr-x. 1 root root   3448 1月  15 2016 mysql_system_tables_data.sql
-rwxr-xr-x. 1 root root  28980 1月  15 2016 mysql_system_tables.sql
-rwxr-xr-x. 1 root root  10411 1月  15 2016 mysql_test_data_timezone.sql
-rwxr-xr-x. 1 root root   1326 1月  15 2016 ndb-config-2-node.ini
drwxr-xr-x. 2 root root     24 9月  25 08:05 norwegian
drwxr-xr-x. 2 root root     24 9月  25 08:05 norwegian-ny
drwxr-xr-x. 2 root root     24 9月  25 08:05 polish
drwxr-xr-x. 2 root root     24 9月  25 08:05 portuguese
drwxr-xr-x. 2 root root     24 9月  25 08:05 romanian
drwxr-xr-x. 2 root root     24 9月  25 08:05 russian
drwxr-xr-x. 3 root root     19 9月  25 08:05 SELinux
drwxr-xr-x. 2 root root     24 9月  25 08:05 serbian
drwxr-xr-x. 2 root root     24 9月  25 08:05 slovak
drwxr-xr-x. 2 root root     24 9月  25 08:05 spanish
drwxr-xr-x. 2 root root     24 9月  25 08:05 swedish
drwxr-xr-x. 2 root root     24 9月  25 08:05 ukrainian

```



#### 2.复制my-huge.cnf到/etc/my.cnf

```
##复制mysql的mu-huge.cnf配置文件到/etc/my.cnf (mysql5.5)
[root@localhost ]# cp /usr/share/mysql/my-huge.cnf /etc/my.cnf

##tips:mysql5.6后要用
cp /usr/share/mysql/my-default.cnf /etc/my.cnf
```



> Tips:未修改之前的字符集 character_set_database   和character_set_server   是latin1  ,插入中文数据会乱码!

```
##查看字符集,
mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```



#### 3.修改配置文件/etc/my.cnf的内容

```
[client]
#password	= your_password
port		= 3306
socket		= /var/lib/mysql/mysql.sock
default-character-set=utf8  (添加此处)

[mysqld]
port		= 3306
character_set_server=utf8		(添加此处)
character_set_client=utf8		(添加此处)
collation-server=utf8_general_ci		(添加此处)

[mysql]
no-auto-rehash
default-character-set=utf8	(添加此处)
```



>  Tips:以下是完整的修改完成的my.cnf文件,可以直接复制使用

```
##修改配置my.cnf文件,将

[root@localhost mysql]# vi /etc/my.cnf
# Example MySQL config file for very large systems.
#
# This is for a large system with memory of 1G-2G where the system runs mainly
# MySQL.
#
# MySQL programs look for option files in a set of
# locations which depend on the deployment platform.
# You can copy this option file to one of those
# locations. For information about these locations, see:
# http://dev.mysql.com/doc/mysql/en/option-files.html
#
# In this file, you can use all long options that a program supports.
# If you want to know which options a program supports, run the program
# with the "--help" option.

# The following options will be passed to all MySQL clients
[client]
#password	= your_password
port		= 3306
socket		= /var/lib/mysql/mysql.sock
default-character-set=utf8  

# Here follows entries for some specific programs

# The MySQL server
[mysqld]
port		= 3306
character_set_server=utf8		
character_set_client=utf8		
collation-server=utf8_general_ci		
socket		= /var/lib/mysql/mysql.sock
skip-external-locking
key_buffer_size = 384M
max_allowed_packet = 1M
table_open_cache = 512
sort_buffer_size = 2M
read_buffer_size = 2M
read_rnd_buffer_size = 8M
myisam_sort_buffer_size = 64M
thread_cache_size = 8
query_cache_size = 32M
# Try number of CPU's*2 for thread_concurrency
thread_concurrency = 8

# Don't listen on a TCP/IP port at all. This can be a security enhancement,
# if all processes that need to connect to mysqld run on the same host.
# All interaction with mysqld must be made via Unix sockets or named pipes.
# Note that using this option without enabling named pipes on Windows
# (via the "enable-named-pipe" option) will render mysqld useless!
# 
#skip-networking

# Replication Master Server (default)
# binary logging is required for replication
log-bin=mysql-bin

# required unique id between 1 and 2^32 - 1
# defaults to 1 if master-host is not set
# but will not function as a master if omitted
server-id	= 1

# Replication Slave (comment out master section to use this)
#
# To configure this host as a replication slave, you can choose between
# two methods :
#
# 1) Use the CHANGE MASTER TO command (fully described in our manual) -
#    the syntax is:
#
#    CHANGE MASTER TO MASTER_HOST=<host>, MASTER_PORT=<port>,
#    MASTER_USER=<user>, MASTER_PASSWORD=<password> ;
#
#    where you replace <host>, <user>, <password> by quoted strings and
#    <port> by the master's port number (3306 by default).
#
#    Example:
#
#    CHANGE MASTER TO MASTER_HOST='125.564.12.1', MASTER_PORT=3306,
#    MASTER_USER='joe', MASTER_PASSWORD='secret';
#
# OR
#
# 2) Set the variables below. However, in case you choose this method, then
#    start replication for the first time (even unsuccessfully, for example
#    if you mistyped the password in master-password and the slave fails to
#    connect), the slave will create a master.info file, and any later
#    change in this file to the variables' values below will be ignored and
#    overridden by the content of the master.info file, unless you shutdown
#    the slave server, delete master.info and restart the slaver server.
#    For that reason, you may want to leave the lines below untouched
#    (commented) and instead use CHANGE MASTER TO (see above)
#
# required unique id between 2 and 2^32 - 1
# (and different from the master)
# defaults to 2 if master-host is set
# but will not function as a slave if omitted
#server-id       = 2
#
# The replication master for this slave - required
#master-host     =   <hostname>
#
# The username the slave will use for authentication when connecting
# to the master - required
#master-user     =   <username>
#
# The password the slave will authenticate with when connecting to
# the master - required
#master-password =   <password>
#
# The port the master is listening on.
# optional - defaults to 3306
#master-port     =  <port>
#
# binary logging - not required for slaves, but recommended
#log-bin=mysql-bin
#
# binary logging format - mixed recommended 
#binlog_format=mixed

# Uncomment the following if you are using InnoDB tables
#innodb_data_home_dir = /var/lib/mysql
#innodb_data_file_path = ibdata1:2000M;ibdata2:10M:autoextend
#innodb_log_group_home_dir = /var/lib/mysql
# You can set .._buffer_pool_size up to 50 - 80 %
# of RAM but beware of setting memory usage too high
#innodb_buffer_pool_size = 384M
#innodb_additional_mem_pool_size = 20M
# Set .._log_file_size to 25 % of buffer pool size
#innodb_log_file_size = 100M
#innodb_log_buffer_size = 8M
#innodb_flush_log_at_trx_commit = 1
#innodb_lock_wait_timeout = 50

[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash
default-character-set=utf8	
# Remove the next comment character if you are not familiar with SQL
#safe-updates

[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 256M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout


```



#### 4.修改后要重启mysql

```
###修改后重启mysql
[root@localhost mysql]# service mysql stop
Shutting down MySQL. SUCCESS! 
[root@localhost mysql]# service mysql start
Starting MySQL.. SUCCESS! 
```



> Tips:如果是在修改配置文件之前建的库,则还是乱码!

```
mysql> insert into user values(3,"李四");
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> select * from user;
+----+------+
| id | name |
+----+------+
|  1 | z3   |
|  2 | ??   |
|  3 | ??   |
+----+------+
3 rows in set (0.00 sec)
```



#### 5.打开mysql,重新建库建表.

```
##修改完my.cnf配置文件后,建立的库是utf8格式的,不会乱码
mysql> create database db02;
Query OK, 1 row affected (0.00 sec)

mysql> use db02
Database changed
mysql> 
mysql> show tables;
Empty set (0.00 sec)

mysql> create table user;
ERROR 1113 (42000): A table must have at least 1 column
mysql> create table user(id int not null,name varchar(20));
Query OK, 0 rows affected (0.01 sec)

mysql> insert into user values(1,"小明");
Query OK, 1 row affected (0.01 sec)

mysql> select * from user;
+----+--------+
| id | name   |
+----+--------+
|  1 | 小明   |
+----+--------+
1 row in set (0.00 sec)

```



## Mysql配置文件

### 1.二进制日志文件 log.bin

主从复制



### 2.错误日志 log.error

默认是关闭的,记录严重的警告和错误信息,每次启动和关闭的详细信息等



### 3.查询日志 log

默认关闭,记录查询的sql语句,如果开启会降低mysql的整体性能,因为记录日志文件也是需要消耗系统资源



### 4.数据文件

windows下: ..\MySQLServer5.5\data目录下可以挑选很多库

linux下:

默认路径/var/lib/mysql.

查看当前系统中的全部库再进去.

```
##查看当前系统中的全部库
[root@localhost mysql]# ls -1F|grep ^d
danish/
dutch/
```



### 5.frm文件

存放表结构



### 6.myd文件

存放表数据



### 7.myi文件

存放表索引





## Mysql逻辑架构

![image-20210925193350295](C:\Users\tom\AppData\Roaming\Typora\typora-user-images\image-20210925193350295.png)

Mysql架构可以在不同场景中应用并发挥良好作用.

主要体现在存储引擎的架构上,插件式的存储引擎架构将查询处理和其他的系统任务以及数据的存储提取相分离.

可以根据业务的实际需求选择合适的存储引擎.



### 1.连接层

 最上层是一些客户端和连接服务，包含本地sock通信和大多数基于客户端/服务端工具实现的类似于tcp/ip的通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。



### 2.服务层

- Management Serveices & Utilities： 系统管理和控制工具  
- SQL Interface: SQL接口,接受用户的SQL命令，并且返回用户需要查询的结果。比如select from就是调用SQL Interface
- Parser: 解析器, SQL命令传递到解析器的时候会被解析器验证和解析。 
- Optimizer: 查询优化器, SQL语句在查询之前会使用查询优化器对查询进行优化。用一个例子就可以理解： select uid,name from user where  gender= 1; 优化器来决定先投影还是先过滤。
-  Cache和Buffer： 查询缓存。如果查询缓存有命中的查询结果，查询语句就可以直接去查询缓存中取数据。这个缓存机制是由一系列小缓存组成的。比如表缓存，记录缓存，key缓存，权限缓存等缓存是负责读，缓冲负责写。



### 3.引擎层

存储引擎层，存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取。后面介绍MyISAM和InnoDB



### 4.存储层

  数据存储层，主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。





## Mysql存储引擎

### 1.查询引擎

```
##1.查询MySql数据库的存储引擎
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.12 sec)


##2.查看你的mysql当前默认的存储引擎
mysql> show variables like '%storage_engine%';
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| default_storage_engine | InnoDB |
| storage_engine         | InnoDB |
+------------------------+--------+
2 rows in set (0.00 sec)
```



### 2.每个引擎简介

1、InnoDB存储引擎
InnoDB是MySQL的默认事务型引擎，它被设计用来处理大量的短期(short-lived)事务。除非有非常特别的原因需要使用其他的存储引擎，否则应该优先考虑InnoDB引擎。行级锁，适合高并发情况

2、MyISAM存储引擎
MyISAM提供了大量的特性，包括全文索引、压缩、空间函数(GIS)等，但MyISAM不支持事务和行级锁(myisam改表时会将整个表全锁住)，有一个毫无疑问的缺陷就是崩溃后无法安全恢复。

3、Archive引擎
Archive存储引擎只支持INSERT和SELECT操作，在MySQL5.1之前不支持索引。
Archive表适合日志和数据采集类应用。适合低访问量大数据等情况。
根据英文的测试结论来看，Archive表比MyISAM表要小大约75%，比支持事务处理的InnoDB表小大约83%。

4、Blackhole引擎
Blackhole引擎没有实现任何存储机制，它会丢弃所有插入的数据，不做任何保存。但服务器会记录Blackhole表的日志，所以可以用于复制数据到备库，或者简单地记录到日志。但这种应用方式会碰到很多问题，因此并不推荐。

5、CSV引擎
CSV引擎可以将普通的CSV文件作为MySQL的表来处理，但不支持索引。
CSV引擎可以作为一种数据交换的机制，非常有用。
CSV存储的数据直接可以在操作系统里，用文本编辑器，或者excel读取。

6、Memory引擎
如果需要快速地访问数据，并且这些数据不会被修改，重启以后丢失也没有关系，那么使用Memory表是非常有用。Memory表至少比MyISAM表要快一个数量级。(使用专业的内存数据库更快，如redis)

7、Federated引擎
Federated引擎是访问其他MySQL服务器的一个代理，尽管该引擎看起来提供了一种很好的跨服务器的灵活性，但也经常带来问题，因此默认是禁用的。



### 3.InnoDB和MyISAM对比(重要)

| 对比项         | MyISAM                                                   | InnoDB                                                       |
| -------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 主外键         | 不支持                                                   | 支持                                                         |
| 事务           | 不支持                                                   | 支持                                                         |
| 行表锁         | 表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作 | 行锁,操作时只锁某一行，不对其它行有影响，适合高并发的操作    |
| 缓存           | 只缓存索引，不缓存真实数据                               | 不仅缓存索引还要缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
| 表空间         | 小                                                       | 大                                                           |
| 关注点         | 性能                                                     | 事务                                                         |
| 默认安装       | 是                                                       | 是                                                           |
| 用户表默认使用 | 否                                                       | 是                                                           |
| 自带系统表使用 | 是                                                       | 否                                                           |


innodb 索引 使用 B+TREE myisam 索引使用 b-tree
innodb 主键为聚簇索引，基于聚簇索引的增删改查效率非常高。



### 4.企业选择

Percona 为 MySQL 数据库服务器进行了改进，在功能和性能上较 MySQL 有着很显著的提升。该版本提升了在高负载情况下的 InnoDB 的性能、为 DBA 提供一些非常有用的性能诊断工具；另外有更多的参数和命令来控制服务器行为。

该公司新建了一款存储引擎叫xtradb完全可以替代innodb,并且在性能和并发上做得更好,

阿里巴巴大部分mysql数据库其实使用的percona的原型加以修改。
AliSql+AliRedis

