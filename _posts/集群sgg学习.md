[toc]

# 1、集群的简介

## 1.1、集群的基本概念

集群是一组**协同工作**的服务器，**各有分工**，对外表现为**一个整体**。

## 1.2、集群的分类

![sgg-集群分类01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-%E9%9B%86%E7%BE%A4%E5%88%86%E7%B1%BB01.jpg?raw=true)

![sgg-集群分类02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-%E9%9B%86%E7%BE%A4%E5%88%86%E7%B1%BB02.jpg?raw=true)

![sgg-集群分类03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-%E9%9B%86%E7%BE%A4%E5%88%86%E7%B1%BB03.jpg?raw=true)



# 2、负载均衡集群

## 2.1、负载均衡器分类

### 2.1.1、按照软硬件分类

软件：`LVS`，`Nginx`，`HAPROXY`

硬件: `F5`

### 2.1.2、按照工作层级进行分类

​	![ssg-负载均衡集群01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E9%9B%86%E7%BE%A401.jpg?raw=true)

&emsp; `F5`工作可以在数据链路层，比如我国的网络是分成移动、电信、网通三家主体的，`F5`的在数据链路层的工作原理就是看来源

是电信还是网通，那么就通过电信专线或者网通的专线转发出去。也就是比如你三家主要网络供应商，都买上一根线。然后`F5`根

据不同的请求源使用不同的线路进行转发出去。`F5`也可以做

&emsp;`LVS`工作在传输层

&emsp;`Nginx`工作在应用层

&emsp;`DNS`可以做到一个域名解析成多个`IP`，不同的`IP`可以解析到一个域名。

## 2.2、负载均衡集群原理

### 2.2.1、 四层工作逻辑
​	![sgg-负载均衡集群原理01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E9%9B%86%E7%BE%A4%E5%8E%9F%E7%90%8601.jpg?raw=true)
&emsp;`LVS`只是改动了一下`IP`地址，根本就没有读取报文的内容，所以这样的设计就能减轻很大的压力，提高性能。我只是进行了一

些地址信息的更改，没有数据转发，没有涉及真实的流量转发和流量生产。这就是`LVS`的性能好的原因。

&emsp;注意`LVS`这张图就好比。一个物流批发站，货物从西安到鹤岗经过这个物流批发站，但是**不会进入**物流批发站，只是在物流批发

站的外面重新的贴了标签，确保货物能准确的送到，所以这整个过程是一个`TCP`连接。

### 2.2.2 、七层工作逻辑

​	![sgg-负载均衡集群原理02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E9%9B%86%E7%BE%A4%E5%8E%9F%E7%90%8602.jpg?raw=true)

&emsp;注意`Nginx`这张图就好比。一个物流批发站，货物从西安到鹤岗经过这个物流批发站，**会进入**物流批发站，所以这就会对我们

的物流站有很大的压力，所以左边客户到`Nginx`是一个`TCP`连接。而右边`Nginx`到真实服务器是另外的一个`TCP`连接。

​	![sgg-负载均衡集群原理03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E9%9B%86%E7%BE%A4%E5%8E%9F%E7%90%8603.jpg?raw=true)

&emsp;所以，`Nginx`的负载均衡能力要比`LVS`差很多。那么为什么我们还要有`Nginx`而不是一直的使用`LVS`。就是因为使用场景的不

同。对于`LVS`我们可以理解，它可以代理`TCP`的，`UDP`，`HTTP`的。所以我们的`Nginx`可以支持`C/S`，`B/S`。但是我们的`Nginx`只

能支持`HTTP`协议（标准版本的`nginx`），那么`Nginx`只能适用于`B/S`。

&emsp;比如有一栋楼，三层住的三哥，二层住的是二哥，一楼有一个门卫大爷。`LVS`的工作原理就是，`A`来找三哥，那么门卫大爷也就

是`LVS`就告诉`A`，三哥在三楼，所以`A`就要自己爬到三楼，然后挨个屋里面寻找三哥。而`Nginx`的原理就是，`Nginx`这个门卫大爷

会自己上到三楼找到三哥，然后带下来给你。直接使用主机名称或者域名，那么使用`Nginx`。有的时候使用主机名或者域名效率

会更高。所以`LVS`只能识别到`TCP`，但是`Nginx`能够识别到协议，主机名，端口和`IP`向下都能看的到。

## 2.3、`LVS`工作模式

​	![sgg-LVS工作模式01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F01.jpg?raw=true)

&emsp;我们无法直接修改内核配置文件，所以需要一个命令行管理工具进行触发。这个工具就是`ipvsadm`。

![sgg-LVS工作模式02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F02.jpg?raw=true)

### 2.3.1、`LVS-NAT`工作模式

​	`NAT`读作那它

&emsp;`DNAT`技术就是目标地址转换，目的地址进行更改。

