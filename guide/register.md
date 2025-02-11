# 服务注册&发现

## 注册中心

注册中心目前只支持 etcd v3。

安装方法见官方文档：<https://etcd.io/docs/v3.5/install/>

docker 安装 etcd：<https://etcd.io/docs/v3.5/op-guide/container/>

## 服务注册

目前仅支持使用 etcd v3 作为注册中心。要把服务注册到 etcd 非常简单，只需要再单体服务的基础上增加 register 即可。

可以通过2中方式指定 etcd 地址
1. 通过环境变量`LUCHEN_ETCD_ADDRESS`设置
2. 调用`env.SetDefaultEtcdAddress(etcdAddrs)`

```go
package main

import (
	"time"

	"github.com/fengjx/go-halo/halo"
	"github.com/fengjx/luchen"

	"github.com/fengjx/luchen/env"
	"github.com/fengjx/luchen/example/registrar/endpoint"
)

func main() {

	// 创建 http server
	hs := luchen.NewHTTPServer(
		luchen.WithServiceName("http.helloworld"),
		luchen.WithServerAddr(":8080"),
	)

	// 创建 grpc server
	gs := luchen.NewGRPCServer(
		luchen.WithServiceName("grpc.helloworld"),
		luchen.WithServerAddr(":8088"),
	)

	// 注册 grpc 和 http 端点
	endpoint.RegisterGreeterGRPCHandler(gs)
	endpoint.RegisterGreeterHTTPHandler(hs)

	registrar := luchen.NewEtcdV3Registrar(
		hs,
		gs,
	)
	// 把服务注册到 etcd 并启动
	registrar.Register()
	// 阻塞服务并监听 kill 信号，收到 kill 信号后退出（最长等待10秒）
	halo.Wait(10 * time.Second)
}
```

## 服务发现

接口定义，Selector 接口负责服务节点查询
```go
// Selector 服务节点查询
type Selector interface {
	Next() (*ServiceInfo, error)
}
```

根据服务名获取 Selector
```go
selector := luchen.GetEtcdV3Selector(serviceName)
// 获取服务节点
serviceInfo, err := selector.Next()
```

在大多数情况下，都不需要直接使用 `Selector`， 除非你有定制化需求。

`GRPCClient` 内部会通过 Selector 获取服务节点，并向目标节点发起rpc请求。具体查看[客户端章节](/guide/grpc-client)


## 示例源码

完整示例代码：[registrar](https://github.com/fengjx/luchen/tree/master/_example/registrar)