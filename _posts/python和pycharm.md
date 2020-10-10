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
