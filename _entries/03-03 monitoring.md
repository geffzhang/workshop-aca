---
sectionid: lab2-monitoring
sectionclass: h2
title: 监控平台
parent-id: lab-2
---

#### 可观察性

可观察性对于任何应用程序都至关重要。为了确保一切正常，还要检测/预测平台上的任何问题，您应该能够获得360°视图。

#### 测试应用

我们已经知道组件已成功部署，但它们是否完全正常工作？要检查平台，可以进行几次视觉测试。首先在浏览器中打开 Reddog 前端`UI`门户。

{% collapsible %}

请记住Reddog平台的架构。前端 `UI`是容器应用，它在内部**internally**，因此无法在（托管）kubernetes 集群外部访问。 `UI`前面是`Traeffik`入口控制器，它是`Reddog`容器应用程序，公开（互联网）。

若要查找门户的 URL，请在 Azure 容器应用环境中，打开`Reddog`容器应用。在"概述"选项卡中，应找到生成的 FQDN URL。

![Finding the endpoint](/media/lab2/monitor/finding-endpoint.png)

复制URL到任何浏览器中打开 发现一个不错的动态仪表板:

![Running application](/media/lab2/monitor/running-app.png)

> 您是否注意到页面首次显示的延迟？这是由于在不使用平台时删除了正在运行的容器。您将在本研讨会中进一步详细了解这一点。 

{% endcollapsible %}

##### 获取日志

指标很重要，但能够获取应用程序的日志以便能够调试或理解任何不当行为也很重要。

在 Azure 容器应用中，日志记录代理能够捕获容器发送的所有*stdout/stderror*消息。然后，这些消息将推送到 Azure 日志分析，允许你在一个位置（无需任何特定工具）将所有日志记录放在一个位置。

尝试检索`UI`容器上的日志。可以使用命令行或通过门户执行此操作

{% collapsible %}

在 Azure 门户上，打开"日志分析"。您可以使用屏幕的左侧部分查看表和列，并使用 Kusto 语言构建查询。

获取 `UI` 容器日志的查询是 :

``` bash
ContainerAppConsoleLogs_CL 
| where ContainerAppName_s == 'ui'
```

![Get Logs using CLI](/media/lab2/monitor/logs-ui.png)

第二种方法是使用命令行和 Azure CLI. (它可能会要求您先安装 CLI 扩展).

``` bash
az monitor log-analytics query \
  --workspace <LOG_ANALYTICS_WORKSPACE_CLIENT_ID> \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'ui' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" \
  --out table
```

在这里，选择要显示的列和要返回的行数的查询更为复杂

![Get Logs using CLI](/media/lab2/monitor/logs-cli.png)

{% endcollapsible %}

无需安装特定的监控工具（即Prometheus），也无需以交互方式连接到容器以检索其日志。

##### 获取指标

Application Insights 是 Azure Monitor 的一项功能，是面向开发人员和 DevOps 专业人员的可扩展应用程序性能管理 （APM） 服务。使用它来监视您的实时应用程序。它将自动检测性能异常，并包括功能强大的分析工具，可帮助你诊断问题并了解用户实际对你的应用执行了哪些操作。 

幸运的是，Application Insights 已自动部署，并使用 Azure 容器应用自动存储在日志分析中的metrics/logs 。如您所见，如果您挖掘深入一点，Application Insights 可以帮助查看指标、错误、用户流等等。

首先打开Application Insights并观察平台的主要指标

{% collapsible %}

在资源组中，查找"Application Insights"资源。打开它后，您可以看到主要指标，例如失败（应为空），平均响应时间和每秒请求数。 

![Overview metrics](/media/lab2/monitor/overview-metrics.png)

如果您单击一个图表（即响应时间），您将进入"性能"选项卡，您可以在其中查看每个微服务的特定指标。

![Detailed performance](/media/lab2/monitor/performance.png)

{% endcollapsible %}

了解平台运行状况的另一种方法是使用 Application Insights 的"魔术地图"功能来概览整个平台。查找并分析应用程序的映射。

{% collapsible %}

在左侧，打开菜单`Application Map` 。仅从日志中，它能够绘制微服务平台的映射，显示组件之间的交互，平均性能，甚至发生错误时的故障率。 

![App Insights - application map](/media/lab2/monitor/logs-app-insights-maps.png)

{% endcollapsible %}

使用映射快速获取特定微服务的日志，例如收据生成器服务：

{% collapsible %}

在地图上，单击一个微服务，然后在打开的侧面板中单击 `View logs`.

![App Insights - get logs](/media/lab2/monitor/logs-app-insights-logs-app.png)

它应该打开日志分析并自动为您生成用于在地图上绘制此微服务的查询。

![Display specific logs](/media/lab2/monitor/service-logs.png)

{% endcollapsible %}

它只是可观察性的概述，但它显示了 Azure 容器应用中的集成程度。
