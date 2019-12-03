# Micro问题集(FAQ)

本项目用于维护相关Micro的问题，定期存档。**[更多说明](https://github.com/micro-in-cn/questions/blob/master/instruction.md)**

**基本概念相关的问题看这里[basic concept](basic-concept.md)**

## 问题列表

+ 为什么consul现在用不了? 一运行服务就出错.

答：go-micro 当前默认集成的注册中心是etcd，如果想继续使用consul需引入插件[go-plugins](https://github.com/micro/go-plugins/tree/master/registry/consul)。迁移计划公告见：[deprecating-consul](https://micro.mu/blog/2019/10/04/deprecating-consul.html)

+ 为什么运行 `go run main.go` 程序会直接退出，但不报错，并在控制台显示出命令行的各种参数提出?

答：确保你程序运行需要的默认参数，都正确指定,比如registry、broker等

+ 运行Micro，传递参数时是否有顺序?

答: 是。Micro有子命令，比如API、Network,所以确保不要将子命令的参数在上一级传递.

+ go-micro 支持websocket吗?

答：支持，参看：[教程中的Web示例](https://github.com/micro-in-cn/tutorials/tree/master/examples/basic-practices/micro-api/web)

+ go-micro 是否已经支持websocket的会话管理?教程中没看到.

答：暂不支持，欢迎轮子，欢迎PR。

+ go-micro 开发不用设置go path了吧?

答：go-micro完全支持go mod,不再依赖go path,关于go path的更多说明，参阅golang官方的声明。

+ 使用proxy 的场景示例吗？

答：参看：[使用micro-proxy](https://github.com/micro-in-cn/tutorials/blob/master/examples/senior-practices/micro-proxy/READEME.md)

+ 如何理解go-mciro的retry和超时机制。

答：不管你retry设置多少次，其中的backoff只是再次发起请求的等待时间，跟你参数设置的超时没关系，参数传入的timeout到了，就会退出，具体参看源码。

+ go-micro的broker是有状态的码?

答：除默认的http broker外，凡以tcp建立会话的broker都是有状态的。