&emsp;`SNAT`技术就是源地址转换，源地址进行更改。

![ssg-lvs-nat工作模式01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-nat%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F01.jpg?raw=true)

![ssg-lvs-nat工作模式02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-nat%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F02.jpg?raw=true)

&emsp;这样做会出现问题，因为数据包返回的源地址和我发送请求时候的原地址不同，所以要进行源地址转换。所以有下图：

![ssg-lvs-nat工作模式03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-nat%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F03.jpg?raw=true)

![ssg-lvs-nat工作模式04](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-nat%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F04.jpg?raw=true)

### 2.3.2、`LVS-DR`工作模式

&emsp;负载调度器和真实服务器要处于同一个广播域中。

![ssg-lvs-dr工作模式01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-dr%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F01.jpg?raw=true)

![ssg-lvs-dr工作模式02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-dr%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F02.jpg?raw=true)

![ssg-lvs-dr工作模式03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-dr%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F03.jpg?raw=true)

&emsp;从上图可以看到如果这样的进行转发，又发生了`NAT`模型中发生的问题。客户端收到的包数据中的源`IP`发生了变更。所以有了如下的方案：

![ssg-lvs-dr工作模式04](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-dr%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F04.jpg?raw=true)

&emsp;这样通过上面的方式就解决了这个，客户端收到的包数据中的源`IP`发生了变更的问题。

![ssg-lvs-dr工作模式05](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-dr%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F05.jpg?raw=true)

![ssg-lvs-dr工作模式06](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-dr%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F06.jpg?raw=true)

&emsp;在`DR`模式下面有一种问题，修改`DMAC`之后，把这个数据包从新发送到广播域中，为了让`RealServer`能接受这个数据包，我们给`RealServer`也配置了`Director`的`IP`，那么如何防止`IP`地址出现问题，给`IP`地址添加了一个`lo:0 10.10.10.100`，为了防

止一个广播域中存在多个同样的`IP`地址，产生冲突，那么就需要对`ARP`进行相应的设置。

&emsp;`arp-ignore`叫做响应级别，也就是我一个数据包到达一个网卡，那么我这个网卡到底是响应，还是不响应，要不要给你回信

息，这个就是`arp-ignore`的设置。比如一台电脑有两个网卡，其中一个是`eth0`对应的`IP`地址是`10.10.10.11`，另外的一个是

`eth1`对应的`IP`地址是`20.20.20.11`。那么当有一个数据包，想找`20.20.20.11`但是它是从`10.10.10.11`进入的。那么如果

`arp-ignore`设置为`0`那么我这个`eth0`网卡也会响应它。如果是设置`arp-ignore`是`1`的话，那么`eth0`就不会进行响应。尽管我

台计算机，有两个网卡`eth0`和`eth1`那么如果设置`arp-ignore`是`1`那么我就不响应，以为它不是我，我也不是它，两个网卡是不

通的。

&emsp;那么`arp-announce`是什么意思那？就是还是我有一台电脑但是这台电脑有两个网卡`eth0`和`eth1`，而且这两个网卡分别连接两

个不同的广播域。那么当这个值设置为`0`的时候，`eth0`在向自己的广播域广播自己的`IP`和`MAC`的时候，同时也会把我这台机器上

的`eth1`的`IP`和`MAC`信息广播到`eth0`所在的广播域。而这个值设置为`2`的时候，那么就不会把`eth1`的`IP`和`MAC`信息广播到`eth0`

所在的广播域中。如果这个值设置为`1`的时候就是尽量避免，但是不保证，介于`0`和`1`之间。

&emsp;所以综上所述，如果我这么设置了,设置`arp-ignore`为`1`,同时设置`arp-announce`为`2`，那么我的`lo`是不是就闭嘴了，既不会

对外公告`IP`和`MAC`信息，也能接受发送给我的信息了。但是这样还有一个问题，就是但是这样还是有一个问题，就是数据报文只

能从`eth0`这种接口进入这台机器，是不能通过`lo:0`这样的接口到达这台机器的，那么怎么办？所以我们需要配置一个路由规则。

加入这条规则是`route add -host 10.10.10.100 dev lo:0 `就是告诉我 ，如果来了一个数据包，是发送给`RealServer`的

`10.10.10.100`这个规则的时候，那么你给`eth0`网卡给我接一下，然后给我`lo:0`。这就好比代取快递。然后数据报文到达了

`lo:0`之后，那么`lo:0`在给请求的客户端发送响应数据报文，这个时候使用的就是`lo:0`的`10.10.10.100`这个`IP`地址。

我们还做了一些优化，就是在我们的负载调度器上，关闭了广播地址的公告功能。还有优化方案就是`lo:0`设置子网掩码是`32`位的

`255`。

 

![sgg-实验03]()

![sgg-实验03]()

![sgg-实验03]()

### 2.3.3、`LVS-TUN`工作模式

