# Micro问题集(FAQ)

本项目用于维护相关Micro的问题，定期存档。

+ 为什么运行 `go run main.go` 程序会直接退出，但不报错，并在控制台显示出命令行的各种参数提出?

答：确保你程序运行需要的默认参数，都正确指定,比如registry、broker等

+ 运行Micro，传递参数时是否有顺序?

答: 是。Micro有子命令，比如API、Network,所以确保不要将子命令的参数在上一级传递或者传递.

答：确保你程序运行需要的默认参数，都正确指定,比如registry、broker等

+ go-micro 支持websocket吗?

答：支持，参看：[教程中的Web示例](https://github.com/micro-in-cn/tutorials/tree/master/examples/basic-practices/micro-api/web)

+ go-micro 是否已经支持websocket的会话管理?教程中没看到.

答：暂不支持，欢迎轮子，欢迎PR。

+ go-micro 开发不用设置go path了吧?

答：go-micro完全支持go mod,不再依赖go path,关于go path的更多说明，参阅golang官方的声明。

+ 使用proxy 的场景示例吗？

答：参看：[使用micro-proxy](https://github.com/micro-in-cn/tutorials/blob/master/examples/senior-practices/micro-proxy/READEME.md)
