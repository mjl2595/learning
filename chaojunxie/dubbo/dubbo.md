## dubbo

### 简介

RPC框架，实现了面向接口的RPC调用，实现了服务注册和发现、负载均衡、容错、扩展等

### 整体架构

![Architecture](http://dubbo.apache.org/img/architecture.png)

0. provider启动
1. provider向注册中心注册服务
2. consumer向注册中心订阅服务
3. 注册中心知会consumer
4. consumer调用provider提供的服务
5. monitor监控调用的次数和时间

### 分层以及组件

|   分层   |   组件    |                             描述                             |
| :------: | :-------: | :----------------------------------------------------------: |
|  业务层  |  Service  |               业务逻辑层，开发者主要关注在这层               |
|  rpc层   |  Config   |                 配置层，用来管理dubbo的配置                  |
|          |   proxy   |   代理层，provider和consumer都会生产proxy用来实现远程调用    |
|          | registry  |                注册层，用来服务注册和服务发现                |
|          |  Cluster  | 集群容错层，负责远程调用的容错策略，负载均衡策略以及路由策略 |
|          |  monitor  |              监控层，负责监控调用次数和调用时间              |
|          | protocol  |                远程调用层，封装调用的具体过程                |
| remoting | Exchange  |    信息交换层，建立request-response模型，封装请求响应模式    |
|          | transport |                          网络传输层                          |
|          | serialize |                           序列化层                           |

### 服务暴露

### 订阅服务

### 调用过程

### SPI



