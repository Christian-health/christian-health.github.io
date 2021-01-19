## 1、docker的安装
```
（1）卸载docker旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```
然后参考如下链接：
https://www.cnblogs.com/yanwuliu/p/11699762.html


(1)docker内vi实现不全
```
docker exec -it --env COLUMNS=`tput cols` --env LINES=`tput lines` your_container_name /bin/bash
```
