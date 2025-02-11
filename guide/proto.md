# 接口协议

使用标准 protobuf 协议来定义接口，并通过通过工具可以自动生成端点代码。有几个优势
- 标准协议，跨语言支持，可以直接其他语言代码
- 代码即文档，不需要额外引入接口文档工具

## Proto 文件示例

```proto
syntax = "proto3";

package pb;

option go_package = "github.com/fengjx/luchen/example/pbdemo/pbgreet";

// gomodpath=github.com/fengjx/luchen/example
// epath=pbdemo/endpoint

// The greeting service definition.
service Greeter {
  // SayHello Sends a greeting
  // http.path=/say-hello
  rpc SayHello(HelloReq) returns (HelloResp) {}
}

// The request message containing the user's name.
message HelloReq { string name = 1; }

// The response message containing the greetings
message HelloResp { string message = 1; }
```

### 注释指令说明

在 proto 文件中，我们通过注释来添加一些额外的参数，用于代码生成：

| 指令      | 说明                              | 示例                                            |
| --------- | --------------------------------- | ----------------------------------------------- |
| gomodpath | go mod 路径，指定代码生成的包路径 | `// gomodpath=github.com/fengjx/luchen/example` |
| epath     | 生成 endpoint 代码的相对路径      | `// epath=pbdemo/endpoint`                      |
| http.path | HTTP 接口路径，定义在 rpc 方法上  | `// http.path=/say-hello`                       |

## 代码生成

使用 `lc pbgen` 命令生成代码：

```bash
lc pbgen -f proto/hello/hello.proto
```

生成如下代码

```
├── endpoint                        // 端点
│   ├── greeter_endpoint.curl       // 接口测试 curl 命令
│   ├── greeter_endpoint.go         // 端点注册辅助函数代码，无需修改
│   └── greeter_sayhello.go         // 端点接口代码，增加自定义实现逻辑
└── pbgreet                         // proto 定义代码
    ├── greet.handler.luchen.go     // 接口签名定义和注册，无需球盖
    ├── greet.pb.go                 // 生产的结构体代码
    └── greet_grpc.pb.go            // 生成的 gRPC 接口签名代码
```
