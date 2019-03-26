---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 搭配使用應用程式度量值與 Node.js 應用程式
{: #metrics}

學習如何安裝、存取及瞭解 Node.js 應用程式度量值。您可以使用「[Node Application Metrics](https://developer.ibm.com/code/open/projects/node-application-metrics/) 儀表板」來監視 Node.js 應用程式，以藉由在 Web 型前端中顯示度量值來視覺化 Node.js 應用程式的效能。
{: shortdesc}

## 以視覺化方式識別問題
{: #identify-problems}

應用程式度量值對於監視應用程式的效能而言，非常重要。具有 CPU、記憶體、延遲及 HTTP 這類度量值的度量值即時視圖非常重要，可確保應用程式有效地執行一段時間。您可以使用 Cloud Foundry 的[自動調整](/docs/services/Auto-Scaling/index.html)這類雲端服務，根據度量值動態調整實例，以符合現行工作負載。使用應用程式度量值，您可以精確地通知何時擴增、縮減或清除不再需要的實例以保留低成本。

應用程式度量值會擷取為時間序列資料。聚集及視覺化擷取的度量值有助於識別一般效能問題，例如：

* 部分或所有路徑上的 HTTP 回應時間太慢
* 應用程式的傳輸量太低
* 需求飆升導致速度變慢
* 高於預期的 CPU 使用率
* 記憶體用量偏高或持續成長中（可能會有記憶體洩漏）

內建的「Application Metrics 儀表板」([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash)) 也包括「其他要求」的圖表，其中顯示受支援資料庫（MongoDB、MySQL、Postgres、LevelDB 及 Redis）的資料庫要求持續時間、Socket.IO 及 Riak 事件。

可以從儀表板產生「Node 報告」或「資料堆 Snapshot」，以啟用更深入的分析。

## 將度量值新增至現有 Node.js 應用程式
{: #add-appmetrics-existing}

使用 [`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash) 建構子，將監視特性新增至現有 Express 應用程式，以傳入許多配置選項。例如，其中一個選項使用現有的伺服器，而不是讓 `appmetrics-dash` 啟動額外的伺服器。

### 安裝儀表板
{: #install-appmetrics}

1. 例如，使用下列簡單的 "Hello World" express 應用程式：
  ```js
  var express = require('express')
  var app = express()
  app.get('/', function (req, res) {
      res.send('Hello World!')
  })
  app.listen(3000, function () {
      console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

2. 使用下列 [npm](https://nodejs.org/) 指令來安裝 `appmetrics` 儀表板：
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. 新增下列程式碼，以將 `appmetrics-dash` 支援新增至現有的應用程式：
  ```js
  var dash = require('appmetrics-dash').attach()
  ```
  {: codeblock}

  此指令會告訴 `appmetrics-dash` 使用已建立的伺服器，並新增 `appmetrics-dash` 端點。

  現在，修訂過的程式碼類似下列範例：
  ```js
  var express = require('express')
  var app = express()
  var dash = require('appmetrics-dash').attach()
  app.get('/', function (req, res) {
    res.send('Hello World!')
  })
  app.listen(3000, function () {
    console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

## 從入門範本套件使用度量值
{: #appmetrics-starterkits}

依預設，從「入門範本套件」建立的 Node.js 應用程式會自動隨附 `appmetrics-dash` 及其儀表板，但必須啟用才能使用。

您可以在產生的應用程式原始檔（名為 `/app_name/server/server.js`）中找到 appmetrics 程式碼：
```js
// Uncomment following to enable zipkin tracing, tailor to fit your network configuration:
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## 存取儀表板
{: #access-dashboard}

在您啟動應用程式之後，請在瀏覽器中移至 `http://<hostname>:<port>/appmetrics-dash`。

針對在本端執行的應用程式，使用預設 `localhost:3001/appmetrics-dash`。
{: tip}

「Node.js 的應用程式度量值」監視儀表板使用者介面提供一系列的度量值，包括下列 [Monitoring Metrics for Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be) 影片中所見的 HTTP 要求及事件迴圈延遲。

## 瞭解資料
{: #understanding-data}

![Appmetrics 儀表板](images/appmetricsdash-1.png)

大部分的資料會繪製為折線圖。「HTTP 送入要求」、「HTTP 送出要求」及「其他要求」會針對時間顯示事件持續時間。「HTTP 傳輸量」顯示每秒要求數。「平均回應時間」顯示所花時間平均最長的最常用五個送入 HTTP 要求。CPU 及「記憶體」圖形顯示一段時間的系統和處理程序用量。「資料堆」顯示一段時間的資料堆大小上限及已使用的資料堆大小。「事件迴圈延遲」顯示從 Node.js 事件迴圈中間隔取得的延遲樣本，其中一個點代表最短延遲、一個點代表平均值，而一個點代表每個樣本所花的最長延遲。

如果圖形有數個點，則將游標移至其上方會提供其他資訊。例如，`HTTP 送入要求`會顯示回應時間，以及所要求的 URL。

所有圖形最多會顯示 15 分鐘的資料。

如果受監視應用程式產生了大量資料，則儀表板會自動啟動以顯示資料。當您再次查看「HTTP 送入要求」圖表時，可以看到每個點都顯示 2 秒鐘期間的所有要求。下列工具提示顯示要求總數與所花的平均時間，以及最長時間。最長時間是所繪製的值。

![顯示工具提示](images/tooltip-1.png)




