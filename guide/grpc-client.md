# gRPC Client

## 通过ip:port访问

未使用服务注册的服务，可以通过固定ip:port访问，直接使用 grpc client 原生调用方式即可。

```go
clientConn, err := grpc.Dial(
    "localhost:8088",
    grpc.WithTransportCredentials(insecure.NewCredentials()),
)
if err != nil {
    panic(err)
}
greeterClient := pb.NewGreeterClient(clientConn)
ctx, cancel := context.WithTimeout(context.Background(), time.Second*3)
defer cancel()
helloResp, err := greeterClient.SayHello(ctx, &pb.HelloReq{
    Name: "fengjx",
})
log.Println(helloResp.Message)
```

```bash
$ go run main.go

2024/04/27 15:53:52 Hi: fengjx
```

## 通过服务发现请求

```go
greeterClient := pbgreet.NewGreeterService("grpc.helloworld")
helloResp, err := greeterClient.SayHello(context.Background(), &pbgreet.HelloReq{
    Name: "fengjx",
})
if err != nil {
    log.Fatal(err)
}
log.Println(helloResp.Message)
```

```bash
$ cd _example/quickstart/cli/greetergrpcmicroclient
$ go run main.go

2024/04/27 15:53:00 Hi: fengjx
```

源码：[registrar/rpccli](https://github.com/fengjx/luchen/blob/master/_example/registrar/rpccli/main.go)

