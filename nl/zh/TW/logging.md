---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 使用 Node.js 進行記載
{: #logging_nodejs}

日誌訊息是一些字串，其中包含與建立日誌項目時微服務的狀態及活動相關的環境定義資訊。在監視應用程式性能方面，需要有日誌才能診斷服務失敗的方式及原因，以及服務扮演 [appmetrics](/docs/node/appmetrics.html#metrics) 的支援角色。

因為在雲端環境中，處理程序都是暫時的，因此必須收集日誌並傳送至其他地方，通常會傳送到一個集中位置，以進行分析。在雲端環境中進行日誌記載最一致的方法是，將日誌項目傳送至標準輸出及錯誤串流，然後讓基礎架構處理剩下的事宜。

您可以使用 [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")，這是適用於 Node.js 的熱門記載架構，並提供許多特有好處，其中包括： 
* 記載至 `stdout` 或 `stderr`
* 各種附加選項
* 可配置的日誌訊息佈置及型樣
* 針對不同日誌種類使用`記載層次`

## 將 Log4js 支援新增至現有 Node.js 應用程式
{: #add_log4j}

1. 首先，在應用程式的根目錄中執行下列 [npm](https://nodejs.org/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 指令，以安裝 `log4js`，這個指令會安裝套件並將其新增至 `package.json` 檔案。
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. 若要在應用程式中使用它，請將下列程式碼行新增至您的應用程式：
  ```js
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

3. 若要將日誌事件寫入至標準錯誤串流，您可以配置附加器，如下列範例所示：
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  如需使用附加器、`記載層次`及配置詳細資料來自訂日誌訊息的相關資訊，請參閱官方的 [log4js-node 文件](https://log4js-node.github.io/log4js-node/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。

## 使用 App Service 應用程式進行監視
{: #monitoring}

依預設，使用 {{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard) 所建立的 Node.js 應用程式會隨附 Log4js。您可以開啟 `server/server.js` 檔案，以查看下列 Log4js 程式碼：
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

依預設，`記載層次`設為 `OFF` 以便安全地用於程式庫，但它可以由應用程式的 **LOG_LEVEL** 環境變數置換。
{: tip}

### 檢視日誌輸出
{: #node-view-log-output}

請參閱來自以原生方式或在雲端環境中執行應用程式的下列範例日誌輸出：
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

您可以使用下列方法，檢視日誌輸出：
* 若為本端環境，請使用 `stdout`。
* 若為 [Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html) 部署，您可以執行下列指令來存取日誌：
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* 若為 [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 部署，您可以執行下列指令來存取日誌：
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## 後續步驟
{: #next_steps-logging notoc}

進一步瞭解在每個部署環境中檢視日誌：
* [Kubernetes 日誌](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")
* [Cloud Foundry 日誌](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html#logging_cf_apps)
* [{{site.data.keyword.openwhisk}} 日誌及監視](/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

使用日誌聚集器：
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK stack](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")
