# 基本概念

+ 是不是使用micro api 就不需要consul或etcd了?

答：这是两个维度的概念，<micro api>micro的API-Gateway,etcd/consul/zk 是第三方服务，用于服务的注册和发现，如果在k8s里面，第三方服务发现和注册中心可以完全不使用，而用K8S默认的DNS更佳。
