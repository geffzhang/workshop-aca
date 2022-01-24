---
sectionid: discover
sectionclass: h2
title: 概要
parent-id: intro
---

[Azure 容器应用](https://docs.microsoft.com/zh-cn/azure/container-apps)是一个新的无服务器容器平台，适用于需要按需缩放以响应 HTTPS 请求、事件或仅作为始终在线的服务或后台作业处理运行而无需管理虚拟机、业务流程协调程序或其他云基础设施的应用程序。借助 Azure 容器应用，可以在完全托管的环境中通过内置的自动缩放、流量路由、应用程序生命周期管理和服务到服务通信来轻松管理容器化应用程序。

虽然应用服务、函数和逻辑应用为应用程序开发人员提供了针对特定领域的问题的完全托管、高生产力的解决方案，但客户需要能够从完全托管的服务退出，并回归到 Kubernetes 以获得完整的微服务应用程序或运行常规用途容器应用程序。Azure 容器应用填补了这一空白，并通过为最常见的容器应用程序方案（包括完全托管环境中的自动缩放、版本管理、应用程序升级和服务到服务通信）提供高级 API 来完善 Azure 应用程序平台。


Azure 容器应用的常见用途包括:

- 部署 API 服务
- 托管后台处理应用程序
- 处理 事件驱动的流程
- 运行微服务

基于 Azure 容器应用生成的应用程序可以根据以下特征动态缩放:

- HTTP 流量
- 事件驱动流程
- CPU 或者 内存负载
- 任何 [KEDA支持的缩放器](https://keda.sh/docs/2.4/scalers/)

![  Azure 容器应用的场景](/media/intro/azure-container-apps-example-scenarios.png)

Azure 容器应用支持运行 打包在任何容器中的应用程序代码，并且没有运行时或编程模型绑定。借助容器应用，你可以享受运行容器的好处，同时无需担心管理云基础设施和复杂的容器业务流程协调程序。

使用 Azure 容器应用，可以:

- [**运行多个容器版本**](https://docs.microsoft.com/zh-cn/azure/container-apps/application-lifecycle-management) 并管理容器应用的应用程序生命周期。

- 根据任何 KEDA 支持的缩放触发器 [**自动缩放**](https://docs.microsoft.com/zh-cn/azure/container-apps/scale-app) 你的应用程序。大多数应用程序可以弹性到零<sup>1</sup>.

- [**启用 HTTPS 入口**](https://docs.microsoft.com/zh-cn/azure/container-apps/ingress) 而无需管理其他 Azure 基础设施

- [**将流量拆分**](https://docs.microsoft.com/zh-cn/azure/container-apps/revisions) 到应用程序的多个版本之间，以进行蓝/绿部署和 A/B 测试方案。

- [**使用内部入口和服务发现**](https://docs.microsoft.com/zh-cn/azure/container-apps/connect-apps) 通过内置的基于 DNS 的服务发现实现仅限内部的安全终端节点.

- [**使用Dapr 构建微服务**](https://docs.microsoft.com/zh-cn/azure/container-apps/microservices) 并访问其丰富的 API 集

- [**访问任何注册表运行容器**](https://docs.microsoft.com/zh-cn/azure/container-apps/containers), 公共或专用的注册表，包括 Docker Hub 和 Azure 容器注册表 （ACR）

- [**使用 Azure CLI 扩展或 ARM 模板**](https://docs.microsoft.com/zh-cn/azure/container-apps/get-started) 来管理应用程序。

- 直接在应用程序中. [**安全管理机密信息**](https://docs.microsoft.com/zh-cn/azure/container-apps/secure-app) 

- 使用 Azure 日志分析[**查看应用程序日志**](https://docs.microsoft.com/zh-cn/azure/container-apps/monitor) 

<sup>1</sup> [根据 CPU 或内存负载进行缩放](https://docs.microsoft.com/zh-cn/azure/container-apps/scale-app) 的应用程序无法缩放到零。
