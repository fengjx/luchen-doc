# http 服务

## 创建一个 http server

```go
httpServer := luchen.NewHTTPServer(
    luchen.WithServiceName("helloworld"),
    luchen.WithServerAddr(":8080"),
)
```

可选参数
```go
// NewHTTPServer 创建 http server
// opts 查看 ServerOptions
func NewHTTPServer(opts ...ServerOption) *HTTPServer

// ServerOptions server 选项
type ServerOptions struct {
    serviceName string
    addr        string
    metadata    map[string]any
}

// WithServiceName server 名称，在微服务中作为一组服务名称标识，单体服务则无需关注
func WithServiceName(serviceName string) ServerOption
// WithServerAddr server 监听地址
func WithServerAddr(addr string) ServerOption
// WithServerMetadata server 注册信息 metadata，单体服务无需关注
func WithServerMetadata(md map[string]any) ServerOption
```

## 路由和端点绑定

使用 Handle 方法注册路由和端点：

```go
// 端点定义
def := &luchen.EndpointDefine{
    Endpoint: sayHello,                         // 端点
    Path:     "/say-hello",                     // 请求路径
    ReqType:  reflect.TypeOf(&sayHelloReq{}),   // 请求参数类型
    RspType:  reflect.TypeOf(&sayHelloRsp{}),   // 响应参数类型
}
// 注册端点
httpServer.Handle(def)
```

端点定义

```go
// 端点定义，端点即对应一个接口，不同协议转换成相同的参数，交给端点进行处理
func sayHello(ctx context.Context, request any) (response any, err error) {
	req := request.(*sayHelloReq)
	response = &sayHelloRsp{
		Msg: "hello " + req.Name,
	}
	return
}
```

## 参数编解码

通过编解码处理将不同协议转换为统一的结构体，交给 endpoint 处理。

HTTPServer 内置了请求解码器，会根据请求的 Content-Type 自动选择合适的解码器：

- application/json: JSON 解码器
- application/protobuf: Protobuf 解码器

编解码实现可以参考 [server_http.go](https://github.com/fengjx/luchen/blob/master/server_http.go)
```go
// getHTTPRequestDecoder 解码 http pb 请求
func getHTTPRequestDecoder(typ reflect.Type) httptransport.DecodeRequestFunc

// encodeHTTPResponse 编码 http 响应
func encodeHTTPResponse(ctx context.Context, w http.ResponseWriter, data any)
```

## 静态文件服务器

http server 支持通过本地文件实现一个静态文件服务器，使用示例

```go
// 注册静态文件服务访问路径和文件路径
mux := httpServer.Mux()
mux.Static("/assets/", "static")
```

底层通过 [xin](https://github.com/fengjx/xin) 来实现

## 示例源码

- [helloworld](https://github.com/fengjx/luchen/tree/master/_example/helloworld) helloworld 示例
- [pbdemo](https://github.com/fengjx/luchen/tree/master/_example/pbdemo) 通过 proto 协议定义接口

