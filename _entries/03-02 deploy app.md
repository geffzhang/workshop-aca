---
sectionid: lab2-deploy
sectionclass: h2
title: 部署解决方案
parent-id: lab-2
---

### 部署应用

Red Dog 应用程序基于容器化服务和 Azure PaaS 服务，例如存储帐户、Azure SQL 数据库、Redis 或 CosmosDB。

![Micro-services architecture](/media/lab2/deploy/reddog_containerapps.png)

首先 [forking](https://github.com/Azure/reddog-containerapps/fork)  [repo](https://github.com/Azure/reddog-containerapps) 以便在 GitHub 帐户上拥有一份副本。

分叉存储库后，在本地计算机上克隆存储库。

![Clone the repository](/media/lab2/deploy/clone-repo.png)

浏览存储库的内容。最有趣的部分是"deploy/bicep"文件夹。使用基础设施即代码和 Bicep 技术，可以在一个命令行中部署所有组件，包括 Azure 容器应用实例、不同的 PaaS 服务以及容器化应用程序。

要部署整个环境，只需执行脚本**run.sh**。为此，请打开终端，登录到 Azure 并执行脚本。 确保  [安装好Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/install) .

{% collapsible %}

首先，登录到 Azure 并根据需要选择相关订阅

``` bash
az login
```

{% endcollapsible %}

然后执行脚本

{% collapsible %}

``` bash
. run.sh
```

![Deployment in progress](/media/lab2/deploy/warnings.png)

> 注意：可能会收到一些警告，因为 Azure 容器应用仍处于预览状态，并且某些 ARM 资源可能未完全声明。安装应该顺利进行。部署整个环境大约需要 15 分钟（有时甚至更长时间）。在等待时，您可以随意使用在第一个实验室中创建的环境。如果部署失败（显示错误消息），只需再次运行脚本，它就是幂等的。 [idempotent](https://en.wikipedia.org/wiki/Idempotence).

{% endcollapsible %}

部署成功后，打开 Azure 门户并注意名为*reddog-RANDOM_ID* 的新资源组，例如*reddog-vl7cflbopmqhu* 

打开它时，可以看到所有资源都已成功创建和部署。

![The new resource group](/media/lab2/deploy/rg-reddog.png)
