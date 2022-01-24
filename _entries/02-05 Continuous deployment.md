---
sectionid: lab1-cicd
sectionclass: h2
title: 持续部署
parent-id: lab-1
---

Azure 容器应用允许你使用 GitHub Actions 将修订发布到容器应用。当提交推送到 GitHub 存储库时，将触发 GitHub Action，该操作会更新容器注册表中的容器映像。在注册表中更新容器后，Azure 容器应用会根据更新的容器映像创建新修订版本。

GitHub action 由提交到存储库中的特定分支触发。创建集成链接时，您可以决定哪个分支触发Action。

![Github Action](/media/lab1/githubactionflow.png)

## 设置您的 Github 存储库

为了能够设置持续部署，您需要一个 github 帐户和新创建的存储库。我们创建了一个公共存储库，您可以在其中找到 [Hello World container](https://github.com/mavilleg/azurecontainerapps-helloworld). [Fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo) 并克隆此存储库.

现在，你已拥有源代码，可以对其进行修改并重新生成将推送到 Azure 容器应用的容器。

## 将 Github Action 附加到容器应用

现在，你已拥有要附加到环境的存储库，必须在 Azure 上设置正确的权限才能配置连续部署。将 GitHub 存储库附加到容器应用时，需要提供具有参与者角色的服务主体上下文。我们需要在容器应用中配置的参数是服务主体的 `tenantId`, `cliendId`, 和 `clientSecret`.

{% collapsible %}

``` bash
az ad sp create-for-rbac \
  --name <SERVICE_PRINCIPAL_NAME> \
  --role "contributor" \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> \
  --sdk-auth
  ```

  此命令的返回值是 JSON 有效负载，其中包括服务主体的 `tenantId`, `cliendId`, 和 `clientSecret`.

  {% endcollapsible %}

检索这些值后，必须 [创建一个能够托管新创建的容器的 Azure 容器注册表](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal#:~:text=%20Quickstart%3A%20Create%20an%20Azure%20container%20registry%20using,must%20log%20in%20to%20the%20registry...%20More%20)  

> 此注册表必须启用*Admin User* ，否则与 ACA 的集成将不起作用。

{% collapsible %}

打开注册表，然后在 `Access keys`面板下，单击以`Enabled`启用用户管理员。或使用:

``` bash
az acr update -n <acrName> --admin-enabled true
```

{% endcollapsible %}

配置完成后，可以通过将 GitHub 存储库附加到修订版来继续操作。

![Github Action](/media/lab1/githubattach.png)

一切就绪后，您可以看到一个新文件夹`.github/workflows` 已添加到项目中。它托管一个 YAML 文件，该文件将允许触发自动 GitHub 操作，该操作将部署推送到分支上的任何更改。它还会自动在您的应用程序上设置一些机密，以存储管理员的登录名以联系容器注册表。我们将在本练习的后面部分了解如何管理这些机密。


![Secret ACR](/media/lab1/secretacr.png)

该命令`az containerapp github-action show`返回容器应用的 GitHub 操作配置设置。它返回一个包含 GitHub Action 集成配置设置的 JSON 有效负载。

{% collapsible %}

``` bash
az containerapp github-action show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME>
```

{% endcollapsible %}

让我们来测试一下！

## 将所有内容放在一起

在 VS Code（或您喜欢的 IDE）中打开项目。您应该看到已添加 `.github/workflows`。如果不是这种情况，请将所做的更改同步到项目上（git 拉取请求）。
现在，您可以修改我们从一开始就使用的 Hello World 容器的源代码。例如，您可以更改`index.html`文件下徽标上方的文本。

提交更改后，您可以转到 GitHub 存储库以查看正在发生的 GitHub Action： 

![Github Action process](/media/lab1/action.png)

如您所见，推送更改（提交）会自动触发 GitHub Action工作流，该工作流将构建新容器并部署到注册表中，然后在新版本下部署到容器应用上。您可以通过转到修订管理面板下并查看新置备的修订来验证它。 

![Github Action process](/media/lab1/revisionaction.png)

如您所见，修订版尚未进行负载平衡，这意味着没有流量路由到它。通过在 Azure 容器应用中支持多个修订，可以管理发送到每个修订版本的版本控制和流量。

将部分（或全部）流量发送到应用后，可以测试应用程序的新版本是否正常运行。

![Github Action process](/media/lab1/actionval.png)

所有这些都可以根据需要进行配置。实际上，你可以更改容器应用是否支持多个活动修订。`activeRevisionsMode`接受两个值的属性：

- multiple: 将容器应用配置为允许多个活动修订。
- single: 激活修订版时自动停用所有其他修订版。

启用single模式后，当您创建修订范围更改并创建新修订时，将自动停用任何其他修订。

还可以使用 [`az containerapp revision`] 命令管理修订的不同方面 (https://docs.microsoft.com/en-us/azure/container-apps/revisions-manage?tabs=bash#list) .
