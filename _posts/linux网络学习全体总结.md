[TOC]



`Redhat Linux`的网络配置，基本上是通过修改几个配置文件来实现的，虽然也可以用`ifconfig`来设置`IP`，用`route`来配置默认网关，

用`hostname`来配置主机名，**但是重启后会丢失(也就是命令配置是临时生效的)**。

`Linux`中网络相关的主要的几个配置文件为：

```shell
/ect/hosts配置主机名（域名）和`IP`地址的对应  <======================> hostname命令，修改主机名称
/etc/sysconfig/network配置主机名和网关      <======================> 
/etc/sysconfig/network-scripts/ifcfg-eth0 <======================> ifconfig命令
eth0配置文件名称为ifcfg-eth0，eth1则文件名为ifcfg-eth1，以此类推
/etc/resolv.conf配置`DNS`客户端（关于使用哪个`DNS`服务器的配置）<======================>
```



## (1)网卡的配置信息`/etc/sysconfig/network-scripts/ifcfg-<interface-name>`文件说明
在`RedHat`中，系统网络设备的配置文件保存在`/etc/sysconfig/network-scripts`目录下，`ifcfg-eth0`包含第一块网卡的配置信息，

`ifcfg-eth1`包含第二块网卡的配置信息。在启动时，系统通过读取这个配置文件决定某个网卡是否启动和如何配置。

下面是`/etc/sysconfig/network-scripts/ifcfg-eth0`文件的示例：

```
DEVICE=eth0
IPADDR=192.168.0.2
NETMASK=255.255.255.0
BROADCAST=192.168.0.255
NBOOT=yes
BOOTPROTO=none
GATEWAY＝192.168.0.1
```

若希望手工修改网络地址或增加新的网络连接，可以通过修改对应的文件`ifcfg-<interface-name>`或创建新的文件来实现。

```
DEVICE=<name>　　                 　<name>表示物理设备的名字
IPADDR=<address>　　              　<address>表示赋给该网卡的IP地址
NETMASK=<mask>　                    <mask>表示子网掩码
BROADCAST=<address>　               <address>表示广播地址
NBOOT=yes/no                     　　启动时是否激活该卡
BOOTPROTO=none                      none：无须启动协议；bootp：使用bootp协议；dhcp：使用dhcp协议
GATEWAY=<address>                   <address>表示默认网关
MACADDR=<MAC-address>               <MAC-address>表示指定一个MAC地址
USERCTL=yes/no　　               　　是否允许非root用户控制该设备


NM_CONTROLLED="yes"                 network mamager的参数 ,是否可以由NNetwork Manager托管
HWADDR=							    MAC地址
TYPE=Ethernet                       类型
PREFIX=24                           子网掩码24位
DEFROUTE=yes                        就是default route，是否把这个eth设置为默认路由
ONBOOT=yes                          设置为yes，开机自动启用网络连接
IPADDR=      					    IP地址
BOOTPROTO=none                      设置为none禁止DHCP，设置为static启用静态IP地址，设置为dhcp开启DHCP服务
DNS1=8.8.8.8                        第一个dns服务器
DNS2=8.8.4.4 #                      第二个dns服务器
UUID=							    唯一标识
BRIDGE=                             设置桥接网卡
IPV6INIT=no                         禁止IPV6
USERCTL=no                          是否允许非root用户控制该设备，设置为no，只能用root用户更改
NAME="System eth1"                  这个就是个网络连接的名字
MASTER=bond1                        指定主的名称 
SLAVE                               指定了该接口是一个接合界面的组件。
NETWORK                             网络地址
ARPCHECK=yes                        检测
PEERDNS                             是否允许DHCP获得的DNS覆盖本地的DNS
PEERROUTES                          是否从DHCP服务器获取用于定义接口的默认网关的信息的路由表条目
IPV6INIT						    是否启用IPv6的接口。
IPV4_FAILURE_FATAL=yes   		    如果ipv4配置失败禁用设备
IPV6_FAILURE_FATAL=yes              如果ipv6配置失败禁用设备
```



## (2)配置`DNS`客户端`/etc/resolv.conf`文件
文件`/etc/resolv.conf`配置`DNS`客户端，它包含了`DNS`服务器地址和域名搜索配置，每一行应包含一个关键字和一个或多个的由空格隔开的参数。下面是一个例子文件：

