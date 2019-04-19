---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-28"

keywords: deploy nodejs app, serverless nodejs, back-end nodejs, generated sdk nodejs, cloud foundry deploy nodejs, kubernetes deploy nodejs, virtual nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 部署和集成 Node.js 应用程序
{: #deploy_apps-nodejs}

您可以使用工具链或命令行界面来部署 Node.js 应用程序。工具链是一组工具集成。命令行界面是部署应用程序和服务实例的简单方法。


有关更多信息，请参阅[部署应用程序](/docs/apps/dep-app-tool.html#deploying-apps)。

## 开发无服务器 Node.js 应用程序
{: #serverless-nodejs}

为了便于无服务器开发，您可以使用 IBM 功能即服务 (FaaaS) 产品 {{site.data.keyword.openwhisk}}。{{site.data.keyword.openwhisk_short}} 支持运行应用程序逻辑以响应事件或者来自 Web 或移动应用程序的 HTTP 直接调用，而无需供应或管理服务器。

{{site.data.keyword.openwhisk_short}} 会执行系统管理（例如，自动缩放、可用性管理以及维护），使开发者能够始终专注于编写应用程序逻辑。

有关更多信息，请参阅[开发无服务器应用程序](/docs/apps/deploying/functions.html#serverless)。

## 使用生成的 SDK 集成后端服务
{: #backend_gensdk}

{{site.data.keyword.IBM_notm}} SDK Generator 插件可使用生成的 SDK 轻松地将后端服务集成到应用程序中。对 REST API 进行更改时，可以重新生成 SDK，并替换旧 SDK 以实现无缝 SDK 升级。还可以将 CLI 集成到 DevOps 管道中，并确保每次构建应用程序时，SDK 始终与 API 规范一致。

有关更多信息，请参阅[使用生成的 SDK 集成后端服务](/docs/swift/backend/cli_sdkgen.html#sdkgen-cli)。

## 部署到 Kubernetes 集群
{: #deploy_kube}

您可以了解如何使用 {{site.data.keyword.cloud_notm}} Kubernetes Service来部署使用 Watson Tone Analyzer 的容器化 Node.js 应用程序。在提供的场景中，虚构的公关公司使用 {{site.data.keyword.cloud_notm}} 服务来分析其新闻稿，并接收有关其消息语气的反馈。有关更多信息，请参阅[将应用程序部署到 Kubernetes  集群中](/docs/containers/cs_tutorials_apps.html#cs_apps_tutorial)教程。

## 部署到 Cloud Foundry
{: #node-deploy-cf}

此选项可部署云本机应用程序，而无需管理底层基础架构。

如果计划将应用程序部署到 [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry/index.html#about)，那么必须[准备 {{site.data.keyword.cloud_notm}} 帐户](/docs/cloud-foundry/prepare-account.html#prepare)。

如果帐户有权访问 {{site.data.keyword.cfee_full_notm}}，那么可选择部署程序类型**[公共云](/docs/cloud-foundry-public/about-cf.html#about-cf)**或 **[Enterprise Environment](/docs/cloud-foundry-public/cfee.html#cfee)**，后者可用于创建和管理隔离环境以专门针对您的企业托管 Cloud Foundry 应用程序。

## 部署到虚拟服务器
{: #virtual_deploy}

虚拟服务针对所有工作负载类型提供了更高程度的透明性、可预测性和自动化。Terraform 用于安全、高效地构建和更改基础架构以及控制基础架构的版本。

有关更多信息，请参阅[部署到虚拟服务器](/docs/apps/vsi-deploy.html#vsi-deploy)。
