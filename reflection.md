# reflection

+ 问题：在protoc生成的.micro.go文件中，为什么注册Handler需要在内部定义接口？

示例项目 [greeter](https://github.com/micro/examples/tree/master/greeter)
源码如下：
```go
// Server API for Greeter service

type GreeterHandler interface {
  Hello(context.Context, *Request, *Response) error
}

func RegisterGreeterHandler(s server.Server, hdlr GreeterHandler, opts ...server.HandlerOption) {
  type greeter interface {
    Hello(ctx context.Context, in *Request, out *Response) error
  }
  type Greeter struct {
    greeter
  }
  h := &greeterHandler{hdlr}
  s.Handle(s.NewHandler(&Greeter{h}, opts...))
}

type greeterHandler struct {
  GreeterHandler
}
```
可以看到，在RegisterGreeterHanlder的内部，重新实现了greeter接口。


答：用户在注册Handler时，可能会使用其他的类名称。但是micro内部实现，使用反射获取类名，这样就可能获取不到`Greeter`的类,导致路由无法成功。为了防止此类问题的发生，在函数内部重写和proto中定义一致的接口签名。proto中定义如下：
```proto
service Greeter {
  rpc Hello(Request) returns (Response) {}
}
```
