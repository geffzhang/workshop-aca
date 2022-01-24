---
sectionid: create-env
sectionclass: h2
title: 创建环境
parent-id: lab-1
---

### 创建环境

在部署容器化应用程序之前，您需要一个"地方"来托管应用程序。在 Azure 容器应用中，底层基础结构称为 `环境`. 环境围绕一组容器应用创建安全边界。部署在同一环境中的容器应用部署在同一虚拟网络中，并将日志写入同一Log Analytics工作区。

Azure 日志分析用于监视容器应用，在创建容器应用环境时需要使用。

让我们从设置一些变量开始:

``` bash
RESOURCE_GROUP="rg-my-container-apps"
LOCATION="northeurope"
LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
CONTAINERAPPS_ENVIRONMENT="my-environment"
```

- **RESOURCE_GROUP**: 将包含容器应用环境的 Azure 资源组
- **LOCATION**: 将在其中部署应用的 Azure 区域。在预览版中，美国北部、北欧和加拿大是唯一受支持的区域
- **LOG_ANALYTICS_WORKSPACE**: 日志分析工作区的名称
- **CONTAINERAPPS_ENVIRONMENT**: **容器应用** 环境的名称.

定义这些变量后，可以创建资源组来组织与新容器应用相关的服务。

```  bash
az group create --name $RESOURCE_GROUP --location "$LOCATION"
```

使用以下命令创建新的 *日志分析工作区* :

```azurecli
az monitor log-analytics workspace create --resource-group $RESOURCE_GROUP --workspace-name $LOG_ANALYTICS_WORKSPACE
```

接下来，检索日志分析Client ID 和 client secret。

#### Bash

请确保单独运行每个查询，以便为请求的完成留出足够的时间。

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

#### PowerShell

请确保单独运行每个查询，以便为请求的完成留出足够的时间。

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=(az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

#### Azure 容器应用环境

各个容器应用将部署到 Azure 容器应用环境。若要创建环境，请运行以下命令：

```azurecli
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET \
  --location "$LOCATION"
```

> 您可能会收到一条错误消息，告知此订阅不允许使用此功能。这意味着服务 Azure 容器应用不适用于所选区域。

创建环境后，就可以部署应用程序了。
