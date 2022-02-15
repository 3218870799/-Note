# 一：简介

前置：Gin框架：





# 二：服务注册与发现

## Consul

1：将服务注册到Consul中；

```go
consulReg:=consul.NewRegistry(
    registry.Addrs("192.168.1335:8500")
)
```



