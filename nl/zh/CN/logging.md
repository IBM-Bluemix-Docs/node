---

copyright:
  years: 2018
lastupdated: "2018-09-18"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js 中的日志记录
{: #logging_nodejs}

日志消息是包含有关创建日志条目时微服务的状态和活动的上下文信息的字符串。需要日志来诊断服务失败的方式和原因，并在监视应用程序运行状况中起到支持 [appmetrics](appmetrics.html) 的作用。

考虑到云环境中进程的瞬态性质，必须收集日志并将其发送到其他位置，通常发送到集中位置以供分析。在云环境中进行日志记录最一致的方法是将日志条目发送到标准输出和错误流，从而使基础架构处理其余事务。

## 向 Node.js 应用程序添加 Log4js 支持
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) 是 Node.js 的常用日志记录框架，提供了许多本机优点，包括： 
* 将日志记录到 `stdout` 或 `stderr`
* 各种附加选项
* 可配置的日志消息布局和模式
* 对不同日志类别使用日志级别

1. 要使用 Log4js，请在应用程序的根目录中运行以下 [npm](https://nodejs.org/) 命令，此命令会安装该软件包并将其添加到 `package.json` 文件。
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. 要在应用程序中使用 Log4js，请添加以下代码行：
  ```javascript
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

有关使用附加器定制日志消息以及日志级别的更多信息及配置详细信息，请参阅官方的 [log4js-node 文档](https://log4js-node.github.io/log4js-node/)。

## 使用 App Service 监视日志
{: #monitoring}

缺省情况下，使用 {{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard) 创建的 Node.js 应用程序随附 Log4js。以本机方式或在云环境中运行该应用程序将生成类似于以下内容的输出：`2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`。您可以如下所示查看输出：
* 在本地运行时，请使用 `stdout`。
* 查看 [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) 和 [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) 部署的日志，这些日志可通过 `ibmcloud app logs --recent <APP_NAME>` 和 `kubectl logs <deployment name>` 进行访问。

在 `server/server.js` 文件中，可以看到以下代码：
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

缺省情况下，“日志级别”设置为 `INFO`，可以通过应用程序的 **LOG_LEVEL** 环境变量覆盖。
{: tip}

## 后续步骤
{: #next_steps notoc}

了解有关查看每个部署环境中日志的更多信息：
* [Kubernetes 日志](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cloud Foundry 日志](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} 日志记录和监视](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

使用日志聚集器：
* [{{site.data.keyword.cloud_notm}} Log Analysis](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK 堆栈](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
