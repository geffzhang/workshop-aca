---
sectionid: prereq
sectionclass: h2
title: 先决条件
parent-id: intro
---


### 先决条件

为了完成 workshop, 您将需要几个组件。如果您还没有它们，系统将引导您获取它们。

- 一个 Azure 订阅（每个用户一个*）
- 一个 GitHub 账号
- VS Code 或者 Visual Studio、Rider

### Azure 订阅

登录到其中一个 Azure 订阅.

{% collapsible %}

请使用您的用户名和密码登录 <https://portal.azure.com>.

此外，请通过在计算机上运行以下命令并按照说明操作来验证 Azure CLI.

``` bash
az account show
az login
```

{% endcollapsible %}

\* 公共预览版期间存在某些 [限制/配额](https://docs.microsoft.com/zh-cn/azure/container-apps/quotas)  例如，此研讨会需要两个 Azure 容器应用环境，并且每个订阅限制为 2 个环境.


| 特征 | 数量 |
|---|---|
| 环境 | 2 |
| 每个环境的容器应用 | 20 |
| 每个容器应用的副本数 | 25 |
| 每个副本的核心数 | 2 |
| 每个环境的核心数 | 50 |

### 工具

在本研讨会期间，你将使用命令行，但大多数操作可能都可以使用 Azure 门户完成。但是，由于该功能处于预览状态，因此门户可能不是最新的，可能不支持所有命令或参数，命令行肯定可以。
 

#### Azure Cloud Shell

你可以从自己的计算机运行命令行（我们建议使用 bash），也可以在使用 Azure 订阅登录后使用 <https://shell.azure.com> 可访问的 Azure Cloud Shell

{% collapsible %}

并使用 Azure 订阅详细信息登录 <https://shell.azure.com> .

选择 **Bash** 作为你的Shell.

![选择 Bash](/media/intro/0-bash.png)

选择 **显示高级设置**

![选择 显示高级设置](/media/intro/1-mountstorage-advanced.png)

 将 **存储帐户 ** 和 **文件共享名称** 设置为资源组名称（全部小写，不带任何特殊字符）保留其他设置不变，然后点击 **创建存储 **

![Azure Cloud Shell](/media/intro/2-storageaccount-fileshare.png)

你现在应该有权访问 Azure Cloud Shell

![Set the storage account and fileshare names](/media/intro/3-cloudshell.png)

{% endcollapsible %}

#### Azure CLI

安装 [Azure CLI](https://docs.microsoft.com/zh-cn/cli/azure/install-azure-cli). (version 2.30以上)

##### 设置

首先从 CLI 登录到 Azure。运行以下命令，并按照提示完成身份验证过程。

``` csharp
az login
```

接下来，将 Azure 容器应用扩展安装到 CLI

``` csharp
az extension add  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl 
```

安装扩展后，请注册命名空间 `Microsoft.Web`.

``` csharp
az provider register --namespace Microsoft.Web
```

#### GitHub

本次研讨会的某些步骤需要使用 GitHub 帐户。如果您还没有，可以在此处免费创建一个: [https://github.com/join](https://github.com/join).
