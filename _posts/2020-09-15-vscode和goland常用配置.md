# visual code配置
#### golang 跳转速度慢

打开 VS Code 的setting, 搜索 go.useLanguageServe, 并勾选上.
默认情况下, 会提示叫你reload，重新打开之后，右下角会自动弹出下载的框框，点击 install即可。
如果下载时间过长，不成功，可以看方案二

#### golang 列字符提示线
在vscode中设置```"editor.rulers": [80,120]```就可以了 想画几条线都可以

#### 展开关闭代码
折叠：`Ctrl/Command + k + 0`

展开：`Ctrl/Command + k + j`

# goLang配置
#### (1)GoLand 取消单行 if 代码的折叠操作
```
Settings -> Editor -> General -> Code Folding -> Go
看到 Go 下边的三个选项了吗
One line returns
One Line panics
Format strings
取消他们的选中，点击 Applay, 还你一个不再夹带 GoLand 私货的代码编辑区域
```
#### (2)golang 安装 gofmt与goimports
https://xiaozhou.net/goland-gofmt-and-goimports-2018-08-24.html

#### (3)goland 激活方式
http://idea.medeming.com/code/

#### (4)goland 安装golint
https://www.cnblogs.com/kotagan/p/11364499.html

(5) windows下面安装golang

http://www.xue51.com/soft/37852.html


## 参考
- [解决vscode编写go代码时提示过慢(gopls)](https://blog.csdn.net/zhetmdoubeizhanyong/article/details/102534172)
