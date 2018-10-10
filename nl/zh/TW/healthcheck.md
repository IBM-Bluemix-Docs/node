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

# 在 Node.js 應用程式中使用性能檢查
{: #healthcheck}

性能檢查提供一種簡單機制，可判定伺服器端應用程式是否適當地運作。許多部署環境（例如 [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) 及 [Kubernetes](https://www.ibm.com/cloud/container-service)）都可以配置成定期輪詢性能端點，以判定您的服務實例是否已備妥可以接受資料流量。

## 性能檢查概觀
{: #overview}

性能檢查一般是透過 `HTTP` 進行存取，並使用標準回覆碼來指出 `UP` 或 `DOWN` 狀態。範例包括：傳回 `200` 表示 `UP`，傳回 `5xx` 表示 `DOWN`。例如，當應用程式無法處理要求時，會使用 `503` 回覆碼，而當伺服器遭遇錯誤狀況時，則會使用 `500`。性能檢查的回覆值是可變的，但是最小 JSON 回應（例如 `{“status”: “UP”}`）則是一致的。

Kubernetes 定義[存活性](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)及[就緒](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)探測：

* 存活性探測可讓應用程式指出是否可以重新啟動處理程序。適用一個考量因素子集：例如，如果達到記憶體臨界值，則 `liveness` 檢查可能會失敗。如果您的應用程式是在 Kubernetes 上執行，請考慮新增 `liveness` 端點，以確保處理程序在必要時重新啟動。

* 就緒探測用於自動遞送決策。成功或失敗指出應用程式是否可以接收新的工作。此端點可以包括必要下游服務的考量。請考慮快取下游服務檢查的結果，以將系統的整體負載降到最低，例如，最多每秒測試一次資料庫連線功能。

Cloud Foundry 僅使用一個性能端點。如果此檢查失敗，則 Cloud Foundry 會重新啟動該處理程序。但是，如果成功，則 Cloud Foundry 假設處理程序可以處理新工作。此性能檢查會將單一端點設為存活性與就緒之間的組合。

## 將性能檢查新增至現有 Node.js 應用程式
{: #add-healthcheck-existing}

若要將性能檢查端點新增至現有應用程式，請從建立新的路徑開始，如下列範例所示：
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

在這裡新增有意義的準則，可確保來自 `/health` 端點的成功回應正確地指出服務已備妥可處理要求。

## 透過 Node.js 入門範本套件應用程式存取性能檢查
{: #healthcheck-starterkit}

依預設，當您使用「入門範本套件」來產生 Node.js 應用程式時，`/health` 中具有基本（未獲授權）性能檢查端點，可用來檢查應用程式的狀態 (UP/DOWN)。

下列 `/server/routers/health.js` 檔案提供性能檢查端點程式碼：
```js
var express = require('express');

module.exports = function(app) {
  var router = express.Router();

  router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });

  app.use("/health", router);
}
```
{: codeblock}

您可以自訂檢查，以確保只有在應用程式已備妥可接受資料流量時才會順利將它傳回。
{: tip}
