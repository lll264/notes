# 各层次描述

![f649c1ee2eba8d6e8bb2ace42d62c7ba](D:\E\学习\Go\笔记\杂项\assets\f649c1ee2eba8d6e8bb2ace42d62c7ba.png)

![DDD经典分层](D:\E\学习\Go\笔记\杂项\assets\01-1735200156601-6.png)

表现层：展示数据相关的操作

应用层：作为一个桥梁，连接了用户界面和领域层，负责处理用户请求，协调领域对象和业务流程

领域层：包含实体、领域服务，领域服务主要完成本服务与其他服务之间的交流

基础设施层：上面各层提供通用的技术能力：为应用层传递消息，为领域层提供持久化机制，为表现层绘制屏幕组件主要是仓储功能



## 防腐层

防腐层（Anti-Corruption Layer, ACL）是领域驱动设计（DDD）中的一个重要概念，主要用于保护领域模型不受外部系统的影响。

我的理解就是一个服务不直接调用另一个服务，而是实现一个防腐层，通过这个防腐层去调用其他的服务。

这个层次有这些作用：

- **隔离领域模型**：防腐层通过提供一个适配器或接口，将外部系统的复杂性和变化隔离开来，确保领域模型的纯粹性和稳定性。
- **数据转换**：防腐层负责将外部系统的数据格式转换为领域模型所需的格式，避免直接依赖外部系统的实现细节。
- **降低耦合**：通过防腐层，领域模型与外部系统之间的耦合度降低，使得在外部系统发生变化时，领域模型的影响最小化。
- **提高可测试性**：防腐层使得领域模型的测试更加容易，因为可以模拟外部系统的行为，而不需要依赖真实的外部服务。
- **增强灵活性**：如果需要更换外部系统或服务，只需修改防腐层的实现，而不需要对领域模型进行大规模的修改。

### test1

接口与适配器



```go
// ExternalService 接口定义外部服务的功能
type BService interface {
    GetData(issueID string) (*ExternaData, error)
}
```

```go
type ServiceAdapter struct {
    bService BStruct
}

```



# 案例

kendoDDD https://github.com/KendoCross/kendoDDD

目录结构 https://github.com/hwholiday/learning_tools/tree/master/ddd-project-example

```
├── cmd 存放 main.go 等
├── adapter
│   ├── grpc
│   └── http
│   └── facade  引用其他微服务（接口防腐层）
├── application 
│   ├── assembler   负责将内部领域模型转化为可对外的DTO
│   └── cqe Command、Query和Event --  入参
│   └── dto Application层的所有接口返回值为DTO -- 出参
│   └── service 负责业务流程的编排，但本身不负责任何业务逻辑
├── domain
│   ├── aggregate 聚合
│   ├── entity 实体
│   ├── event 事件
│   │   ├── publish
│   │   └── subsctibe
│   ├── repo 接口
│   │   └── specification 统一封装查询
│   ├── service 领域服务
│   └── vo 值对象
└── infrastructure
│   ├── config 配置文件
│   ├── pkg 常用工具类封装（DB,log,tool等）
│   └── repository
│   ├── converter domain内对象转化 do {互转}
│   └── do 数据库映射对象
└── types 封装自定义的参数类型,例如 phone 自校验参数        
```
