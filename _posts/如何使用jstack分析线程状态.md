https://www.cnblogs.com/wuchanming/p/7766994.html


(1)非`root`用户`dump`文件生成
`sudo -H -u provider bash -c '/usr/local/zxjdk-8u212-linux-x64/bin/jmap -dump:live,format=b,file=fm_full_mem.hprof pid' `
