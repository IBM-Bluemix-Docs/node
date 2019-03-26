---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-28"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 配置 Node.js 环境
{: #configure-nodejs}

通过实施云本机原则，Node.js 应用程序可以从一个环境移至另一个环境（如从测试环境移至生产环境），而无需更改代码或执行除此之外未测试的代码路径。

当提供配置的方式存在重大差异时，会出现问题，具体取决于开发环境。例如，Cloud Foundry 使用字符串化的 JSON 对象，而 Kubernetes 使用平面值或字符串化的 JSON 对象。除了 Kubernetes 外，本地开发也有不同的注意事项。凭证可以在公共和专用中以不同方式提供，这使得应用程序在不同环境之间保持不变愈加困难。

无论您是需要添加 {{site.data.keyword.cloud}} 对现有应用程序的支持，还是要使用入门模板工具包创建应用程序，目标都是为任何开发平台上的 Node.js 应用程序提供可移植性。

## 向现有 Node.js 应用程序添加 {{site.data.keyword.cloud_notm}} 配置
{: #addcloud-env-nodejs}

[`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env) 模块聚集了各种云提供者（例如，CloudFoundry 和 Kubernetes）的环境变量，因此应用程序独立于环境。

### 安装 `ibm-cloud-env` 模块
{: #install-module-nodejs}

1. 使用以下命令安装 `ibm-cloud-env` 模块：
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. 在代码中通过引用 `mappings.json` 来初始化模块，例如：
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  如果未在 `IBMCloudEnv.init()` 中指定映射文件路径，那么该模块将尝试从缺省路径 `/server/config/mappings.json` 装入映射。
  {: tip}

  示例 `mappings.json` 文件：
  ```json
  {
    "service1-credentials": {
        "searchPatterns": [
            "cloudfoundry:my-service1-instance-name", 
            "env:my-service1-credentials", 
            "file:/localdev/my-service1-credentials.json" 
        ]
    },
    "service2-username": {
        "searchPatterns":[
            "cloudfoundry:$.service2[@.name=='my-service2-instance-name'].credentials.username",
            "env:my-service2-credentials:$.username",
            "file:/localdev/my-service1-credentials.json:$.username" 
        ]
    }
  }
  ```
  {: codeblock}

### 使用 Node.js 应用程序中的值
{: #values-nodejs}

使用以下命令检索应用程序中的值。

1. 检索 `service1credentials` 变量：
  ```js
  // 这将是字典
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. 检索 `service2username` 变量：
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // 这将是字符串
  ```
  {: codeblock}

现在，通过对从不同云计算提供者引入的差异进行抽象化，应用程序可以在任何运行时环境中实现。

### 过滤标记和标签的值
{: #filter-values-nodejs}

可以过滤模块基于服务标记和服务标签生成的凭证，如以下示例中所示：
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // 返回包含指定服务标记和标签的凭证的 JSON
```
{: codeblock}

## 使用入门模板工具包应用程序中的 Node.js 配置管理器
{: #nodejs-config-skit}

使用[入门模板工具包](https://cloud.ibm.com/developer/appservice/starter-kits/)创建的 Node.js 应用程序会自动随附在许多云部署环境（CF、K8s、VSI 和 Functions）中运行所需的凭证和配置。

### 了解服务凭证
{: #credentials-nodejs}

服务的应用程序配置信息存储在 `/server/config` 目录下的 `localdev-config.json` 文件中。此文件位于 `.gitignore` 目录中，以阻止敏感信息存储在 Git 中。本地运行的任何已配置服务的连接信息（例如，用户名、密码和主机名）都存储在此文件中。

应用程序使用配置管理器从环境和此文件中读取连接和配置信息。应用程序将使用定制构建的 `mappings.json`（位于 `server/config` 目录中）来传达在何处可以找到每个服务的凭证。

本地运行的应用程序可以使用从 `mappings.json` 文件中读取的未绑定凭证连接到 {{site.data.keyword.cloud_notm}} 服务。如果需要创建未绑定的凭证，那么可以在 {{site.data.keyword.cloud_notm}} Web 控制台中或使用 [CloudFoundry CLI](https://docs.cloudfoundry.org/cf-cli/) `cf create-service-key` 命令进行创建。

将应用程序推送到 {{site.data.keyword.cloud_notm}} 时，不会再使用这些值。应用程序将改为使用环境变量来自动连接到绑定的服务。

* **Cloud Foundry**：从 `VCAP_SERVICES` 环境变量获取服务凭证。对于 Cloud Foundry Enrprise Edition，请参阅此[入门教程](/docs/cloud-foundry/getting-started.html#getting-started)以获取更多信息。

* **Kubernetes**：从每个服务的不同环境变量获取服务凭证。

* **{{site.data.keyword.cloud_notm}} Container Service**：从 VSI 或 {{site.data.keyword.openwhisk}} (Openwhisk) 获取服务凭证。

## 后续步骤
{: #next_steps-config notoc}

`ibm-cloud-config` 支持使用以下三种搜索模式类型来搜索值：`cloudfoundry`、`env` 和 `file`。如果您想查看其他支持的搜索模式和搜索模式示例，请查看[用法](https://github.com/ibm-developer/ibm-cloud-env#usage)部分。
