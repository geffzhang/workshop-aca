---
sectionid: lab2-rotation
sectionclass: h2
title: 机密轮换
parent-id: lab-2
---

管理应用程序生命周期的主要关注点之一是其机密/证书管理。事实上，出于安全原因（泄漏或只是良好的操作），您将不得不在密钥/秘密/证书上进行轮换。让我们看看如何使用 Azure 容器应用完成机密管理。

### 管理你的机密

在已部署的 Reddog 应用程序 `receipt-generation-service`中，容器有两个机密，用于将自身连接到服务总线，发送他要发布到存储帐户的凭据。 

 ![The receipt secret](/media/lab2/rotation/secretrotation.png)

每秒它都会收到一封凭据，然后将其保存到 Blob 存储帐户`receipts`中。我们将模拟存储帐户访问密钥的轮换。

为此，请转到存储帐户，以便轮换主密钥和辅助密钥。

{% collapsible %}

转到边栏`Access key` 选项卡下的存储帐户，然后单击 `Rotate keys`按钮。 

![Rotation Key](/media/lab2/rotation/sarot.png)

{% endcollapsible %}

完成后，你将看到 Blob 存储不再从我们的应用程序接收任何收据。可以通过删除"Receipts"blob 容器并在几秒钟后重新创建它来轻松验证这一点（Azure 可能会显示一条错误消息，只需在几秒钟后重试）。Blob 容器应保持为空，因为应用程序不再具有写入新收据的权限。

#### 获取新密钥

为了设置存储帐户与 `receipt-generation-service`  存储帐户之间的连接，必须检索新密钥并编辑密钥`blob-storage-key`的值。

{% collapsible %}

在边栏选项卡 `Access key`下的存储帐户上，复制密钥值。然后转到容器应用面板的边栏选项`Secrets`卡，以便按新复制的密钥编辑密钥的旧值。

![Rotation Key](/media/lab2/rotation/sarot3.png)

{% endcollapsible %}

> 请注意，此更改是应用程序范围的更改，不会重新创建修订版本。话虽如此，必须重新启动修订才能传播密钥的新值。这应该通过保存对密钥所做的更改来自动完成。但是，如果不是这种情况，则必须使用 CLI 重新启动修订版。 

{% collapsible %}

``` bash
az containerapp revision restart \
  --name <REVISION_NAME> \
  --app <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

{% endcollapsible %}

现在，如果返回到`receipts` Blob，则应再次看到收到的所有收据。请注意，由于服务总线的保留期，你还将看到在轮换期间发生的回执。这也演示了一个开发良好的应用程序对于其复原能力和容错能力至关重要。