```
search wuxp.com
nameserver 192.168.0.1
nameserver 192.168.0.2
```

`search wuxp.com`:表示当提供了一个不包括完全域名的主机名时，在该主机名后添加`wuxp.com`的后缀

`nameserver`:表示解析域名时使用该地址指定的主机为域名服务器。其中域名服务器是按照文件中出现的顺序来查询的。因此，应该首

先给出最可靠的服务器。目前，至多支持三个名字服务器。



## (3)服务器上的网络配置信息`/etc/sysconfig/network`文件
该文件用来指定服务器上的网络配置信息，下面是一个示例：

```
NETWORK=yes
FORWARD_IPV4=yes
HOSTNAME=server.wuxp.com
GAREWAY=192.168.0.1
GATEWAYDEV=eth0

NETWORK=yes/no　　　					　网络是否被配置
FORWARD_IPV4=yes/no　　　				　是否开启IP转发功能
HOSTNAME=<hostname>					   <hostname>表示服务器的主机名
GAREWAY=<address>　　　　		   		<address>表示网络网关的IP地址
GAREWAYDEV=<device>　　				 <device>表示网关的设备名，如：eth0
```



## (4) `/etc/hosts`文件
当机器启动时，**在可以查询`DNS`以前**，机器需要查询一些主机名到`IP`地址的匹配。这些匹配信息存放在`/etc/hosts`文件中。**在没有域名服务器情况下，系统上的所有网络程序都通过查询该文件来解析对应于某个主机名的`IP`地址**。
下面是一个`/etc/hosts`文件的示例：

```
127.0.0.1 Localhost server.wuxp.com
192.168.0.3 station1.wuxp.com
```

**最左边一列是主机`IP`信息，中间一列是主机名。任何后面的列都是该主机的别名。**

修改后，需要重启网络才会生效：

```
重新启动网络设置：/sbin/service network restart
service network start //启动网络服务
service network stop //停止网络服务
service network status //查看网络服务状态
```

## (5)单个网卡绑定多个`IP`

有时，我们需要在一块网卡上配置多个`IP`，例如，在上面的例子中，我们还需要为`eth0`配置`IP` `192.168.168.2`和`192.168.168.3`。那么需要再在`/etc/sysconfig/network-scripts`下新建两个配置文件：

```
原来只有一个ifcfg-eth0,现在要在一个网卡上绑定两个IP，所以要新建两个文件。

ifcfg-eth0:0内容如下：

DEVICE=eth0:0
BOOTPROTO=static
IPADDR=192.168.168.2
NETMASK=255.255.255.0
NBOOT=yes



ifcfg-eth0:1内容如下：

DEVICE=eth0:1
BOOTPROTO=static
IPADDR=192.168.168.3
NETMASK=255.255.255.0
NBOOT=yes

```

## (6)多个网卡绑定成一块虚拟网卡

为了提供网络的高可用性，我们可能需要将多块网卡绑定成一块虚拟网卡对外提供服务，这样即使其中的一块物理网卡出现故障，也不会导致连接中断。比如我们可以将`eth0`和`eth1`绑定成虚拟网卡`bond0`
首先在`/etc/sysconfig/network-scripts/`下创建虚拟网卡`bond0`的配置文件`ifcfg-bocnd0`，内容如下：

```
DEVICE=bond0
BOOTPROTO=none
BROADCAST=192.168.168.255
IPADDR=192.168.168.1
NETMASK=255.255.255.0
NETWORK=192.168.168.0
NBOOT=yes
TYPE=Ethernet
GATEWAY=192.168.168.250
USERCTL=no
```

然后分别修改`eth0`和`eth1`的配置文件

`ifcfg-eth0`内容：

```
DEVICE=eth0
BOOTPROTO=none
NBOOT=yes
USERCTL=no
MASTER=bond0
SLAVE=yes
```

`ifcfg-eth1`内容：

```
DEVICE=eth1
BOOTPROTO=none
NBOOT=yes
USERCTL=no
MASTER=bond0
SLAVE=yes
```

因为`linux`的虚拟网卡是在内核模块中实现的，所以需要安装的时候已经装好该`module`。在`/etc/modprobe.conf`文件（好像有些老版本是`/etc/modules.con`）中添加如下内容（如果没有该文件，则新建一个）：

```
alias bond0 bonding
options bond0 miimon=100 mode=1 primary=eth0
```

