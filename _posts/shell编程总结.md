## 一、简单介绍shell

（1）为什么要使用shell

1. 提高工作效率
2. 自动化管理
3. 减少重复的工作
4. 安装操作系统
5. 系统优化SSH，关闭SElinux，关闭iptables，加大描述符，内核参数优化等。
6. 安装服务
7. 服务的配置
8. 启动服务
9. 日志统计，日志切割（用的最多的是三剑客）
10. 服务监控 zabbix（全能，主要负责监控服务，端口，自定义的取值）、cacti（监控流量）、nagions（运营商用这个，因为这个擅长监控交换机和路由器）

补：1、关闭iptables，千万要注意，有的公司没有硬件防火墙，所以用的全是iptables，所有的路由和端口转发，用的都是iptables，如果把iptables干掉，那么所有的业务都停止了，就完蛋了



（2）学习shell变成要掌握的基础知识

1. 三剑客（sed，grep，awk）
2. 常用的linux命令
3. 熟练掌握Vim



（3）如何学习好shell编程

1、环境变量基础知识

2、if判断

3、for循环

4、while循环

5、case语句

6、函数

7、数组

8、流程控制语句 continue   break  exit

基础语法必须掌握。自己总结，不断的练习

不要拿来主义，一定要有教材，自己总结的比较完善的笔记

（4）Shell脚本初步入门

1、什么是shell

shell是谁写得是GNU项目留下的一个产物。GNU项目失败了。但是留下了很多的组件。

bash包含sh的所有功能，但是sh不一定包含了bash的所有功能

交互模式：终端等待我们输入命令，执行我们所输入的命令行并且把结果反馈给我们，关闭终端，中断

非交互模式：它回去执行我们所写好的命令（放入到文本里），执行到最后一条，Shell退出

init的pid是1，init启动之后，首先调用/usr/login

2、什么是shell脚本

把可执行的命令放到一个文本里，这就是shell脚本

3、创建shell脚本（规范）

1、脚本必须放在同样的一个目录里面/server/scripts

2、脚本的开头必须写解释器

3、脚本名称的结尾必须以.sh结尾

4、脚本中写注释，创建者的名字，创建时间

```shell
#Author yangxuefeng
#create time 2020-09-26
#脚本的用途
#联系方式
```

5、成对的符号一次性书写完成 

```shell

“” ，''，{}，[]，``

```

6、循环语句一次性书写完成  `for i in ; do ;done`

7、注释尽量不要使用中文

8、内容缩进

给shell脚本加上制定的权限，才可以使用tab补全。`/server/`按`tab`能出现`/server/scripts`

```mysql
cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core) 
#下面是centos6方式
/etc/init.d/nginx 启动方式
/etc/init.d/sshd  restart/start/reload 启动方式
#下面是centos7方式
systemctl restart nginx
#比较起来就是6是串行（如果一个错了就卡在那里了），7是并行的（出错了不管，继续启动其他的）。
# echo $USER
#系统变量都是大写的
```

## 二、Shell变量核心基础知识

1、什么是变量

`x=1  y=x+1`

linux下判断是否相等，一个是使用`-eq`，另外一个是使用`==`

按照变量的生命周期进行划分：

永久性环境变量 `/etc/profile`

临时性环境变量`export直接声明即可`

使用`set`可以查看**全局环境变量**，也可以称为shell环境变量

使用`env`可以用来查看**用户的环境变量**，也可以称为本地环境变量，局部环境变量。



