![ssg-lvs-tun工作模式01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-tun%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F01.jpg?raw=true)

![ssg-lvs-tun工作模式02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-tun%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F02.jpg?raw=true)

![ssg-lvs-tun工作模式03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-tun%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F03.jpg?raw=true)

![ssg-lvs-tun工作模式04](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-lvs-tun%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F04.jpg?raw=true)

# 3、负载均衡集群实验

![sgg-实验01]()

![sgg-实验02]()

![sgg-实验03]()

## 3.1、`LVS-DR`实验

&emsp;`NetworkManager`：不安全的网络策略会被`NetworkManager`强制关闭。所以我们在做实验之前，一定要先关闭

`NetworkManager`。所以比如我们要配置`lo:0`这种开子接口的方案可以会被`NetworkManager`关闭。

&emsp;`service NetworkManager stop && chkconfig NetworkManager off`   

##  3.2、`LVS-NAT`实验



# 4、负载均衡集群调度算法

## 4.1、调度算法基本分类（通用的调度算法）

固定算法（静态调度算法）：只根据算法本身去调度，**不考虑服务器本身**

动态算法（动态调度算法）：除了考虑算法本身，还要**考虑服务器状态**

```
http 链接，分成两类：
     （1）活动链接
     	  传输数据	
     （2）非活动链接
     	  刚建立握手还没有传输数据
          传输数据完毕还没有来得及断开
在lvs调度算法中，会理解为活动链接的资源消耗是非活动链接的256倍
```

![sgg-LVS-调度算法01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS-%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%9501.jpg?raw=true)

```
WLC
	第一台服务器   w=1     	第一台服务器   w=2
	0						0
最开始到达的请求数都是0，那么当一个请求来的时候，根据WLC算法会把这个请求发送给第一台服务器，但是根据上面，我们可以看到其实是第二台服务器更好，因为他的权重是2。所以引入了SED算法
```

![sgg-LVS-调度算法02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS-%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%9502.jpg?raw=true)

![sgg-LVS-调度算法03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS-%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%9503.jpg?raw=true)

&emsp;前面我们使用`DH`算法，如果请求`1.jpeg`这个文件特别的多，那么就会对第一个`SQUID`造成非常大的压力，所以就有了`LBLV`算

法，也就是如果`SQUID1`扛不住了，那么我就在选择下面的一个`SQUID`，把请求发送给它，也让它开始进行缓存。所以这样就减少

了`SQUID1`的压力。那么可以以此增加`SQUID`的个数。

&emsp;最后还有`LBLCR`算法，这个算法就是让`SQUID`之间能进行缓存的同步。而不必全去后面的`APACHE`请求数据。

## 4.2、`LVS持久连接`（`LVS`特有的调度算法）

```
HTTPS   安全   配置    花钱  
消耗性能甚至有SSL握手设备这种物理设备专门做这件事情。
```

&emsp;为什么要有`LVS`持久连接？

![sgg-LVS-调度算法04](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS-%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%9504.jpg?raw=true)

&emsp;比如，如上图，当客户端发送一个请求到`LVS`，那么`LVS`把这个请求发送给了第一个`APACHE`，这个当然要建立一个`HTTPS`连

接。然后请求的资源返回给了客户端，下一次同样的一个客户端又发送请求，请求一个`APACHE`上的资源。那么又要重新建立一个

`HTTPS`连接。所以创建`HTTPS`连接过程消耗的资源实在是太多了。我们可以使用`SH`调度算法，但是这样就无法实现`RR`了。那么怎

么解决这个问题？使用`LVS`持久连接！持久化连接和`SH`类似，而且这个算法优先于通用算法。我们应该先匹配到持久化连接，然

后再去匹配通用算法。那么我就可以先配置持久化连接，然后再开启`RR`调度算法。那么这样就是既开启了持久化连接，又`RR`也能

轮询了。持久化连接会被普遍应用到我们的`HTTPS`集群中。原理就是在内存分页中采用一块缓存，记录我们的客户端数据与我们

分配的真实服务器的相关数据，保证我们能够在一定时间中持久化。持久化连接的时间会一点一点减少，当持久化连接时间到达`0`

时候，就采用通用算法，比如`RR`进行处理。当我们的持久化时间减少的时候，如果客户端又有请求来了。那么我就把这个持久化

时间，加上`30S`，直到这个时间减为`0`。记住两点：（1）优先级高于通用算法（2）类似于`SH`算法（3）应用于`HTTPS`场景。

![sgg-LVS-调度算法05](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS-%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%9505.jpg?raw=true)

