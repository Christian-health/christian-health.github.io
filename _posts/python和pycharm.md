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
## (2)升级环境上的pip版本(离线方式)
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
## (3)安装asserts使用from asserts import assert_true
1、https://pypi.org/  进入这个网页然后搜asserts    
2、然后下载最新的版本     
3、执行pip install asserts-0.11.0-py2.py3-none-any.whl发现如下错误      
```
[root@LIN-6A14041F8B1 python]# pip install asserts-0.11.0-py2.py3-none-any.whl 
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.
Processing ./asserts-0.11.0-py2.py3-none-any.whl
ERROR: Package 'asserts' requires a different Python: 2.7.13 not in '>=3.6'
```
4、解决方法是，进入容器，发现我们使用的版本是      
```
/opt/python2.7.6/lib/python2.7/site-packages/asserts-0.5.1-py2.7.egg-info
```
那么我们就去`Release history`中查找这个版本，然后下载下来。      
5、然后解压缩下载下来的asserts-0.5.1.tar.gz进入目录执行 `python setup.py install` 就会发现这个时候pycharm已经能看到这个软件被成功的导入了。     
```
[root@LIN-6A14041F8B1 asserts-0.5.1]# python setup.py install
running install
running bdist_egg
running egg_info
writing asserts.egg-info/PKG-INFO
writing top-level names to asserts.egg-info/top_level.txt
writing dependency_links to asserts.egg-info/dependency_links.txt
reading manifest file 'asserts.egg-info/SOURCES.txt'
reading manifest template 'MANIFEST.in'
writing manifest file 'asserts.egg-info/SOURCES.txt'
installing library code to build/bdist.linux-x86_64/egg
running install_lib
running build_py
creating build
creating build/lib
copying asserts.py -> build/lib
copying test_asserts.py -> build/lib
creating build/bdist.linux-x86_64
creating build/bdist.linux-x86_64/egg
copying build/lib/test_asserts.py -> build/bdist.linux-x86_64/egg
copying build/lib/asserts.py -> build/bdist.linux-x86_64/egg
byte-compiling build/bdist.linux-x86_64/egg/test_asserts.py to test_asserts.pyc
byte-compiling build/bdist.linux-x86_64/egg/asserts.py to asserts.pyc
creating build/bdist.linux-x86_64/egg/EGG-INFO
copying asserts.egg-info/PKG-INFO -> build/bdist.linux-x86_64/egg/EGG-INFO
copying asserts.egg-info/SOURCES.txt -> build/bdist.linux-x86_64/egg/EGG-INFO
copying asserts.egg-info/dependency_links.txt -> build/bdist.linux-x86_64/egg/EGG-INFO
copying asserts.egg-info/top_level.txt -> build/bdist.linux-x86_64/egg/EGG-INFO
zip_safe flag not set; analyzing archive contents...
creating dist
creating 'dist/asserts-0.5.1-py2.7.egg' and adding 'build/bdist.linux-x86_64/egg' to it
removing 'build/bdist.linux-x86_64/egg' (and everything under it)
Processing asserts-0.5.1-py2.7.egg
Copying asserts-0.5.1-py2.7.egg to /usr/lib/python2.7/site-packages
Adding asserts 0.5.1 to easy-install.pth file

Installed /usr/lib/python2.7/site-packages/asserts-0.5.1-py2.7.egg
Processing dependencies for asserts==0.5.1
Finished processing dependencies for asserts==0.5.1
[root@LIN-6A14041F8B1 asserts-0.5.1]# 

```
6、验证成功导入了,说明成功了      
```
[root@LIN-6A14041F8B1 asserts-0.5.1]# python
Python 2.7.13 (default, May 10 2017, 20:04:28) 
[GCC 6.3.1 20161221 (Red Hat 6.3.1-1)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> from asserts import assert_true
>>> 
```
7、同理可以下载我们需要的`Flask-0.10.1`
