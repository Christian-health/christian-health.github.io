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

mysqld是最重要的，如果没有mysqld，那么就不会有后面的创建master thread、干活的Thread、预分配的内存，这些都是mysqld做的。

### 3.1 SQL语句引入

结构化的查询语言
DQL   数据查询语言
DDL   数据定义语言
DML   数据操作语言
DCL   数据控制语言

```
例如：  mysql> select user,host from mysql.user;
```

### 3.2 连接层

（1） 提供连接协议
		Socket  
		TCPIP
（2） 验证用户名（root@localhost）密码合法性，进行匹配专门的授权表。

（3） 派生一个专用连接线程（接收SQL，返回结果）
	  mysql> show processlist;
思考： 
忘记密码的参数在哪做的手脚？
--skip-grant-tables  
--skip-networking	  

### 3.3 SQL层（优化方面至关重要的）

（1）验证SQL语法和SQL_MODE
（2）验证语义
（3）验证权限
（4）解析器进行语句解析，生成执行计划（解析树）
（5）优化器（各种算法，基于执行代价），根据算法，找到代价最低的执行计划。
	代价：CPU  IO  MEM
（6）执行器按照优化器选择执行计划，执行SQL语句，得出获取数据的方法。
（7）提供query cache(默认不开)，一般不开，会用redis
（8）记录操作日志（binlog），默认没开

### 3.4 存储引擎层

真正和磁盘打交道的一个层次
根据SQL层提供的取数据的方法，拿到数据，返回给SQL，结构化成表，再又连接层线程返回给用户。



## 参考
- [体系结构与管理](https://www.jianshu.com/p/e872bc12f583)
- 