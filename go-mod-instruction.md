#go mod的说明

注意China go mod加速问题：[官方讨论](https://github.com/golang/go/issues/31755)

关联问题：

[Go Modules 和 Proxy](https://github.com/guanhui07/blog/issues/642)

[Go Module China 加速](https://github.com/developer-learning/night-reading-go/issues/468)

设置参考：

+ 设置China国内加速(1)

```bash

go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct   //go >= 1.13
go env -w GOSUMDB=sum.golang.org //可选
```

+ 设置China国内加速(2)

```bash
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.io,direct
```

+ 设置China国内加速(3)

```bash
go env -w GO111MODULE=on
go env -w GOPROXY=https://some.other.proxy,direct
go env -w GOSUMDB=sum.golang.google.cn
```
