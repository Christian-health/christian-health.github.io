## 1、登录mysql有两种方法：
```
TCP/IP方式（远程、本地）：
mysql -uroot -poldboy123 -h 10.0.0.51 -P3306
Socket方式(仅本地)：
mysql -uroot -poldboy123 -S /tmp/mysql.sock
```
mysql启动的时候就可以设置使用的内存大小是多少。mysql启动的时候，告诉操作系统，我mysql很重要，所以我要使用的内存是多少。你操作系统分配给我，如果其他的进程需要使用内存，那么不能枪我mysql的。

## 2、MySQL实例的构成

公司： 老板  +  经理  + 员工 + 办公区  
实例： mysqld +  master thread   + 干活的Thread  + 预分配的内存

## 3、MySQL中mysqld服务器进程结构

![mysqld程序结构.JPG](https://github.com/Christian-health/christian-health.github.io/blob/master/img/mysqld%E7%A8%8B%E5%BA%8F%E7%BB%93%E6%9E%84.JPG?raw=true)

mysqld是最重要的，如果没有mysqld，那么就不会有后面的创建master thread、干活的Thread、预分配的内存，这些都是mysqld做的。

### 3.1 、SQL语句引入

结构化的查询语言  
DQL   数据查询语言  
DDL   数据定义语言  
DML   数据操作语言  
DCL   数据控制语言  

```
例如：  mysql> select user,host from mysql.user;
```

### 3.2、 连接层功能
对应前面的图mysqld程序结构上的链接层。  
（1） 提供连接协议  
		Socket     
		TCPIP  
（2） 验证用户名（root@localhost）密码合法性，进行匹配专门的授权表。  
（3） 派生一个专用连接线程（接收SQL，返回结果），多一个会话就会多一个出来线程，多一个查询结果。  
默认最多支持151个会话，也有参数控制这个默认值。会话8个小时没有动作就会退出去，这个也有参数设定。  

​	  mysql> show processlist;  

```sql
mysql> show processlist;
+----+------+-----------+------+---------+------+----------+------------------+
| Id | User | Host      | db   | Command | Time | State    | Info             |
+----+------+-----------+------+---------+------+----------+------------------+
|  2 | root | localhost | NULL | Query   |    0 | starting | show processlist |
+----+------+-----------+------+---------+------+----------+------------------+
1 row in set (0.16 sec)

```

（4）讲语句交给下一层  

思考：   
忘记密码的参数在哪做的手脚？  
--skip-grant-tables    
--skip-networking	    

### 3.3 、SQL层（优化方面至关重要的）  
我们现在使用的sql标准是sql92。  
（1）验证SQL语法和SQL_MODE（是一组mysql支持的基本语法及校验规则 ）
mysql 5.7将mysql的sql语句的语法制定成为严丝合缝的国际标准，5.7以前的版本都不严谨，可能会存放错误的数据进去。
```
mysql> select @@sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------+
| @@sql_mode                                                                                                                                |
+-------------------------------------------------------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
+-------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.07 sec)
```
（2）验证语义 =》到底是DDL DQL ，DDL，DML ，DCL  ？   
（3）验证权限   
        **预处理包括，解析，优化，评估。从这里往后，一直到sql执行都属于预处理。**    
（4）解析器进行语句解析，生成执行计划（解析树）    
（5）优化器（各种算法，基于执行代价的算法），根据算法，找到代价最低的执行计划。（预执行了一下）  
	代价：执行时候使用的CPU  IO  MEM是多少  
	**8.0版本的优化器的算法是最好的。**  
（6）执行器按照优化器选择执行计划，执行SQL语句，得出获取数据的方法。  
（7）提供query cache(默认不开)，一般不开，会用redis  
（8）记录操作日志（binlog），默认没开  
审计日志（记录执行过的语句的），通用日志，binlog二进制日志  
![mysqld程序结构-解释.jpg](https://github.com/Christian-health/christian-health.github.io/blob/master/img/mysqld%E7%A8%8B%E5%BA%8F%E7%BB%93%E6%9E%84-%E8%A7%A3%E9%87%8A.jpg?raw=true)

### 3.4 存储引擎层（mysql内置的File system）
真正和磁盘打交道的一个层次  
根据SQL层提供的取数据的方法，拿到数据，返回给SQL层，结构化成表，再又连接层线程返回给用户。  

### 3.5 MySQL逻辑存储结构

库 	                                    --相当于-------------------------》Linux目录  
create database wordpress charset utf8mb4;    ----》     mkdir /wordpress  
show databases;							    ----》    ls /  
use wordpress;							    ----》    cd /wordpress  
表                  								  ----》Linux的文件  
列（字段）               								无  
列属性			      								无  
数据行（记录）		         				  ----》Linux数据行  
表属性（元数据）							  ----》Linux 文件属性  

### 3.6 MySQL物理存储结构
库： 使用FS上的目录来表示   
```
[root@localhost data]# pwd
/data/mysql/data    进入磁盘上用来存放mysql数据的磁盘目录
[root@localhost data]# ls -l
总用量 122952
-rw-r-----. 1 mysql mysql       56 8月   8 10:05 auto.cnf
-rw-r-----. 1 mysql mysql      321 8月   8 11:47 ib_buffer_pool
-rw-r-----. 1 mysql mysql 12582912 8月   8 15:32 ibdata1
-rw-r-----. 1 mysql mysql 50331648 8月   8 15:32 ib_logfile0
-rw-r-----. 1 mysql mysql 50331648 8月   8 10:05 ib_logfile1
-rw-r-----. 1 mysql mysql 12582912 8月   8 15:32 ibtmp1
-rw-r-----. 1 mysql mysql    29841 8月   8 15:32 localhost.localdomain.err
-rw-r-----. 1 mysql mysql        5 8月   8 15:32 localhost.localdomain.pid
drwxr-x---. 2 mysql mysql     4096 8月   8 10:05 mysql 可以看到一个数据库就是一个目录
比如，show databases可以看到这个mysql数据库，然后我进入这个目录可以看到有一个mysql的目录。我如果在这里创建了一个目录，那么我也可以在show databases哪里看到新添加了一个数据库
drwxr-x---. 2 mysql mysql     8192 8月   8 10:05 performance_schema
drwxr-x---. 2 mysql mysql     8192 8月   8 10:05 sys
[root@localhost data]# cd mysql  进入一个数据库对应的目录
[root@localhost mysql]# ls -l
总用量 11896
-rw-r-----. 1 mysql mysql    8820 8月   8 10:05 columns_priv.frm
-rw-r-----. 1 mysql mysql       0 8月   8 10:05 columns_priv.MYD
-rw-r-----. 1 mysql mysql    4096 8月   8 10:05 columns_priv.MYI
-rw-r-----. 1 mysql mysql    9582 8月   8 10:05 db.frm
-rw-r-----. 1 mysql mysql     976 8月   8 10:05 db.MYD
-rw-r-----. 1 mysql mysql    5120 8月   8 10:05 db.MYI
-rw-r-----. 1 mysql mysql      65 8月   8 10:05 db.opt
-rw-r-----. 1 mysql mysql    8780 8月   8 10:05 engine_cost.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 engine_cost.ibd
-rw-r-----. 1 mysql mysql   10223 8月   8 10:05 event.frm
-rw-r-----. 1 mysql mysql       0 8月   8 10:05 event.MYD
-rw-r-----. 1 mysql mysql    2048 8月   8 10:05 event.MYI
-rw-r-----. 1 mysql mysql    8665 8月   8 10:05 func.frm
-rw-r-----. 1 mysql mysql       0 8月   8 10:05 func.MYD
-rw-r-----. 1 mysql mysql    1024 8月   8 10:05 func.MYI
-rw-r-----. 1 mysql mysql      35 8月   8 11:47 general_log.CSM
-rw-r-----. 1 mysql mysql       0 8月   8 10:05 general_log.CSV
-rw-r-----. 1 mysql mysql    8776 8月   8 10:05 general_log.frm
-rw-r-----. 1 mysql mysql    8784 8月   8 10:05 gtid_executed.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 gtid_executed.ibd
-rw-r-----. 1 mysql mysql    8700 8月   8 10:05 help_category.frm
-rw-r-----. 1 mysql mysql  114688 8月   8 10:05 help_category.ibd
-rw-r-----. 1 mysql mysql    8612 8月   8 10:05 help_keyword.frm
-rw-r-----. 1 mysql mysql  245760 8月   8 10:05 help_keyword.ibd
-rw-r-----. 1 mysql mysql    8630 8月   8 10:05 help_relation.frm
-rw-r-----. 1 mysql mysql  131072 8月   8 10:05 help_relation.ibd
-rw-r-----. 1 mysql mysql    8770 8月   8 10:05 help_topic.frm
-rw-r-----. 1 mysql mysql 9437184 8月   8 10:05 help_topic.ibd
-rw-r-----. 1 mysql mysql   12982 8月   8 10:05 innodb_index_stats.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 innodb_index_stats.ibd
-rw-r-----. 1 mysql mysql    8830 8月   8 10:05 innodb_table_stats.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 innodb_table_stats.ibd
-rw-r-----. 1 mysql mysql    8986 8月   8 10:05 ndb_binlog_index.frm
-rw-r-----. 1 mysql mysql       0 8月   8 10:05 ndb_binlog_index.MYD
-rw-r-----. 1 mysql mysql    1024 8月   8 10:05 ndb_binlog_index.MYI
-rw-r-----. 1 mysql mysql    8586 8月   8 10:05 plugin.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 plugin.ibd
-rw-r-----. 1 mysql mysql    9996 8月   8 10:05 proc.frm
-rw-r-----. 1 mysql mysql  300528 8月   8 10:05 proc.MYD
-rw-r-----. 1 mysql mysql    4096 8月   8 10:05 proc.MYI
-rw-r-----. 1 mysql mysql    8875 8月   8 10:05 procs_priv.frm
-rw-r-----. 1 mysql mysql       0 8月   8 10:05 procs_priv.MYD
-rw-r-----. 1 mysql mysql    4096 8月   8 10:05 procs_priv.MYI
-rw-r-----. 1 mysql mysql    8800 8月   8 10:05 proxies_priv.frm
-rw-r-----. 1 mysql mysql     837 8月   8 10:05 proxies_priv.MYD
-rw-r-----. 1 mysql mysql    9216 8月   8 10:05 proxies_priv.MYI
-rw-r-----. 1 mysql mysql    8692 8月   8 10:05 server_cost.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 server_cost.ibd
-rw-r-----. 1 mysql mysql    8838 8月   8 10:05 servers.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 servers.ibd
-rw-r-----. 1 mysql mysql   10908 8月   8 10:05 slave_master_info.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 slave_master_info.ibd
-rw-r-----. 1 mysql mysql    9468 8月   8 10:05 slave_relay_log_info.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 slave_relay_log_info.ibd
-rw-r-----. 1 mysql mysql    9364 8月   8 10:05 slave_worker_info.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 slave_worker_info.ibd
-rw-r-----. 1 mysql mysql      35 8月   8 11:47 slow_log.CSM
-rw-r-----. 1 mysql mysql       0 8月   8 10:05 slow_log.CSV
-rw-r-----. 1 mysql mysql    9016 8月   8 10:05 slow_log.frm
-rw-r-----. 1 mysql mysql    8955 8月   8 10:05 tables_priv.frm
-rw-r-----. 1 mysql mysql    1894 8月   8 10:05 tables_priv.MYD
-rw-r-----. 1 mysql mysql    9216 8月   8 10:05 tables_priv.MYI
-rw-r-----. 1 mysql mysql    8636 8月   8 10:05 time_zone.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 time_zone.ibd
-rw-r-----. 1 mysql mysql    8624 8月   8 10:05 time_zone_leap_second.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 time_zone_leap_second.ibd
-rw-r-----. 1 mysql mysql    8606 8月   8 10:05 time_zone_name.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 time_zone_name.ibd
-rw-r-----. 1 mysql mysql    8686 8月   8 10:05 time_zone_transition.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 time_zone_transition.ibd
-rw-r-----. 1 mysql mysql    8748 8月   8 10:05 time_zone_transition_type.frm
-rw-r-----. 1 mysql mysql   98304 8月   8 10:05 time_zone_transition_type.ibd
-rw-r-----. 1 mysql mysql   10816 8月   8 10:05 user.frm
-rw-r-----. 1 mysql mysql     396 8月   8 11:36 user.MYD
-rw-r-----. 1 mysql mysql    4096 8月   8 11:47 user.MYI
```

表： 
**MyISAM(文件系统)**  
 user.frm  ： 存储的表结构（列，列属性）  
 user.MYD  :  存储的数据记录  
 user.MYI  ： 存储索引  

**InnoDB(XFS文件系统)**  
 time_zone.frm ： 存储的表结构（列，列属性）  
 time_zone.ibd ： 存储的数据记录和索引  
 ibdata1       :   数据字典信息（相当于linxu的inode节点，用来存储文件的元数据信息）  

MyISAM和InnoDB可以理解为linux上的两个不同的文件系统。  
从mysql5.6开始就开始淘汰MyISAM。  
### 3.7 innodb 段 区 页  

一般情况下（非分区表）
一个表就是一个段
一个段由多个区构成
一个区在（16k），64个连续的页，1M大小

#### 磁盘管理的过程：

先加一个磁盘，分区，格式化，挂在，然后使用。分区，格式化在底层做了一件什么事情那？

盘片，磁头，磁道，扇区，这些是磁盘的生产商，生产出来之后，做了低级格式化，就有了这写东西。有了这些我们也不是去使用这个磁盘，因为我们不知道往哪个扇区，哪个磁道，写数据。为了简化我们硬盘的使用方法。所以就有了文件系统。那么文件系统做了一件什么事情？格式化就是创建文件系统，不再以磁道，扇区，这种低效的方式。文件系统把一个磁盘格式为大小相同的块，默认每一个块是4k。将来使用的时候，比如需要8k空间，那么就给它分几个块，最好是连续的。**文件系统如此，那么我们的mysql也有类似的机制**
![timezone-idb.jpg](https://github.com/Christian-health/christian-health.github.io/blob/master/img/timezone-idb.jpg?raw=true)

所以mysql就在存储引擎层，把这个ibd文件也做了一个格式化，格式化成为一个格子一个格子的。一个格子是16kb，mysql中一个page就是16kb。然后mysql觉得将来表数据会很多，一页一页分太麻烦了，所以**一次性分**64个页，也就是1MB。但是这只是针对我们的数据行。如果是索引，那么因为索引很小，所以索引是按照页分配的，一次一个页。所以如果是数据行存储，那么每次分64页，也就是1MB。如果是索引存储，那么每次分1个页，也就是16kb。数据页大小可以调。连续的64个页，我们称之为一个区。

## 4、用户和权限管理

#### 4.1 作用 

​	登录MySQL 
	管理MySQL 

#### 4.2 用户的定义

​	用户名@'白名单'

```
wordpress@'%'     User@% 允许从所有的ip访问. 
wordpress@'localhost'
wordpress@'127.0.0.1'
wordpress@'10.0.0.%'
wordpress@'10.0.0.5%'   这个是50-59
wordpress@'10.0.0.0/255.255.254.0'
wordpress@'10.0.%'
```

#### 4.3 用户的操作 

##### 4.3.1 建用户

```mysql
mysql> create user hot@'10.0.1.%' identified by 'Aa123456';
Query OK, 0 rows affected (0.00 sec)
```

说明：
**8.0以前**，可以自动创建用户并授权，通过如下命令。

```mysql
mysql> grant all on . to hot@'10.0.0.%' identified by 'Aa123456';   一边授权一边创建用户
```

**8.0以后**必须先建立用户，然后才能授权，因为没有用户怎么授权。所以必须严格。

##### 4.3.2 查询用户

```mysql
mysql> select user,host from mysql.user;
```

##### 4.3.3 修改用户密码

```mysql
mysql> alter user hot@'10.0.0.%' identified by '123456';
```

##### 4.3.4 删除用户

```mysql
mysql> drop user hot@'10.0.0.%' ;   生产中删除用户的操作基本都是屏蔽掉的，不要轻易删除用户
```

#### 4.4 权限管理

##### 4.4.1 权限列表

**ALL （ALL其实就是一种角色，把下面的权限都放在一起了）**
SELECT,INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE

**with grant option**  可以给别人授权

见参考文献3

##### 4.4.2 授权命令

```mysql
grant all on *.* to hot@'10.0.0.%' identified by 'Aa123456' with grant option;

grant 权限  on 作用目标  to 用户  identified by 密码 with grant option;

(1)权限就是4.4.1 ALL 下面的所有的权限，可以使用多个，然后使用逗号进行分割。
   grant SELECT,INSERT, UPDATE, DELETE, CREATE on wordpress.* to 
(2)作用目标:
*.* 等价于chown 777 -R ，整个根底下的所有文件全部授权777
wordpress.*  相当于某个目录下的所有都授权，这里就是wordpress目录下的所有文件都授权。某一个库下的所有表都授权
worpress.t1 worpress库下的t1表授权
```

##### 4.4.3 授权需求

1. 创建一个管理员用户root，可以通过10网段，管理数据库.

  ```mysql
  grant all on *.* to root@'10.0.0.%' identified by 'Aa123456' with grant option;
  ```

2. 创建一个应用用户wordpress，可以通过10网段，wordpress库下的所有表进行SELECT,INSERT, UPDATE, DELETE.

  ```
  grant SELECT,INSERT, UPDATE, DELETE on wordpress.* to wordpress@'10.0.0.%' identified by 'Aa123456';
  ```

##### 4.4.4 回收权限

```mysql
mysql> show  grants for wordpress@'10.0.0.%';   显示给一个用户授权了哪些权限
mysql> revoke delete on wordpress.*  from 'wordpress'@'10.0.0.%';  收回这个用户的delete权限，从wordpress这个库下的所有表
mysql> show  grants for wordpress@'10.0.0.%';
```

##### 4.4.5 关于生产中开用户

（1）如何沟通开用户
1. 是否有邮件批复
2. 对哪些库和表做操作
3. 做什么操作
4. 从什么地址来登录

（2）开发人员找你要root用户密码？
5. 走流程拒绝他
6. 如果是金融类的公司
  （1）原则上是不允许任何非DBA人员持有或申请root
  （2）如果有人私下索要root密码，即使举报。

## 参考

- [体系结构与管理](https://www.jianshu.com/p/e872bc12f583)
- [sql_mode简介](https://blog.csdn.net/ssz1219175635/article/details/88429479)
- [mysql数据库中with grant option使用方法](https://jingyan.baidu.com/article/29697b91de0b0aea20de3cdb.html)
- 