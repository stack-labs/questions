# etcd 与 grpc 版本不兼容的问题

etcd v3版本和 目前grpc v1.27.0 和 v1.28.0 存在不兼容的问题，运行时会出现以下错误：
```bash
# github.com/coreos/etcd/clientv3/balancer/resolver/endpoint
../../../go/pkg/mod/github.com/coreos/etcd@v3.3.18+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:114:78: undefined: resolver.BuildOption
../../../go/pkg/mod/github.com/coreos/etcd@v3.3.18+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:182:31: undefined: resolver.ResolveNowOption
# github.com/coreos/etcd/clientv3/balancer/picker
../../../go/pkg/mod/github.com/coreos/etcd@v3.3.18+incompatible/clientv3/balancer/picker/err.go:37:44: undefined: balancer.PickOptions
../../../go/pkg/mod/github.com/coreos/etcd@v3.3.18+incompatible/clientv3/balancer/picker/roundrobin_balanced.go:55:54: undefined: balancer.PickOptions
```
解决方案： 在go.mod中将grpc的版本修改为v1.26.0
```go.mod
replace (
	google.golang.org/grpc => google.golang.org/grpc v1.26.0
)
```