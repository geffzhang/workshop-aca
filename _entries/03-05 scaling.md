---
sectionid: lab2-scaling
sectionclass: h2
title: 可伸缩性
parent-id: lab-2
---

### 可伸缩性

可伸缩性是应用程序复原能力的重要组成部分。您的应用程序应该能够处理失败的负载增加。在云原生世界中，尤其是 Kubernetes，伸缩是手动完成的，但也可以通过创建水平 Pod 自动缩放[Horizontal Pod Autoscaling (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)对象，根据 CPU/内存使用情况进行自动缩放来管理。

Azure 容器应用通过一组声明性缩放规则管理水平自动缩放，以丰富 HPA 机制。默认情况下，Azure 容器应用将缩放到零，并**在不使用时暂停计费**。当容器应用横向扩展时，将按需创建容器应用的新实例。容器应用支持许多缩放触发器，包括使用 Kubernetes 事件驱动的自动缩放 （KEDA） 的 HTTP 和基于事件的触发器。KEDA 是一个丰富的自动缩放器，具有许多由社区持续贡献的事件缩放器选项。有关支持的规模触发器的详细信息，请参 阅[KEDA Scalers](https://keda.sh/docs/scalers/)。

用于缩放（传入/传出）容器应用的触发器列表包括 :

- CPU
- Memory
- HTTP requests
- Event-driver (Keda 支持的任何一个)

#### 使用 Azure 容器应用进行缩放

在容器应用中，可伸缩性通过修订版本进行管理。每个修订版都包含可伸缩性限制（最小和最大副本），但也可以包含自动缩放应用程序的规则。要查看容器应用程序使用的当前副本数，请打开当前活动修订版的"修订管理"选项卡：

![Current scaling](/media/lab2/scale/current-scale.png)

在我们的例子中，UI 应用的最小限制等于 0，最大限制为 10，并且没有自动缩放规则。

> 在预览期间，每个修订版的最大副本数为 25，但 Azure Web 门户目前只允许指定 10 个。如果需要 10 个以上的副本，请使用 CLI

![UI Default scaling](/media/lab2/scale/ui-default-scaling.png)

让我们使用新的缩放规则创建一个新修订版本 :

- 有一个 后缀/名称 "autoscale"
- 最小限制为一个副本
- 最大限制为五个副本
- 具有 HTTP 自动缩放规则，其中并发请求的最大值等于 1（我们希望每个用户一个副本）

{% collapsible %}

打开 UI 容器应用和选项卡 `Revision Management`。单击 `创建一个新的修订版本` 。在第一页中，指定名称后缀，然后单击`Next` 按钮。 

在缩放部分中，配置 1 到 5 个副本之间的限制。

![Define the limits](/media/lab2/scale/minmax.png)

然后添加一个具有`HTTP Scaling` 类型和一个并发请求的规则。 

![Create a scaling rule](/media/lab2/scale/http-rule.png)

单击该`Create`按钮，应创建一个新修订版，并将100%的入口发送到它：

![A new revision is created](/media/lab2/scale/ui-new-revision.png)

{% endcollapsible %}

完成后，应用程序应准备好自动缩放。

#### 超载我们的容器

现在是时候检查我们的容器在用户连接到它时是否会自动缩放了。

##### 安装一个负载测试工具

对我们的平台进行负载测试的更简单方法是使用小型命令行工具来模拟大量请求。你将使用工具 [Vegeta](https://github.com/tsenart/vegeta) ，但如果您愿意，可以用任何其他负载测试工具替换它。

首先安装最新版本的Vegeta

{% collapsible %}

``` bash
# download vegeta
curl -LO https://github.com/tsenart/vegeta/releases/download/v12.8.4/vegeta_12.8.4_linux_amd64.tar.gz

# unzip the app
tar -zxvf vegeta_12.8.4_linux_amd64.tar.gz

# move bin into /usr/bin
sudo mv ./vegeta /usr/bin/vegeta

# check the install
vegeta --version

```

{% endcollapsible %}

您需要创建一个包含要调用的终结点的文件。端点是Reddog应用程序的公开端点，它转到Traeffik应用程序。

{% collapsible %}

创建一个名为 target.txt 的文件，然后复制应用程序的 URL

``` txt
GET https://url-public-endpoint-of-reddog-app
```

{% endcollapsible %}

然后使用命令*attack* 运行Vegeta。如果未给出特定参数，Vegeta 将在定义的目标上每秒发出 50 个请求，并且在您停止之前不会停止。您还可以使用相应的参数速率和持续时间指定每秒请求数和持续时间。  

{% collapsible %}

运行以下命令行，让 Vegeta 生成请求

``` bash
vegeta attack -targets targets.txt -rate=20 and -duration=30s.
```

{% endcollapsible %}

几秒钟后，检查 UI 容器应用的副本数是否已增加。

{% collapsible %}

打开 `ui-autoscale`修订版，然后在概览选项卡中，检查当前复制副本的数量。

![Post load testing](/media/lab2/scale/after-load-testing.png)

{% endcollapsible %}

我们可以确认自动缩放为每个请求创建了一个副本（并行创建大约 20 个请求），但由于修订版中定义的最大限制，横向扩展从未超过五个副本。

关闭 Vegeta 和任何显示 Reddog 应用程序的浏览器选项卡，在几分钟内，您应该能够观察到 UI 容器 App 自动缩减到一个副本，因为它不再被过度使用。
