# V1升级V2指导

## 不兼容性问题【重大】

1. 原默认的HttpClient/Server，改为GRPCClient/Server
2. 原HTTPBroker改为Nats Embbeded版本

## 功能主要改动

1. util/log模块移除，新增logger模块，增强Logger能力，增加zerolog, zap, logus插件支持
2. 将Consul移动到Plugins中，并将Etcd替换Consul
3. HTTP Broker 替换为Embbed Nats
4. Protobuf插件protoc-gen-micro升级匹配V2接口
5. 增加Auth接口
6. 增加自定义GRPC Error特性
7. 增加config service接口与实现

## 升级指导

对于代码层面，我们需要做如下改动：

1. 更新go-micro, micro, protoc-gen-micro到v2版本
2. 将 ~~"github.com/micro/go-micro"~~ 统一改为"github.com/micro/go-micro/v2"
3. 在执行第一步后，我们会遇到一些接口签名不正确的问题，它们集中在Action与Logger

## 开始操作

### 查询更新

因为墙的原因，我们使用go mod模式，并加上代理

```bash
# 切到代码所在目录
$ cd
# 打开go mod
$ go env -w GO111MODULE=on
# 使用代理，避免资源被防火墙屏蔽
$ go env -w GOPROXY="https://goproxy.io,direct"
# 查看可更新版本
$ go list -m -u all | grep "github.com/micro"
# ... 其它无关msg
github.com/micro/cli/v2 v2.1.2
github.com/micro/go-micro/v2 v2.2.0 [v2.3.0]
github.com/micro/mdns v0.3.0
```

我们可以看到有三个包可以更新，它们从上往下分别是cli命令行解析库，go-micro框架库，mdns是服务本地注册库。

### 更新

更新依赖，每个项目的依赖是不同的，下列的更新指令需要因环境作出变动

```bash
$ go get -u github.com/micro/cli/v2@v2.1.2
$ go get -u github.com/micro/go-micro/v2@v2.2.0
$ go get -u github.com/micro/mdns@v0.3.0
```

更新`protoc-gen-micro`，用于重新生成接口文件

```bash
go get github.com/micro/protoc-gen-micro/v2
```

### 重新生成Proto【重要】

参考指令：

```bash
## 普通rpc.proto
$ protoc --proto_path=$GOPATH/src:. --micro_out=. --go_out=. 接口文件.proto
## 引用了go-micro/api的proto
protoc --proto_path=${GOPATH}/src:. --go_out=. --micro_out=Mgithub.com/micro/go-micro/api/proto/api.proto=github.com/micro/go-micro/v2/api/proto:. 接口文件/api.proto
```

对于api类型的proto，也就是包含`import "github.com/micro/go-micro/api/proto/api.proto";`的接口文件需要使用**M**选项来修改api.proto的位置，因为protoc目前对于go mod没有支持。

### 更新代码

我们开始调整被破坏的代码。

本次更新函数签名不一致导致的变动都比较好改，比如Action，它的主要逻辑并没有变动，我们只需要把Action的函数签名对上即可：

**Action**：

Action接收的参数包名cli同样也升级到v2版本

~~"github.com/micro/cli"~~ -> "github.com/micro/cli/v2"

```go
micro.Action(func(c *cli.Context) {
	 // 业务逻辑
}),
```

变成：

```go
micro.Action(func(c *cli.Context) error {
     // 业务逻辑
     return err
}),
```

**Logger**

Logger是V2中新增的接口，V1时只是一个简单的日志库，后面我们会给大家专门介绍V2的Logger，本篇我们只讲v1 util/log转向V2 Logger。

~~"github.com/micro/go-micro/v2/util/log"~~ -> log "github.com/micro/go-micro/v2/logger"

我们使用log保持与V1的包名一致，剩下的调整就比较简单了，这里不赘述。

**GRPC**

V2中GRPC的TLS配置使用不再使用框架提供的Secure函数，使用通用库的TLSConfig：crypto/tls/Config

~~"github.com/micro/go-micro/transport"~~ -> grpcc "github.com/micro/go-micro/v2/client/grpc"

// v1
```go
    import (
        "github.com/micro/go-micro/client"
        "github.com/micro/go-micro/transport"
    )
	// v1
	client.DefaultClient.Init(
		client.Transport(
			transport.NewTransport(transport.Secure(true)),
		),
	)
```

// v2
```go
    import (
        "crypto/tls"

        "github.com/micro/go-micro/v2/client"
        grpcc "github.com/micro/go-micro/v2/client/grpc"
    )
	tlsCfg := &tls.Config{
		InsecureSkipVerify: true,
	}
	client.DefaultClient.Init(
		grpcc.AuthTLS(tlsCfg),
	)
```

**CLI**

CLI V2接口改为使用指针传参，且EnvVar改为数组

~~"github.com/micro/cli"~~ -> "github.com/micro/cli/v2"

v1:
```go
    command := cli.Command{
		// ...
		Flags: []cli.Flag{
			&cli.StringFlag{
				Name:    "address",
				Usage:   "Set the web UI address e.g 0.0.0.0:8082",
				EnvVar: "MICRO_WEB_ADDRESS",
			},
```

v2:
```
	command := cli.Command{
		// ...
		Flags: []cli.Flag{
			&cli.StringFlag{
				Name:    "address",
				Usage:   "Set the web UI address e.g 0.0.0.0:8082",
				EnvVars: []string{"MICRO_WEB_ADDRESS"},
			},
```

## Consul方案

因为V1中包含有Consul注册组件，V2中替换成了ETCD，故使用Consul组件的用户需要手动引用go-plugins/registry/consul，如下：

新增plugins.go文件与main.go同级，将下列import加入plugins.go中

```
import _ "github.com/micro/go-plugins/registry/consul"
```

编译指令参考：

```bash
$ go build main.go plugins.go
```

也或者如果觉得增加plugins.go文件比较麻烦，也可以直接将引用加入main.go中。但是我们推荐使用plugins.go的方式，使核心代码不受插件影响。

## 是否选择升级

如果要升级，**必须**将产线所有使用Go-micro的微服务都要升级，否则会因为Transport不对称导致服务无法调用。

## 未尽之处

如有未解决您的问题的情况，请在微信群告知我们，我们会第一时间更新本篇的方案。请收藏本篇博客，我们根据大家的建议维护本篇升级指导。

## 加入中国区讨论区

中国区讨论区是Micro官方成员创建，面向中国用户的问题讨论群，欢迎大家加入，积极讨论与反馈，推动Go-Micro在中国的发展。


