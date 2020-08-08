## MySQL体系结构与管理

登录mysql有两种方法：

```
TCP/IP方式（远程、本地）：
mysql -uroot -poldboy123 -h 10.0.0.51 -P3306
Socket方式(仅本地)：
mysql -uroot -poldboy123 -S /tmp/mysql.sock
```

mysql启动的时候就可以设置使用的内存大小是多少。mysql启动的时候，告诉操作系统，我mysql很重要，所以我要使用的内存是多少。你操作系统分配给我，如果其他的进程需要使用内存，那么不能枪我mysql的。

## 参考
- [体系结构与管理](https://www.jianshu.com/p/e872bc12f583)
- 