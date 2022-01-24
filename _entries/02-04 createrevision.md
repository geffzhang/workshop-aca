---
sectionid: lab1-revision
sectionclass: h2
title: 创建修订版本
parent-id: lab-1
---

修订版是容器应用的不可变快照。第一个修订版是在部署容器应用时自动创建的。当容器应用的模板配置发生更改时，将自动创建新修订版本。实际上，虽然修订是不可变的，但它们会受到全局配置值更改的影响，这些更改适用于所有修订版本。

当您启用入口以使容器应用可通过 HTTP 访问时，修订最有用。当你想要将流量从容器应用的一个快照定向到下一个快照时，通常会使用修订。典型的流量方向策略包括A / B测试和BlueGreen部署。

下图显示了具有两个修订版本的容器应用。

![Revision App](/media/lab1/revisionpond.png)

> 请注意，对容器应用所做的更改属于以下两类之一：修订范围更改和应用程序范围更改：

- 修订范围更改是触发新修订的任何更改（例如：对容器的更改、添加或更新缩放规则、对 Dapr 设置的更改等）
- 应用程序范围的更改不会创建修订（例如：更改流量拆分规则、打开或关闭入口、更改机密值等）

### 创建您的第一个修订版

> 不要在修订中使用任何后缀，因为它可能会引起一些 [内部问题](https://github.com/microsoft/azure-container-apps/issues/37). 

让我们创建并部署具有不同布局的 Hello World 应用程序的新版本。为此，您必须在应用程序中部署一个新容器，这意味着我们正在执行修订范围的更改。 我们应用程序的这个新版本可以在 docker hub 上找到 `mavilleg/acarevision-helloworld:acarevision-hellowold`.

预配此新版本后，我们将通过分配百分比值来配置两个修订版之间的流量平均分配。您可以决定如何在不同修订版之间平衡流量。流量拆分规则是通过为不同的修订版本设置权重来分配的。

{% collapsible %}
转到应用面板左侧的修订管理边栏选项卡.
点击 `创建一个新的修订版`

![Revision soluce](/media/lab1/addrevision.png)

然后，可以决定编辑现有容器映像定义或添加新的容器映像定义（但不要忘记删除现有容器映像定义，否则部署可能会失败！单击 `Add` 以提取将用于创建新修订版本的新图像。

![Revision soluce](/media/lab1/addrevision1.png)
  
{% endcollapsible %}

预配新修订版后，可以使用 Azure 门户中的修订管理面板在它们之间拆分流量。命中终结点将导致根据所选的思考（以 % 为单位）提供其中一个修订。 

> 请注意，新修订将保持活动状态，直到你停用它们，或者将容器应用设置为自动停用旧修订版本。

- 非活动修订将保留为容器应用的快照记录，处于特定状态
- 您无需为非活动修订付费.
- 在清除之前，最多有 100 个修订版可用.

你可以用命令 `az containerapp revision list` 该命令查看所有修订版，并用命令 `az containerapp revision show`获取有关特定修订版的更多详细信息。  

{% collapsible %}

```bash
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table
```

``` bash
az containerapp revision show \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

{% endcollapsible %}

这就是配置修订以便能够具有 A/B 测试方案或蓝绿色部署方案的方法。  
