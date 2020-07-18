---
layout:     post
title:      "echo框架使用学习总结"
subtitle:   "echo"
data:       ""
author:     ""
header-img: img/echo-back-title.jpg
catalog: true
tags:
    - echo
    - go
---



## 为什么选择echo框架？



## 安装运行

​		（1）创建一个echo的项目的目录

​		（2）执行go mod init echo-demo，将会生成go.mod文件

​		（3）创建src目录和main目录，并创建main.go

​		（4）拷贝echo官网的demo到main.go

​		（5）执行go run main.go，在执行过程中会自动下载所依赖的echo包，同时将会更新go.mod和go.sum两个文件 

![echo运行成功](https://github.com/Christian-health/christian-health.github.io/blob/master/img/echo-simple-run.jpg?raw=true)

## 使用echo

#### 1、自定义

```go
package main

import (
   "github.com/labstack/gommon/log"
   "io"
   "net/http"
   "os"

   "github.com/labstack/echo/v4"
)

func main() {
   // 创建一个Echo的instance
   e := echo.New()
   /*
     用来关闭启动时候的横幅，也就是在启动的控制台上不要打印出如下内容
     D:\gowork\echo\src\main>go run  main.go

      ____    __
     / __/___/ /  ___
    / _// __/ _ \/ _ \
   /___/\__/_//_/\___/ v4.1.16
   High performance, minimalist Go web framework
   https://echo.labstack.com
   ____________________________________O/_______
                                       O\
   ⇨ http server started on [::]:1323
   */
   e.HideBanner=false

   // 用于关闭HTTP/2协议
   e.DisableHTTP2=true

   //用于设置读取请求的最大时间。

   //用于设置日志输出的位置，默认是 os.Stdout
   e.Logger.SetOutput(io.Writer(os.Stdout))

   //下面两个设置完全禁用日志。
   //e.Logger.SetOutput(ioutil.Discard)
   //e.Logger.SetLevel(log.OFF)

   //设置日志级别
   e.Logger.SetLevel(log.DEBUG)
   //e.Logger.SetLevel(log.INFO)
   //e.Logger.SetLevel(log.WARN)
   //e.Logger.SetLevel(log.ERROR)
   //e.Logger.SetLevel(log.OFF)

   // Routes
   e.GET("/", hello)

   // Start server
   e.Logger.Fatal(e.Start(":1323"))
}

type customerLogger struct {
   Logger echo.Logger
}

// Handler
func hello(c echo.Context) error {
   return c.String(http.StatusOK, "Hello World,customize!")
}	
```

## 参考：

- [echo英文官方文档](https://echo.labstack.com/guide )
- [echo中文文档](https://www.bookstack.cn/read/go-echo/README.md)
- [ECHO系列教程](http://blog.studygolang.com/category/echo-%e7%b3%bb%e5%88%97/)
- 