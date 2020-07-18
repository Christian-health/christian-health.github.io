---
layout:     post
title:      ""
subtitle:   ""
data:       ""
author:     ""
header-img: img/post-bg-go-swagger.jpg
catalog: true
tags:
    - go-swagger
---

## go-swagger文档自动化

工作中使用go-swagger完成文档自动化，一直是简单的使用，没有仔细的研究，最近有时间仔细研究一下，在此记录一下。

## 下载go-swagger

[go-swagger 官方下载](https://github.com/go-swagger/go-swagger/releases)
根据不同的操作系统选择对应的版本
![swagger的发布版本截图](https://github.com/Christian-health/christian-health.github.io/blob/master/img/post-bg-swagger-download.jpg)
比如下载swagger_windows_amd64.exe，将swagger_windows_amd64.exe重命名成swagger.exe，放在项目所在的main目录同级的目录中。

## 使用go-swagger
进入自己项目的swagger.exe所在的目录，swagger会自己寻找main包。spec是单词specification（说明书）的缩写。

```
执行命令
swagger generate spec -o ./swagger.json    //根据swagger规范 创建swagger.json规范文档 
swagger serve -F=swagger swagger.json      //启动一个http 服务同时将json文档放入http://petstore.swagger.io 执行 
```

## 使用swagger:meta定义接口的全局信息

属性

| 标记 | 含义 |
| --- | -- |
| Terms Of Service | 描述使用接口服务的一些协议，比如免责等 |
| Consumes | 描述接口默认请求的mime类型值，如果有多个，每个类型占据一行。支持的MIME类型有json，yaml，xml，txt，bin，urlform，multipartform |
| Produces | 描述接口默认响应的mime类型值，如果有多个，每个类型占据一行 |
| Schemes | 描述接口默认支持的协议类型，可能的值有http，https，ws，wss |
| Version | 当前接口的版本 |
| Host | 接口服务所在的域名 |
| Base path	| 接口默认的根路径 |
| Contact | 通常来说是接口文档编写者的联系方式，格式：John Danjohn.dan@example.com |
| License | 接口文档遵循的许可证名称 |
| Security | 关键字字典：[] string {scopes} |
| SecurityDefinitions | 支持的授权类型列表https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject |
| Extensions | Swagger Schema扩展列表。 字段名称必须以x-开头，例如x-internal-id。 该值可以为null，基元，数组或对象。 |

比如如下的代码注释，将会生成对应的json
```
// Package classification testProject API.
//
// the purpose of this application is to provide an application
// that is using plain go code to define an API
//
// This should demonstrate all the possible comment annotations
// that are available to turn go code into a fully compliant swagger 2.0 spec
//
// Terms Of Service:
//
// there are no TOS at this moment, use at your own risk we take no responsibility
//
//     Schemes: http, https
//     Host: localhost
//     BasePath: /v1
//     Version: 0.0.1
//     Contact: Haojie.zhao<haojie.zhao@changhong.com>
//
//     Consumes:
//     - application/json
//     - application/xml
//
//     Produces:
//     - application/json
//     - application/xml
//
// swagger:meta
package routers

import (
  "testProject/controllers"
  "github.com/astaxie/beego"
)

func init() {
    beego.Router("/", &controllers.MainController{})
}

```
从下面对应的json文件，可以看到上面的每一个在代码注释中的标记，都被转变成了最终的json中的一个key。

而对应的标记的冒号后面的值变成了json的对应Key的value。如果注释中的标记后面有多个值，那么对应的value就是一个列表，其中包含多个value，也就是key和value是一对多。

```
{
  "consumes": [
    "application/json",
    "application/xml"
  ],
  "produces": [
    "application/json",
    "application/xml"
  ],
  "schemes": [
    "http",
    "https"
  ],
  "swagger": "2.0",
  "info": {
    "description": "the purpose of this application is to provide an application\nthat is using plain go code to define an API\n\nThis should demonstrate all the possible comment annotations\nthat are available to turn go code into a fully compliant swagger 2.0 spec",
    "title": "testProject API.",
    "termsOfService": "there are no TOS at this moment, use at your own risk we take no responsibility",
    "contact": {
      "name": "John Dan",
      "email": "john.dan@example.com"
    },
    "version": "0.0.1"
  },
  "host": "localhost",
  "basePath": "/v1",
  "paths": {}
}

```
如果查看swagger对应的web页面，可以看到如下的内容:
![swagger-meta在web上对应的页面](https://github.com/Christian-health/christian-health.github.io/blob/master/img/post-swagger-meta.JPG)
### 使用swagger:route定义路由信息

swagger:route标记用来定义接口的路由信息，它会将路径连接到方法，此操作获取唯一id，该id在各个位置用作方法名称。语法如下：
swagger:route [method] [path pattern] [?tag1 tag2 tag3] [operation id]。

https://blog.csdn.net/benben_2015/article/details/100538074

https://www.cnblogs.com/xiaobaiskill/p/10696621.html

### 属性

|    标记  |     含义    |
|------------|-------------|
|Consumes|描述接口支持的特定的mime类型值，如果有多个，每个类型占据一行。支持的MIME类型有json，yaml，xml，txt，bin，urlform，multipartform|
|Produces|描述接口支持的特定的mime类型值，如果有多个，每个类型占据一行|
|Schemes|描述接口支持的特定协议类型，可能的值有http，https，ws，wss|
|Responses|响应状态码字典|
|Deprecated|如果此值为true，则将路由标记为不推荐使用|
|Security|关键字字典：[] string {scopes}|

```
package controllers

import (
  "github.com/astaxie/beego"
)

type MainController struct {
  beego.Controller
}

// Get serves the API for this get index page
func (c *MainController) Get() {
  // swagger:route GET / users indexPage
  //
  // Show index page.
  //
  // This will show all available users by default.
  //
  //     Consumes:
  //     - application/json
  //
  //     Produces:
  //     - application/json
  //
  //     Schemes: http, https, ws, wss
  //
  //     Responses:
  //       default: Resp
  //       422: validationError
  c.Data["Website"] = "beego.me"
  c.Data["Email"] = "astaxie@gmail.com"
  c.TplName = "index.tpl"
}
```
生成效果如下：
! [](https://img-blog.csdnimg.cn/20190904141837901.png)





## 结语

是的，这次的 WWDC 只有软件，没有新的电子设备发布，没有新 iPad Pro、没有 iPhone SE2、没有带八代酷睿的新 MacBook，唯一能和“硬件”沾上边的就是一个新的彩虹表带。

![](https://images.ifanr.cn/wp-content/uploads/2018/06/WWDC-56.jpg)

时至今日，苹果生态已经日趋完善了，大概苹果的产品经理们也想不出什么石破天惊的功能让大家 wow 一声了，有的只是细节层面的改进。作为看客和用户，也只能接受这样的现实了。

对了，那个可以四个人一起玩的乐高积木和 AR 应用，倒是可以考虑买来玩一下，不要一边说没有新东西，一边又对新东西视而不见。

对于 iOS 开发者来说，macOS 将可以使用 iOS 的 UIKit 框架进行开发是一个值得关注的点。

### 参考
- [利用swagger打造高可用项目文档——GO篇](https://studygolang.com/articles/22648?fr=sidebar)

- [使用go-swagger为Go工程生成自动化接口文档](https://blog.csdn.net/benben_2015/article/details/100538074)

- [go-swagger官方文档](https://goswagger.io/)

  


