## (1)Pycharm打开已有项目配置python环境（在项目上就是这样使用的）
```
1.1 打开项目
File -> Open... -> Open a file or project
选择项目根路径即可

1.2 配置Python编译环境
菜单栏依次点击如下： File -> setting -> 左侧 project : project-name -> Project Interpreter -> 点击解释器右侧齿轮 
即设置 -> Add local... -> Virtual Environment -> 可以选择 Bash Interpreter 和localtion就可以创建一个独立于本项目的虚拟环境啦！

1.3 配置requirements.txt
比如scipy==1.23.0配置好之后，如果存在没有安装的包，pycharm会自动提示， 点击Install Packages即可

1.4 pycharm常用操作
移除没有引用的包
Ctrl+alt+o

1.5 找到在最外层的，__init__.py
然后在这个__init__.py所以在目录上执行，make Director as --> as Source Root
```
## (2)升级环境上的pip版本
想使用pip安装python包，但是环境上的pip版本比较低，所以需要升级版本
```
You are using pip version 8.1.2, however version 20.2.3 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```
但是在线升级总是出现问题，所以采用离线的升级方式
```
1、pip下载安装
  1.1 pip下载
  进入https://pypi.python.org/pypi/pip，下载 .tar.gz压缩包
  
  1.2  Linux安装pip
  # tar -xzvf pip-1.5.4.tar.gz      解压
  # cd pip-1.5.4                    进入解压文件
  # python setup.py install         安装
  
  1.3 升级pip
  python -m pip install --upgrade pip
```
