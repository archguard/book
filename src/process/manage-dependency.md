# 管理依赖

## 问题

## 解决方案

### 示例：上下文策略

参考：[Context Mapping](https://github.com/ddd-crew/context-mapping/)

![Context Map CheatSheet](https://github.com/ddd-crew/context-mapping/raw/master/resources/context-map-cheat-sheet.png)

团队关系：

- 相互依存（Mutually Dependent）
- 上游-下游（Upstream Downstream）
- Free（自由）

DDD 中的示例：

- 共享内核（// Symmetric relation）
    - 共享内核（SharedKernel）
    - 合伙（Partnership）
- 上游-下游（// Upstream Downstream）
    - 客户供应商（CustomerSupplier）
    - 遵奉者（Conformist）
    - 防腐层（AntiCorruptionLayer）
    - 开放主机服务（OpenHostService）。允许下游模块访问上游模块的内部状态，但是要求上游模块提供一个开放的接口，以便下游模块可以访问它的内部状态。
    - 发布的语言（PublishedLanguage）。
    - 各行其道（SeparateWay）。核心思想是：上游和下游各自独立开发，不需要知道对方的存在，只需要知道对方提供的服务即可。
