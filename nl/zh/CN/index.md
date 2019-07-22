---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-07"

keywords: node getting started, node cloud native, create node app, add node service, node programming guide, node guide

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 入门教程
{: #getting-started}

以下教程指导您完成使用 {{site.data.keyword.cloud_notm}} 提供的工具来构建、本地运行和部署 Node.js 应用程序的步骤。您可以在命令行上使用 [{{site.data.keyword.dev_cli_long}}](/docs/cli?topic=cloud-cli-getting-started) 或使用基于 Web 的 [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")，如以下教程步骤中所示。通过使用这其中任一种方法，您可以在仅仅几分钟内就生成生产就绪型 Node.js 应用程序。

确保使用的是最新的 Node.js LTS 发行版。

## 创建 Node.js 应用程序
{: #node-create-project}

1. 在 {{site.data.keyword.dev_console}} 中的[入门模板工具包](https://cloud.ibm.com/developer/appservice/starter-kits){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 页面中，选择一个在 `Node.js` 中编写的入门模板工具包。此外，还可以通过单击**创建应用程序**并选择 `Node.js` 作为语言来创建空白的入门模板应用程序。

    您必须登录到 {{site.data.keyword.cloud_notm}} 帐户才能创建应用程序。如果您没有帐户，那么可以[注册免费帐户](https://cloud.ibm.com/registration){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")。
    {: tip}

2. 单击**创建应用程序**。
3. 对应用程序**命名**。系统会提供通用应用程序名称（如果您要使用此名称）。
4. 单击**创建**。创建项目后，可以使用工具链进行部署，也可以继续构建，然后通过命令行部署项目。
5. 要在仪表板中创建部署工具链，请单击**部署**。根据您所选方法的指示信息来设置部署目标：
  * **部署到 [IBM Kubernetes Service](/docs/containers?topic=containers-app)**。此选项创建主机（称为工作程序节点）的集群来部署和管理高可用性应用程序容器。可以创建集群或部署到现有集群。
  * **部署到 Cloud Foundry**。此选项可部署云本机应用程序，而无需管理底层基础架构。如果您的帐户有权访问 {{site.data.keyword.cfee_full_notm}}，那么可以选择部署程序类型**[公共云](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)**或**[企业环境](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)**，可使用这些类型来创建和管理隔离的环境，以用于专门为您的企业托管 Cloud Foundry 应用程序。
  * **部署到[虚拟服务器](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server)**。此选项会供应虚拟服务器实例，装入包含您的应用程序的映像，创建 DevOps 工具链，并为您启动第一个部署周期。

6. 完成选项，然后单击**创建**以创建工具链。
7. 如果选择继续使用 CLI 而不使用工具链，请将项目下载到本地计算机，对项目执行 `unzip` 命令，然后通过 `cd` 命令转至根目录。现在，可以使用以下平台命令方法来安装必备软件：

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
```
    {: codeblock}

    Windows（以管理员身份运行）：
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
```
    {: codeblock}

## 添加服务
{: #node-add-service}

1. 返回到 {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} 中您的项目。
2. 单击**添加服务**，选择要添加的服务的类别，单击**下一步**，然后选择服务。例如，要将 NoSQL 数据库添加到应用程序中，请选择**数据**类别，然后选择 **Cloudant** 以提供轻量套餐进行免费开发。{{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} 会根据所选套餐供应服务。
注：如果先前已供应您计划使用的服务，请选择**现有**类别。
3. 供应服务后，单击**下载代码**以使用连接到服务的 SDK 重新生成项目。

<!--
<video of creating a project and adding a service>
-->

## 本地运行应用程序
{: #node-run-local}

1. 使用 `ibmcloud dev build` 命令构建应用程序。
2. 使用 `ibmcloud dev run` 命令在本地运行应用程序。应用程序将在本地系统上的 Docker 容器中运行。可以在浏览器中通过访问 `http://localhost:3000` 来测试应用程序。
3. `http://localhost:3000/health` 上提供了运行状况检查端点。
4. 可以访问 [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 仪表板：`http://localhost:3000/appmetrics-dash`。

<!--
<video>
-->

## 部署到 IBM Cloud
{: #deploy_cloud}

使用 `ibmcloud dev deploy` 命令作为 Cloud Foundry 应用程序部署到 {{site.data.keyword.cloud_notm}}。 

要部署到 {{site.data.keyword.cloud_notm}} 中的 IBM Container Services，请使用以下命令：
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## 后续步骤
{: #node-tutorial-nextsteps notoc}

继续查看有关 Node.js 编程指南的主题，或者要获取更高级部署的信息，可以了解如何创建 Kubernetes 集群并将 Node.js 应用程序部署到该集群。

### 设置 Kubernetes 集群
有关在 {{site.data.keyword.cloud_notm}} 中设置 Kubernetes 集群的更多信息，请查看[教程步骤](/docs/containers?topic=containers-clusters)。

### 将 Node.js 应用程序部署到 Kubernetes 集群
了解如何[将 Node.js 应用程序部署到 Kubernetes 集群](/docs/containers?topic=containers-cs_apps_tutorial)。