![sgg-LVS-调度算法06](https://github.com/Christian-health/christian-health.github.io/blob/master/img/sgg-LVS-%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%9506.jpg?raw=true)

```
集群不先进行处理，而是先打上一个标记，然后根据标记进行处理。这样会更加的灵活，这个要和iptables一起使用。
```



# 5、高可用集群

## 5.1、高可用集群的说明

```
高可用：尽可能的提高服务的可用性
	99  基础
	999 一般要做到，原因是可用性越高，资金成本越高。
	9999 需要硬件设备支持
	99999 理论上的概念
	
	实现原理：心跳检测
	
服务：
	有状态(在N断时间之后，把它添加上，在功能上没有区别，因为数据库中的数据可能已经差很多了)
		MYSQL 	
	无状态(在N断时间之后，把它添加上，在功能上没有任何的区别)
		Apache
		LVS
所以无状态服务好实现高可用，而有状态的不好实现高可用，但是也可以实现。


```

![sgg-LVS-调度算法06]()

## 问题1、上面的图如果后面的`apache`服务器挂了会发生什么？

```shell
# 脚本的思想：循环判断我们拥有的真实服务器的节点，它的状态是否正常。如果正常，那么静默，等待下次循环。如果不正常，那么添加或者删除。这个脚本在负载均衡器节点执行。

#!/bin/bash
#=============================================================================
VIP=10.10.10.100      #集群虚拟IP
CPORT=80        #定义集群端口
FAIL_BACK=127.0.0.1     #本机回环地址
RS=("10.10.10.12" "10.10.10.13")  #编写集群地址
declare -a RSSTATUS       #变量RSSTATUS定义为数组态
RW=("2" "1")
RPORT=80        #定义集群端口
TYPE=g          #制定LVS工作模式：g=DR m=NAT
CHKLOOP=3
LOG=/var/log/ipvsmonitor.log

#=============================================================================

addrs() {
  ipvsadm -a -t $VIP:$CPORT -r $1:$RPORT -$TYPE -w $2
  [ $? -eq 0 ] && return 0 || return 1
}

delrs() {
  ipvsadm -d -t $VIP:$CPORT -r $1:$RPORT
  [ $? -eq 0 ] && return 0 || return 1
}

checkrs() {
  local I=1
  
  while [ $I -le $CHKLOOP ]
  do
    if curl --connect-timeout 1 http://$1 &> /dev/null
    then
      return 0
    fi
    
    let I++
  done
  
  return 1
}

initstatus() {
  
  local I
  local COUNT=0;

  for I in ${RS[*]}
  do
    if ipvsadm -L -n | grep "$I:$RPORT" && > /dev/null
    then
    
      
      RSSTATUS[$COUNT]=1
    else
      RSSTATUS[$COUNT]=0
    fi
      let COUNT++
  done
}

#=============================================================================
initstatus

while :; do

  let COUNT=0
  for I in ${RS[*]}
  do
    if checkrs $I
    then
      if [ ${RSSTATUS[$COUNT]} -eq 0 ]
      then
                    addrs $I ${RW[$COUNT]}
                  [ $? -eq 0 ] && RSSTATUS[$COUNT]=1 && echo "`date +'%F %H:%M:%S'`, $I is back." >> $LOG
      fi
    else
                  if [ ${RSSTATUS[$COUNT]} -eq 1 ]
      then
                    delrs $I
                    [ $? -eq 0 ] && RSSTATUS[$COUNT]=0 && echo "`date +'%F %H:%M:%S'`, $I is gone." >> $LOG
      fi
    fi
    
    let COUNT++
  done
  sleep 5
done
```

## 问题2、上面解决了真实服务器挂掉的问题，那么如果我们的`LVS`挂了怎么办？

所以我们需要对`LVS`实现高可用，那么我们使用的就是`keepalived`



## 5.2、`Keepalived原理`

![ssg-keeplived01.jpg](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-keeplived01.jpg?raw=true)

`Keepalived`是专门为`LVS`做的健康状态检查的工具。

&emsp;不光是服务器需要高可用，其实很多网络设备也是需要高可用的，比如交换机。所以很早就有解决交换机等网络设备的高可用方

案，就是`VRRP`。而`Keepalived`就是使用已经成熟的`VRRP`协议。

![ssg-keeplived02.jpg](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-keeplived02.jpg?raw=true)

&emsp;前面我们演示过`LVS`是不支持，后端服务器健康监测的，那么所以写了一个`shell`脚本用来进行健康状态监测。但是

`Keepalived`是支持对后端服务器的健康状态监测的。而且`Keepalived`里面集成了`LVS`的模块。所以使用`Keepalived`可以实

现健康，对后端服务器的增删。

### 5.2.1实验

接下来实验以下`Keepalived`：

#### 1、主`LVS`需要做的事情：

```

(1) 关闭网卡守护进程,每一台实验机器都要关闭，无论是真实服务器还是负载均衡器
	service NetworkManager stop && chkconfig NetworkManager off
(2) 添加一个网卡当做vip使用
    cd /etc/sysconfig/network-scripts
    cp -a ifconfig-eth0  ifconfig-eth0:0
(3) vi ifcfig-eth0:0
    设备名改成eth0:0
    IP地址改成静态的10.10.10.100
(4) 启动ifcfg-eth0:0
	ifup ifcfg-eth0:0
(5) yum install ipvsadm
(6) 关闭网卡的广播功能 vim /etc/sysctl.conf    (这里是做什么用的？)
	net.ipv4.conf.all.send_redirects = 0
	net.ipv4.conf.default.send_redirects = 0
	net.ipv4.conf.eth0.send-redirects = 0
(7) sysctl -p
(8) 添加集群
	ipvsadm -A -t 10.10.10.100:80 -s rr
(9) 添加集群中的服务器
    ipvsadm -a -t 10.10.10.100:80 -r  10.10.10.13:80 -g
(10) 保存已经添加的集群
	 service ipvsadm  save
(11) ipvsadm -nL  查看是否添加成功
```

#### 2、服务器上要做的事情(每台都类似只有`IP`不同)

```

(1) 关闭网卡守护进程,每一台实验机器都要关闭，无论是真实服务器还是负载均衡器
	service NetworkManager stop && chkconfig NetworkManager off
(2) chkconfig httpd on
(3) echo "this is server 1" >> /var/www/html/index.html
(4) curl localhost 检查是否网站运行正常
(5) cd /etc/sysconfig/network-scripts
	cp -a ifconfig-lo  ifconfig-lo:0
(6) vi ifconfig-lo:0
    改设备名称
    IP地址改成静态的10.10.10.100
    ifup lo:0
(7) 关闭arp通信行为
	net.ipv4.conf.all.arp_ignore=1
	net.ipv4.conf.all.arp_announce=2
	
	net.ipv4.conf.default.arp_ignore=1
	net.ipv4.conf.default.arp_announce=2
	
	net.ipv4.conf.lo.arp_ignore=1
	net.ipv4.conf.lo.arp_announce=2
	
	因为修改了内核参数所以要使用sysctl -p使其生效
	
(8) 
	route add -host 10.10.10.100 ev lo:0
	route -n 查看添加是否生效
	echo "route add -host 10.10.10.100 ev lo:0" >> /etc/rc.local  防止重启之后失效
	
到此完成1和2两大步，那么测试一下LVS-DR模式是否工作正常。如果工作正常，那么我们接下来开始继续实验Keepalived，我们回
到主LVS上，安装Keepalived。
```

#### 3、安装`Keepalived`  (主`LVS`节点)

```shell
3、安装Keepalived  (主LVS节点)
搞一个Keepalived安装包，实验中使用的是keepalived-1.2.2.tar.gz 因为使用源代码安装，所以要安装gcc 和 gcc-c++
(1) 安装gcc和gcc-c++
(2) 解压keepalived安装包
(3) 安装相关的keepalived的安装依赖
(4) yum -y install kernel-devel openssl-devel popt-devel gcc*
(5) 使用./configure生成makefile文件了
./configure --prefix=/ --with-kernel-dir=/usr/src/kernels/2.6.32...../     (这里要注意自己的机器的目录可能不是2.6.32)
	如果出不来/kernels/2.6.32...../ 这个目录，那么重新安装kernel-devel.x86_64 0:2.6.32-642.el6这个文件

(6) make && make install
	编译 && 编译安装
(7) 接着我们拷贝启动脚本
	ls -l /etc/init.d/keepalived
	chkconfig --add keepalived 添加keepalived的启动管理
	chkconfig keepalived on    设置为开机自动启动
(8) 修改Keepalived配置文件
	vim /etc/keepalived/keepalived.conf
(9) service keepalived start
(10) cat /var/log/messages
	可以看到Keepalived的监控信息日志已经打印到了这个文件中

```

#### 4、主节点的`Keepalived`配置

```
[root@localhost ~]# vim /etc/keepalived/keepalived.conf
#全局定义块：对整个 Keepalive 配置生效的，不管是否使用 LVS；
global_defs {					#全局配置
	#notification_email {		#指定keepalived在发生切换时需要发送email到的对象，一行一个
	#	acassen@firewall.loc	#指定收件人邮箱
    #	failover@firewall.loc
	#	sysadmin@firewall.loc
	#}
	#notification_email_from Alexandre.Cassen@firewall.loc #指定发件人
	#smtp_server 192.168.200.1	#指定smtp服务器地址
	#smtp_connect_timeout 30		#指定smtp连接超时时间
    #上面这些是邮件配置，一般来说我们不会使用这么low的监控服务，我们后期会构建监控服务，对keepalived的可用性进行检测
	router_id R1			#此处注意router_id为负载均衡标识，在局域网内应该是唯一的。也就是我当前机器的名称，同一个组里面机器的名称不能一样，准备节点不一样
}
 
vrrp_instance inside_network {
	state MASTER 			#指定那个为master，那个为backup，如果设置了nopreempt这个值不起作用，主备依靠priority决定
	interface eth0 			#设置实例绑定的网卡，指定通过哪个网卡进行心跳检测
	virtual_router_id 66	#VPID标记，虚拟的组，只有在同一个组里面才是同一个高可用环境，所以主备节点他们的virtual_router_id值应该是一样的。
	priority 80				#优先级，权重，高优先级竞选为master，主和重之间的差距最好是50，那么切换更加的顺畅一点
	advert_int 1			#检查间隔，默认1秒
	authentication {		#设置认证
		auth_type PASS		#认证方式，类型主要有PASS、AH 两种
		auth_pass 111	    #认证密码
	}
	virtual_ipaddress {		#设置vip，也就是整个集群的IP地址
		10.10.10.100
	}
}

virtual_server 10.10.10.100 80 { #集群所使用的VIP和端口
    delay_loop 2					#健康检查间隔，单位为秒
    lb_algo rr						#lvs调度算法rr|wrr|lc|wlc|lblc|sh|dh
    lb_kind DR						#负载均衡转发规则。一般包括DR,NAT,TUN 3种
    protocol TCP					#转发协议，有TCP和UDP两种，一般用TCP，没用过UDP
 
    real_server 10.10.10.13 80 { #真实服务器，包括IP和端口号
        weight 1					#默认为1,0为失效        
		TCP_CHECK {					#通过tcpcheck判断RealServer的健康状态
            connect_port 80         健康检查的端口的端口
            connect_timeout 3		#连接超时时间
            nb_get_retry 3			#重连次数
            delay_before_retry 4	#重连间隔时间
        }
    }

    real_server 10.10.10.14 80 { #真实服务器，包括IP和端口号
        weight 1                    #默认为1,0为失效        
        TCP_CHECK {                 #通过tcpcheck判断RealServer的健康状态
            connect_port 80         健康检查的端口的端口
            connect_timeout 3       #连接超时时间
            nb_get_retry 3          #重连次数
            delay_before_retry 4    #重连间隔时间
        }
    }
}
```

#### 5、安装`Keepalived`  (备份`LVS`节点)

![ssg-keeplived03.jpg](https://github.com/Christian-health/christian-health.github.io/blob/master/img/ssg-keeplived03.jpg?raw=true)

```
(1) cd /etc/sysconfig/network-scripts
    cp -a ifconfig-eth0  ifconfig-eth0:0
	vi ifcfig-eth0:0
	设备名改成eth0:0
	IP地址改成静态的10.10.10.100
(2) 启动ifcfg-eth0:0
	ifup ifcfg-eth0:0
	这是会报错，说
	Determining if ip address 10.10.10.100 is already in use for device eth0...
	Error, some other host (00:0c:29:93:39:84) already uses address 10.10.10.100.
(3) 为了解决上面的问题，那么我们打开ifup启动程序这个文件
	一般在256行。不同的内核版本所在的行号不同，注释掉ifup中的响应代码，让这个网卡能正常的启动
(4) 安装Keepalived
搞一个Keepalived安装包，实验中使用的是keepalived-1.2.2.tar.gz 因为使用源代码安装，所以要安装gcc 和 gcc-c++
(5) 安装gcc和gcc-c++
(6) 解压keepalived安装包
(7) 安装相关的keepalived的安装依赖
(8) yum -y install kernel-devel openssl-devel popt-devel gcc*
(9) 使用./configure生成makefile文件了
./configure --prefix=/ --with-kernel-dir=/usr/src/kernels/2.6.32...../     (这里要注意自己的机器的目录可能不是2.6.32)
	如果出不来/kernels/2.6.32...../ 这个目录，那么重新安装kernel-devel.x86_64 0:2.6.32-642.el6这个文件
(10) make && make install
	编译 && 编译安装
(11) 接着我们拷贝启动脚本
	ls -l /etc/init.d/keepalived
	chkconfig --add keepalived 添加keepalived的启动管理
	chkconfig keepalived on    设置为开机自动启动
(12) 修改Keepalived配置文件
 	vim /etc/keepalived/keepalived.conf
(13) service ipvsadm start
(14) chkconfig ipvsadm on
(15) service keepalived start
(16) 关闭网卡的广播功能 vim /etc/sysctl.conf    (这里是做什么用的？)
	net.ipv4.conf.all.send_redirects = 0
	net.ipv4.conf.default.send_redirects = 0
	net.ipv4.conf.eth0.send-redirects = 0
	sysctl -p
(17) ipvsadm -Ln
	 我们使用这个命令刷新的时候，就能看到集群信息，但是其实我们还没有在备LVS节点配置这些，那么为什么会有这些信息那
因为我们在配置Keepalived的时候，已经在配置文件中添加了这信息，而Keepalived里面有集成的LVS的模块。所以就可以把这些信息添加到环境中。
```

#### 6、备`LVS`的`Keepalived`配置

```
[root@localhost ~]# vim /etc/keepalived/keepalived.conf
#全局定义块：对整个 Keepalive 配置生效的，不管是否使用 LVS；
global_defs {					#全局配置
	#notification_email {		#指定keepalived在发生切换时需要发送email到的对象，一行一个
	#	acassen@firewall.loc	#指定收件人邮箱
    #	failover@firewall.loc
	#	sysadmin@firewall.loc
	#}
	#notification_email_from Alexandre.Cassen@firewall.loc #指定发件人
	#smtp_server 192.168.200.1	#指定smtp服务器地址
	#smtp_connect_timeout 30		#指定smtp连接超时时间
    #上面这些是邮件配置，一般来说我们不会使用这么low的监控服务，我们后期会构建监控服务，对keepalived的可用性进行检测
	router_id R2			#此处注意router_id为负载均衡标识，在局域网内应该是唯一的。也就是我当前机器的名称，同一个组里面机器的名称不能一样，准备节点不一样
}
 
vrrp_instance inside_network {
	state SLAVE 			#指定那个为master，那个为backup，如果设置了nopreempt这个值不起作用，主备依靠priority决定
	interface eth0 			#设置实例绑定的网卡，指定通过哪个网卡进行心跳检测
	virtual_router_id 66	#VPID标记，虚拟的组，只有在同一个组里面才是同一个高可用环境，所以主备节点他们的virtual_router_id值应该是一样的。
	priority 20				#优先级，权重，高优先级竞选为master，主和重之间的差距最好是50，那么切换更加的顺畅一点
	advert_int 1			#检查间隔，默认1秒
	authentication {		#设置认证
		auth_type PASS		#认证方式，类型主要有PASS、AH 两种
		auth_pass 111	    #认证密码
	}
	virtual_ipaddress {		#设置vip，也就是整个集群的IP地址
		10.10.10.100
	}
}

virtual_server 10.10.10.100 80 { #集群所使用的VIP和端口
    delay_loop 2					#健康检查间隔，单位为秒
    lb_algo rr						#lvs调度算法rr|wrr|lc|wlc|lblc|sh|dh
    lb_kind DR						#负载均衡转发规则。一般包括DR,NAT,TUN 3种
    protocol TCP					#转发协议，有TCP和UDP两种，一般用TCP，没用过UDP
 
    real_server 10.10.10.13 80 { #真实服务器，包括IP和端口号
        weight 1					#默认为1,0为失效        
		TCP_CHECK {					#通过tcpcheck判断RealServer的健康状态
            connect_port 80         健康检查的端口的端口
            connect_timeout 3		#连接超时时间
            nb_get_retry 3			#重连次数
            delay_before_retry 4	#重连间隔时间
        }
    }

    real_server 10.10.10.14 80 { #真实服务器，包括IP和端口号
        weight 1                    #默认为1,0为失效        
        TCP_CHECK {                 #通过tcpcheck判断RealServer的健康状态
            connect_port 80         健康检查的端口的端口
            connect_timeout 3       #连接超时时间
            nb_get_retry 3          #重连次数
            delay_before_retry 4    #重连间隔时间
        }
    }
}

```

#### 7、测试`Keepalived`能实现`LVS`主备高可用

&emsp;要想模拟`LVS`挂掉了，因为`LVS`就是内核。所以要把整个内核搞挂了不容易。所以采用的模拟方法就是断网，把网卡停了。然后

去观察网站还能否使用了，如果能使用了就说明我们的`Keepalived`使用还是好使的。

#### 8、测试`Keepalived`能检测后端服务器工作正常

​	停掉服务器，然后使用`ipvsadm -nL`查看服务器是否从列表中清除掉。

​	重新开启服务器，然后使用`ipvsadm -nL`查看服务器是否从列表中添加。

## 5.3 Heartbeat-Nginx

![sgg-heartbeat01]()

`Heartbeat`使用一组脚本的方式进行检测和切换。所以原理不像`Keepalived`那么复杂。

### 5.3.1实验

#### 1、安装`nginx`

```

(0) 搞一个nginx的源代码安装包解压
(1) yum -y install pcre pcre-devel zlib zlib-devel     (nginx的依赖)
(2) useradd -s /sbin/nologin -M nginx
(3)	cd nginx-1.2.6
	./configure --prefix=/usr/local/nginx --user=nginx --group=nginx
(4) make && make install
(5) cd /usr/local/nginx
	rm -fr *.html
(6) vi index.html
(7) 启动nginx
```

#### 2、安装`heartbeat.tar.gz`

```
(1) tar -zxvf heartbeat.tar.gz
(2) cd heartbeat
(3) yum -y install *
```

#### 3、时间同步

对于所有的主备，这种集群的这种同步的，时间同步都非常的重要，必须要有。

```
在10.10.10.11上进行配置：
(1) yum -y install ntp
(2) vim /etc/ntp.conf
(3) 
    restrict 10.10.10.0  mask 255.255.255.0 nomodify notrap
    server 127.127.1.0
    fudge 127.127.1.0 stratum 10
(4) service ntpd start
(5) chkconfig ntpd on
```

```
在10.10.10.12上进行配置：
(1) yum -y install ntpdate
(2) ntpdate -u 10.10.10.11
```

#### 5、配置主机域名解析

&emsp;一定要配置主机域名解析，因为它是使用`uname -n`来进行进行检测的，这个检测的值要和你的配置文件中配置的值是一样的，

并且能够被解析才行。

```
在10.10.10.11上进行配置：
(1) hostname www.centos1.com
(2) vi /etc/sysconfig/network  看是否生效
(3) 构建DNS
	vi /etc/hosts
	10.10.10.11 www.centos1.com
	10.10.10.12 www.centos2.com
```

```
在10.10.10.12上进行配置：
(1) hostname www.centos2.com
(2) vi /etc/sysconfig/network  看是否生效
(3) 构建DNS
	vi /etc/hosts
	10.10.10.11 www.centos1.com
	10.10.10.12 www.centos2.com
```

#### 6、拷贝配置文件

上面我安装完成了`heartbeat`和初始环境。然后我要去拷贝配置文件了。

```
在10.10.10.11上进行拷贝配置文件：
cd /usr/share/doc/heartbeat-3.0.4/
cp ha.cf authkeys haresources /etc/ha.d
其中：
ha.cf 主配置文件
authkeys 认证文件    
haresources 源文件
```

#### 7、修改认证文件

认证服务，节点之间的认证配置，修改`/etc/ha.d/authkeys`，在主上修改

```
dd if=/dev/random bs=512 count=1 | openssl md5  #生成秘钥随机数
vim authkeys
	auth 3
	1 crc
	2 sha1 HT!
	3 md5 54d306dd3d5e0f87ce4266d1dd64
chmod 600 authkeys
```

#### 8、修改主配置文件

```
bcast eth0
node www.centos1.com
node www.centos2.com
```

![sgg-heartbeat02]()

#### 9、修改源配置文件

```
www.centos1.com IPadder::10.10.10.100/24/eth0:0 （配置文件中添加）
等同于
ifconfig eth0:0 10.10.10.100 netmask 255.255.255.0
```

#### 10、重节点进行拷贝上面的三个配置文件

#### 11、开始试验

```
(1) 一直ping  10.10.10.100这个浮动，当切换的时候，我们会发现ping不通
(2) 模拟一个节点挂了，就是断掉网卡
(3) /etc/init.d/heartbeat status  查看heartbeat的运行状态、
(4) /etc/init.d/heartbeat restart 重新启动heartbeat
```

&emsp;`Heartbeat`也能完成切换的功能，但是从原理角度考虑，还是`Keepalived`和`VRRP`协议更加好一些，因为协议非常成熟。

`Heartbeat`的`V2`版本资源控制器的东西，用来进行资源的监控和对应的服务的启用。但是在`V3`的时候被剥离出去了。所以如果是

网断了，`Heartbeat`关闭了，服务器宕机了，都可以进行我们的服务的切换。但是比如如果`nginx`被`pkill nginx`了，会发现服

务没有进行自动的切换。**`Heartbeat`检测是的网络的通信能力，而不是我服务是否存活**。这个非常重要。比如我的`LVS`挂了，那

么挂的就是内核，但是我的`nginx`是在用户空间的。所以我们为了让`nginx`退出的情况下，也能够马上的进行切换。

```
#!/bin/bash
PWD=/usr/local/script/jiankong
URL="http://10.10.10.11/index.html"
HTTP_CODE=`curl -o /dev/null -s -w "%{http_code}" "${URL}"` 
if [ $HTTP_CODE != 200 ]
    then
	service heartbeat stop
fi
```

我们把这个脚本添加`crond`中。

```
>> crontab -e
*/1 * * * *  bash /usr/local/script/80.sh

>> service crontab restart 
>> chkconfig crond on
```

那么这个时候就会一分钟定时执行这个任务，然后发现服务不可用了，那么就把`heartbeat`关闭了。这样就完成了切换。

这样无论是服务私网，网络中断，系统崩溃都能完成切换。



## 5.4 多级负载

### 5.4.1 实现原理



# 6、缓存和代理服务



# 7、网络存储

## 7.1、存储分类

![ssg-网络存储01]()

#### 7.1.1、网络拓扑

`NAS` 

`DAS`

`SAN`

#### 7.1.2、存储技术

#### 7.1.3、网络拓扑+存储技术





emsp;

# 参考

- [LVS健康检查脚本](https://www.cnblogs.com/lyshark/p/10222252.html)
- 





























