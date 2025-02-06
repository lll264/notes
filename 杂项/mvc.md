https://www.runoob.com/design-pattern/mvc-pattern.html

- **模型（Model）**：负责数据和业务逻辑，通常包含数据存储、检索和业务规则。
- **视图（View）**：负责显示数据（模型）的用户界面，不包含业务逻辑。
- **控制器（Controller）**：接收用户的输入，调用模型和视图去完成用户的请求。





models 数据库操作

services  业务逻辑

controllers 控制访问调用对应service，gin的handerfunc