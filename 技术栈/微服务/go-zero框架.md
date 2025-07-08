### go-zero与grpc的区别是什么

**go-zero:**

- **双协议支持：**
  - **HTTP**：通过 `.api` 文件定义 RESTful 接口，自动生成 Go 代码及文档（Markdown/Swagger）。
  - **gRPC**：通过 `zrpc` 模块封装 gRPC 服务端和客户端，支持 Protocol Buffers 序列化。
- **工具链驱动**：通过 `goctl` 工具一键生成 API、RPC、Model 等代码，降低开发成本。

**gRPC**：

- **专注 RPC 通信**：仅提供高性能的远程过程调用能力，不涉及服务治理、配置管理等周边功能。