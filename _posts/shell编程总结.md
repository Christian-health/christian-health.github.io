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
#!/bin/sh -x是什么意思 -x 是调试用的，加了这个，就会把脚本中的每条命令的执行情况打印出来
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

![**shell-运行shell脚本的方式**](https://github.com/Christian-health/christian-health.github.io/blob/master/img/shell-%E8%BF%90%E8%A1%8Cshell%E8%84%9A%E6%9C%AC%E7%9A%84%E6%96%B9%E5%BC%8F.jpg?raw=true)

## 二、Shell变量核心基础知识

1、什么是变量

`x=1  y=x+1`

linux下判断是否相等，一个是使用`-eq`，另外一个是使用`==`

按照变量的生命周期进行划分：

永久性环境变量 `/etc/profile`

临时性环境变量`export直接声明即可`

使用`set`可以查看**全局环境变量**，也可以称为shell环境变量

使用`env`可以用来查看**用户的环境变量**，也可以称为本地环境变量，局部环境变量。

2、自定义环境变量

包含字母，下划线，数字。建议以字母或者下划线开头，**千万注意等号两边不能有空格**

3、三种变量定义方式

1. 字符串如何定义  
    ```shell
    yangxuefeng="test the function"
    ```

如果不知道该加单引号还是双引号，那么就加上双引号
    单引号里面写得是什么就会输出什么
    
2. 数字如何定义
     ```shell
     yangxuefeng=18
     ```

3. 命令如何定义
  ```shell
  yangxuefeng=`ls` 
  yangxuefeng=$(ls)
  ```

4、Shell脚本的重要位置参数

**$0   #脚本的名称，如果是全路径执行，那么获取的将是全路径的名称**

![**shell-xargs**](https://github.com/Christian-health/christian-health.github.io/blob/master/img/shell-xargs.jpg?raw=true)

```shell
>> sh test.sh
那么会显示test.sh
>>/server/scripts/test.sh
那么会显示/server/srcipts/test.sh
```

 ```shell
## basename 是去除目录后剩下的名字 
example：shell>temp=/home/temp/1.test 
     shell>base=`basename $temp` 
     shell>echo $base 
结果为：1.test 

## dirname 是取目录 
example：shell>temp=/home/temp/1.test 
     shell>dir=`dirname $temp` 
     shell>echo $dir 
结果为：/home/temp 
 ```

**$n 对应的第n个参数**

**$# 传递参数的总个数**（注意传递参数个数超过9那么就要使用花括号包裹

![**shell-多个参数显示**](https://github.com/Christian-health/christian-health.github.io/blob/master/img/shell-%E5%A4%9A%E4%B8%AA%E5%8F%82%E6%95%B0%E6%98%BE%E7%A4%BA.jpg?raw=true)

![**shell-多个参数显示正确**](https://github.com/Christian-health/christian-health.github.io/blob/master/img/shell-%E5%A4%9A%E4%B8%AA%E5%8F%82%E6%95%B0%E6%98%BE%E7%A4%BA%E6%AD%A3%E7%A1%AE.jpg?raw=true)

**$?  最后运行的命令的结束代码（返回值）即执行上一个指令的返回值 (显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误)** 

**$* 获取传递的所有参数，如果不加上双引号和$@相同，如果加上双引号获取的是一个整体**

**$@获取传参的所有参数，如果不加双引号和$*相同，如果加上双引号，则获取的为单个参数**

**$$获取脚本的pid，一个系统运行了很多的脚本，你杀死它的时候你不知道该杀死谁，那么这个时候使用这个**

```
比如在写shell脚本的时候，在代码中写入 $$ > count.pid 那么以后就可以去count.pid文件中获取shell脚本的进程id
```

**$!获取上一个在后台运行的脚本的pid，**

**$_ 获取上一个脚本最后一个参数**

5、shell变量传递参数

```shell
read  -t  "超时时间"  -p  "提示信息"   变量  变量
read -t 5 -p "please input  num: " a b
echo $a
```
**双引号是弱引用，里面的变量会被替换掉，而单引号是强引用，里面原来是啥就会显示啥，里面的变量不会被替换。**

6、变量的子串删除和替换

```
>> yangxuefeng="yangxuefeng123"
>> echo ${#yangxuefeng}  会显示出变量yangxuefeng的总长度
14
>> echo ${yangxuefeng:2}
ngxuefeng
>> echo ${yangxuefeng:2:2}  类似于python中的切片
ng
>> echo $yangxuefeng | wc -L 获取字符串长度
>> echo $yangxuefeng | awk '{print length}' 
>> expr length "$yangxuefeng" 也可以取长度
>>  youngboy="I am young boy"
>>  echo $youngboy | xargs -n1 | awk '{if (length<3) print}' #打印出长度小于3的
>>  echo $youngboy | awk '{for(i=1;i<NF;i++)if(length($i)<3) print $i}'
```

02-变量子串内容  30:00看到这里


# shell大杂烩知识点   
(1) `>/dev/null 2>&1`        
见参考文献3,写的非常的好     

(2)`>/dev/null`     

(3)`set -e 和 set +e的区别` 
```
set -e ： 执行的时候如果出现了返回值为非零，整个脚本 就会立即退出 
set +e： 执行的时候如果出现了返回值为非零将会继续执行下面的脚本 
```
见参考文献4,写的非常的好    

(4)`在shell里面${a%%.*}、${a##*.}`   
```
${varible##*string} 从左向右截取最后一个string后的字符串
${varible#*string}从左向右截取第一个string后的字符串
${varible%%string*}从右向左截取最后一个string后的字符串
${varible%string*}从右向左截取第一个string后的字符串
例子：
$ MYVAR=foodforthought.jpg
$ echo ${MYVAR##*fo}
rthought.jpg
$ echo ${MYVAR#*fo}
odforthought.jpg
```
(5)`Linux umask命令`
Linux umask命令指定在建立文件时预设的权限掩码。
umask可用来设定[权限掩码]。[权限掩码]是由3个八进制的数字所组成，将现有的存取权限减掉权限掩码后，即可产生建立文件时预设的权限。
```
umask 022
$ mkdir test1                       #创建目录  
$ ls –d –l test1/                   #显示目录的详细信息  
drwxr-xr-x 2 rootlocal rootlocal 4096 2011-9-19 21:46 test1/ 
注意：在上面的输出信息中，"drwxr-xr-x"="777-022=755"。
```
(6)`:>`

(7)`sudo -E -u`
简单来说，就是加上-E选项后，用户可以在sudo执行时保留当前用户已存在的环境变量，不会被sudo重置，另外，如果用户对于指定的环境变量没有权限，则会报错。
参考文献8

(8)`Linux nohup 命令`
nohup 英文全称 no hang up（不挂起），用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行。
```
nohup Command [ Arg … ] [　& ]
参数说明：
Command：要执行的命令。

Arg：一些参数，可以指定输出文件。

&：让命令在后台执行，终端退出后命令仍旧执行。
```
见参考文献7,写的非常的好    

(9)`grep命令返回值`
grep 可用于 shell 脚本，因为 grep 通过返回一个状态值来说明搜索的状态
模板搜索成功，则返回 0
搜索不成功，则返回 1
搜索的文件不存在，则返回 2
见参考文献9,写的非常的好    



## 参考

- [参考文献1、xargs 命令教程](http://www.ruanyifeng.com/blog/2019/08/xargs-tutorial.html)
- [参考文献2、tee命令](https://www.myfreax.com/linux-tee-command/)
- [参考文献3、>/dev/null 2>&1](https://www.cnblogs.com/ultranms/p/9353157.html)
- [参考文献4、shell 中的 set -e 和 set +e的区别](https://www.cnblogs.com/vanoraxnc/p/10728424.html)
- [参考文献5、在shell里面${a%%.*}、${a##*.}](https://blog.csdn.net/long375577908/article/details/78498235)
- [参考文献6、Linux umask命令](https://www.runoob.com/linux/linux-comm-umask.html)
- [参考文献7、Linux nohup 命令](https://www.runoob.com/linux/linux-comm-nohup.html)
- [参考文献8、sudo -E的意思](https://www.cnblogs.com/pinganzi/p/5254995.html)
- [参考文献9、Linux 操作命令 grep](https://blog.51cto.com/11495268/2341830)





