`miimon=100`表示每`100ms`检查一次链路连接状态，如果不通则会切换物理网卡

`mode=1`表示主备模式，也就是只有一块网卡是`active`的，只提供失效保护。如果`mode=0`则是负载均衡模式的，所有的网卡都是

`active`，还有其他一些模式很少用到

`primary=eth0`表示主备模式下`eth0`为默认的`active`网卡

最后，在`/etc/rc.local`中加入

`modprobe bonding miimon=100 mode=1`

重启机器后可以看到虚拟网卡已经生效，可以通过插拔两个物理网卡的网线来进行测试，不过`linux`中网卡接管的时间好象比较长。



## (8) vm主机三种网络模式

### (8.1) Briged（桥接模式）

![vm-网络模式-桥接模式01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F01.jpg?raw=true)  
![vm-网络模式-桥接模式02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F02.jpg?raw=true)   
![vm-网络模式-桥接模式03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F03.jpg?raw=true)   
![vm-网络模式-桥接模式04](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F04.jpg?raw=true)   
![vm-网络模式-桥接模式05](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F05.jpg?raw=true)   
![vm-网络模式-桥接模式06](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F06.jpg?raw=true)   
![vm-网络模式-桥接模式07](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F07.jpg?raw=true)  
![vm-网络模式-桥接模式08](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vm-%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F08.jpg?raw=true)  


### (8.2) NAT（NAT模式）
按照这个配置成功
https://blog.51cto.com/13648313/2164309
https://blog.51cto.com/yangxiaowei/1955553



https://www.aneasystone.com/archives/2015/04/three-network-modes-of-vmware-in-action.html

这篇文章的所有参考文献都需要看。很重要。

### (8.3) Host-Only（仅主机模式）

#### 8.3.1 Host-Only模式访问互联网

```
问题描述
在VMware Workstation中新建了一个虚拟机CentOS7，网络适配器选择的是“仅主机模式”，结果，物理机ping不通虚拟机，虚拟机也ping不通物理机。

原因分析
物理机ping不通虚拟机
（1）虚拟机防火墙的原因，关闭防火墙即可

虚拟机ping不通物理机
（1）ping的地址不对，由于是“仅主机模式”，所以物理机和虚拟机通讯用的网络适配器是VMnet1，因此，ping的时候应该写VMnet1的地址，ping它才是ping宿主机  

****这个是重点，注意是ping VMware Network Adapter VMnet1这个虚拟网卡，而不是ping真正的物理机的IP

（2）物理机防火墙的原因，关闭防火墙即可
```



![vmware-hostonly-00](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-00.jpg?raw=true)
![vmware-hostonly-01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-01.JPG?raw=true)   
![vmware-hostonly-02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-02.JPG?raw=true)   
![vmware-hostonly-03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-03.JPG?raw=true)    
![vmware-hostonly-04](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-04.JPG?raw=true)    
![vmware-hostonly-05](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-05.JPG?raw=true)   
![vmware-hostonly-06](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-06.JPG?raw=true)   
![vmware-hostonly-07](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-07.JPG?raw=true)    
![vmware-hostonly-08](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-08.JPG?raw=true)    
![vmware-hostonly-09](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-09.JPG?raw=true)    
![vmware-hostonly-10](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-10.JPG?raw=true)     



#### 8.3.2 Host-Only模式不访问互联网

![vmware-hostonly-noInternet-01](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-noInternet-01.JPG?raw=true)   
![vmware-hostonly-noInternet-02](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-noInternet-02.JPG?raw=true)     
![vmware-hostonly-noInternet-03](https://github.com/Christian-health/christian-health.github.io/blob/master/img/vmware-hostonly-noInternet-03.JPG?raw=true)     


## (9)如何实现端口映射



## (100)参考文献

- [参考文献1、Linux网卡的配置文件详解及应用](https://blog.csdn.net/Com_ma/article/details/77130820)
- [参考文献2、Linux网络接口配置文件ifcfg-eth0解析(非常全的参数解释)](https://www.cnblogs.com/liujiacai/archive/2004/01/13/8067928.html)
- [参考文献3、/etc/sysconfig/network-scripts/下文件介绍](https://www.cnblogs.com/zhouhbing/p/5191076.html)
- [参考文献4、实战VMware的三种网络模式](https://www.aneasystone.com/archives/2015/04/three-network-modes-of-vmware-in-action.html)
- 
