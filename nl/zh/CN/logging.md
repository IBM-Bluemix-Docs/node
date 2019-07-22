---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-10"

keywords: nodejs logging, view logs nodejs, add logging nodejs, log4j nodejs, stdout nodejs, nodejs log, output nodejs, nodejs logger

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js 中的日志记录
{: #logging_nodejs}

日志消息字符串包含微服务在日志条目创建时的状态和活动的上下文相关信息。需要日志来诊断服务失败的方式和原因，并在监视应用程序运行状况中起到支持 [appmetrics](/docs/node?topic=nodejs-metrics) 的作用。

考虑到云环境中进程的瞬态性质，必须收集日志并将其发送到其他位置（通常是集中位置）以供分析。在云环境中进行日志记录最一致的方法是将日志条目发送到标准输出和错误流，从而使基础架构处理其余事务。

您可以使用 [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")，这是 Node.js 的常用日志记录框架，提供了许多本机优点，包括： 
* 将日志记录到 `stdout` 或 `stderr`
* 各种附加选项
* 可配置的日志消息布局和模式
* 对不同日志类别使用 `Log Levels`

## 向现有 Node.js 应用程序添加 Log4js 支持
{: #add_log4j}

1. 首先，通过在应用程序根目录中运行以下 [npm](https://nodejs.org/en/){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 命令安装 `log4js`，该命令会安装软件包并将其添加到 `package.json` 文件。
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. 要在应用程序中使用 log4js，请向应用程序添加以下代码行：
  ```js
  var log4js = require('log4js');
  
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **stdout 输出**：
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

3. 要将日志事件写入到标准错误流，可以配置类似于以下示例的附加器：
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  有关使用附加器、`日志级别`和配置详细信息来定制日志消息的更多信息，请参阅官方的 [log4js-node 文档](https://log4js-node.github.io/log4js-node/){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")。

## 使用 App Service 应用程序进行监视
{: #monitoring}

缺省情况下，使用 {{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 创建的 Node.js 应用程序随附 Log4js。您可以打开 `server/server.js` 文件以查看以下 Log4js 代码：
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

缺省情况下，`Log Level` 设置为 `OFF`，以便可以在库中安全使用，但它可由应用程序的 **LOG_LEVEL** 环境变量覆盖。
{: tip}

### 查看日志输出
{: #node-view-log-output}

查看以本机方式或在云环境中运行应用程序所产生的以下样本日志输出：
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

您可以使用以下方法查看日志输出：
* 对于本地环境，使用 `stdout`。
* 对于 [Cloud Foundry](/docs/cli/reference?topic=cloud-cli-ibmcloud_commands_apps#ibmcloud_app_logs) 部署，可以通过运行以下命令访问日志：
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* 对于 [Kubernetes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 部署，可以通过运行以下命令访问日志：
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## 后续步骤
{: #next_steps-logging notoc}

了解有关在每个部署环境中查看日志的更多信息：
* [Kubernetes 日志](https://kubernetes.io/docs/concepts/cluster-administration/logging/#basic-logging-in-kubernetes){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")
* [Cloud Foundry 日志](/docs/services/CloudLogAnalysis/cfapps?topic=cloudloganalysis-logging_cf_apps)
* [{{site.data.keyword.openwhisk}} 日志和监视](/docs/openwhisk?topic=cloud-functions-logs)

使用日志聚集器：
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK 堆栈](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")
