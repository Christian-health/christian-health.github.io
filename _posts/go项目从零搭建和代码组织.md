
代码组织：   
（1）基本上有一个struct那么就会有一个new struct,比如如下:
```
type Log struct {
    a   *A
    b   *B
}

那么将来基本上都会有一个  func NewLog(a   *A,b   *B) *Log{
    return &{
        a:a,
        b:b
    }
}
```


(2)、有一个struct那么就可以有一个对应的interface，interface中是对应的struct对应的函数，那么将来需要传入这个
struct类型的参数，就可设置为参数类型是LogIF，比如如下:
```
type Log struct {
    a   A
    b   B
}

func (s Log) GetA() A {
    return s.a
}

func (s Log) GetB() B {
    return s.b
}

type LogIF interface {
    GetA() A
    GetB() B 
}
//注意这个函数的参数是LogIF，那么这里可以使用Log传入，也就是  Process(Log)
func Process(log LogIF) {

}

```
(3)、我们称常驻内存的为服务，如何才能上电之后常驻内存？使用fx.lifecycle,在NewLog()   
()、层级调用
```
router层   --> control层
control层  -->  app层 -->  

app层 -->  app层 
      -->  provider层 
domain层 =====> ***_service.go

因为是router层到control层，然后control层到app层。那么如果我不走路由而是直接调用的化，那么需要直接调用app层，从app层作为入口。
```
()、代码的目录
```
ppmc //根目录
--gdto(adto)  global dto
--ci
    --tools
    ----gocoverng
    ----golangci-lint
    ----swagger
--common
    ----zlog 全局日志
    ----error-handle
    ----custom-error
    ----common-router全局路由，比如用来存放日志的等级设置
--constant  存放全局变量
--doc存放plantuml
     --存放的各个代码段的plantuml图
--domain
--ppmc-north
            --domain
            --inner-dto
            --north-app
            --north-app-dto
            --north-constant
            --north-router
            --provider
--ppmc-proc
--ppmc-south
            --domain
                    --provider-itfc  #注意这里domain一般是包含调用provider，但是我们会把provider层对应的接口，放在domain层的provider-itfc中，而不是防止在provider目录层
                    也就是domain层中函数provider层中的方法对应的接口
            --provider
--main
--provider
--router
--test
--utils
--vendor
--fx
--.gitignore
--go.mod
--go.sum
```
