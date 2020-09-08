使用top命令
`top -b -d 1 -n pid >top.txt`
然后使用`cat  top.txt | grep -a "pid"`过滤出指定的pid的进程的cpu使用率
最后通过excel软件绘制出图形

