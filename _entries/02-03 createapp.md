---
sectionid: lab1-createapp
sectionclass: h2
title: 部署应用
parent-id: lab-1
---


### 创建你的第一个应用

让我们使用[此处](https://docs.microsoft.com/zh-cn/cli/azure/container) 记录的命令 `az containerapp create`创建和部署您的第一个 hello-world 应用程序. 我们将使用现成的容器映像 `mcr.microsoft.com/azuredocs/containerapps-helloworld:latest`.

> 使用 `az containerapp --help` 发现不同的可用参数

不要忘记设置参数 `--ingress` 为 `external` 以使容器应用可供公共请求使用（向 Internet 公开）. 通过添加查询参数, 可以设置 create 命令返回的结果的格式: `--query configuration.ingress.fqdn`

{% 切换 %}

``` bash
az containerapp create \
  --name my-container-app \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress 'external' \
  --query configuration.ingress.fqdn
```

在我们的例子中，该命令 `create` （仅）返回容器应用的完全限定域名，因为我们指定了参数I  `query`。

![Create an with the console](/media/lab1/create-app.png)

{% endcollapsible %}

将此位置复制到 Web 浏览器以查看以下消息

![Running app](/media/lab1/running-app.png)

打开 [Azure 门户](https://portal.azure.com). 在资源组中，应看到容器应用环境以及容器应用.单击它。在这里，您可以直接查看、诊断或重新配置应用程序，例如更改入口配置、机密、负载平衡或持续部署：

![App in Azure](/media/lab1/created-app-in-azure.png)

就是这样！部署和托管应用程序是多么简单！
