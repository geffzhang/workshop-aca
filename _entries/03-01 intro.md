---
sectionid: lab2-intro
sectionclass: h2
title: 上下文
parent-id: lab-2
---

现在，你已成功部署了一个简单的应用程序，让我们了解 Azure 容器应用如何帮助处理更复杂的应用程序。您将部署一个名为 Red Dog 的完整微服务应用程序。微服务架构允许您独立开发、升级、版本控制和扩展整个系统中的核心功能领域。Azure 容器应用为部署微服务提供了基础，这些微服务具有以下特点： 

- 独立扩展、版本控制和升级
- 服务发现
- 原生 Dapr 集成

[Red Dog](https://github.com/azure/reddog-code) 应用程序是一个简化的电子商店应用程序，客户下订单，这些订单由 Worker 处理。 同时，订单、收据和账号 存储在不同的持久性系统中。电子商务平台不包含任何 UI，除了用于监视订单的仪表板。

![The Red Dog application](/media/lab2/intro/reddog_code.png)

> 该应用程序可能看起来很复杂，但事实并非如此，您不必完全理解它即可完成研讨会。

以下是应用程序的不同组件的说明

| 服务          | 描述                                                                                                 |
|------------------|-------------------------------------------------------------------------------------------------------------|
| AccountingService | 用于处理、存储和聚合订单数据的服务，将客户订单转换为可通过 UI 展示的有意义的销售指标 |
| Bootstrapper | 一种服务，它利用Entity Framework Core迁移，根据 Reddog.AccountingModel 中的数据模型初始化 Azure SQL DB 中的表|
| LoyaltyService | 通过根据支出修改客户奖励积分来管理忠诚度计划 |
| MakeLineService | 负责模拟和协调当前订单的"队列"。监控"队列"中每个订单的处理和完成情况|
| OrderService | 用于下订单和管理订单的基本 CRUD API |
| ReceiptGenerationService | 生成和存储订单收据的存档程序，用于审计和历史目的  |
| UI | 仪表板显示与单个中心位置相关的订单/销售数据和/或通过混合方案中的企业仪表板跨中心可见性 |
| VirtualCustomers | 模拟客户下订单的"客户模拟"程序|
| VirtualWorker | 模拟客户订单完成的"工人模拟"程序 |
| CorporateTransferService* | Azure 函数负责通过 RabbitMQ 监视订单活动，即在特定中心位置的上下文中下订单和订单完成，并将这些订单活动传播到 Azure 服务总线，以便企业中心使用和查看 |