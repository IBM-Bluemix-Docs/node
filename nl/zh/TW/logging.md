---

copyright:
  years: 2018
lastupdated: "2018-10-04"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 在 Node.js 中記載
{: #logging_nodejs}

日誌訊息是字串，具有在製作日誌項目時有關微服務狀態及活動的環境定義資訊。在監視應用程式性能方面，需要有日誌才能診斷服務失敗的方式及原因，以及服務扮演 [appmetrics](appmetrics.html) 的支援角色。

因為在雲端環境中，處理程序都是暫時的，因此必須收集日誌並傳送至其他地方，通常會傳送到一個集中位置，以進行分析。在雲端環境中進行日誌記載最一致的方法是，將日誌項目傳送至標準輸出及錯誤串流，然後讓基礎架構處理剩下的事宜。

## 將 Log4js 支援新增至 Node.js 應用程式
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) 是 Node.js 的熱門記載架構，並提供許多原生好處，包括： 
* 記載至 `stdout` 或 `stderr`
* 各種附加選項
* 可配置的日誌訊息佈置及型樣
* 使用不同日誌種類的「記載層次」

1. 若要使用 Log4js，請在應用程式的根目錄中執行下列 [npm](https://nodejs.org/) 指令，以安裝套件並將其新增至 `package.json` 檔案。
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. 若要在應用程式中使用它，請新增下列程式碼行：
  ```javascript
  var log4js = require('log4js');
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **stdout 輸出**：
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

如需使用附加器、記載層次及配置詳細資料來自訂日誌訊息的相關資訊，請參閱官方的 [log4js-node 文件](https://log4js-node.github.io/log4js-node/)。

## 使用 App Service 監視日誌
{: #monitoring}

依預設，使用 {{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard) 所建立的 Node.js 應用程式會隨附 Log4js。在本機或在雲端環境中執行應用程式，會產生如下的輸出：`2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`。您可以檢視輸出，如下所示：
* 當您在本端執行時，請使用 `stdout`。
* 在 [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) 及 [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) 部署的日誌中，這些是藉由 `ibmcloud app logs --recent <APP_NAME>` 及 `kubectl logs <deployment name>` 進行存取。

在 `server/server.js` 檔案中，您可以看到下列程式碼：
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

依預設，「記載層次」設為 `OFF` 以便安全地用於程式庫，但它可以由應用程式的 **LOG_LEVEL** 環境變數置換。
{: tip}

## 後續步驟
{: #next_steps notoc}

進一步瞭解在每一個部署環境中檢視日誌：
* [Kubernetes 日誌](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cloud Foundry 日誌](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} 日誌 & 監視](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

使用日誌聚集器：
* [{{site.data.keyword.cloud_notm}} Log Analysis](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK 堆疊](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
