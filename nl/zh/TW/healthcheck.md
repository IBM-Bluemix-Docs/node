---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-28"

keywords: healthcheck node, add healthcheck node, healthcheck endpoint nodes, readiness node, liveness node, endpoint node, probes node, health check node

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 在 Node.js 應用程式中使用性能檢查
{: #node-healthcheck}

性能檢查提供一種簡單機制，可判定伺服器端應用程式是否適當地運作。雲端環境（像是 [Kubernetes](https://www.ibm.com/cloud/container-service){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 及 [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 可以配置為定期輪詢性能端點，以判定您的服務實例是否準備好接受資料流量。

## 性能檢查概觀
{: #node-healthcheck-overview}

性能檢查提供一種簡單機制，可判定伺服器端應用程式是否適當地運作。它們通常是透過 HTTP 耗用，並使用標準回覆碼來指出 UP 或 DOWN 狀態。性能檢查的回覆值是可變的，但是最小 JSON 回應（例如 `{"status": "UP"}`）是相同的。

Kubernetes 具有細緻入微的處理程序性能記號。它定義兩個探測：

- _**readiness**_ 探測用來指出處理程序是否可以處理要求（可遞送）。

  Kubernetes 不會將工作遞送至無法通過 readiness 探測的容器。readiness 探測可能失敗的情況包括服務未完成起始設定，或是因為其他因素而忙碌、超載或無法處理要求。

- _**liveness**_ 探測用來指出處理程序是否要重新啟動。

  Kubernetes 停止並重新啟動無法通過 `liveness` 探測的容器。使用 liveness 探測可清除處於無法復原狀態的處理程序，例如耗盡記憶體，或是內部處理程序當機之後容器未適當地停止。

作為比較的附註，Cloud Foundry 使用一個性能端點。如果這項檢查失敗，處理程序會重新啟動，但如果成功，要求便會遞送給它。在此環境中，端點至少會在處理程序存活時成功。已配置了起始延遲，以便延遲性能檢查，直到服務完成起始設定為止，以避免重新啟動的循環。

下表提供 readiness、liveness 及單一性能端點要提供之回應的指引。

|狀態| Readiness                   | Liveness                   |性能|
|----------|-----------------------------|----------------------------|---------------------------|
|          | 非 OK 會導致不載入| 非 OK 會導致重新啟動| 非 OK 會導致重新啟動|
| Starting | 503 - 無法使用| 200 - OK                   |使用延遲以避免測試|
| Up       | 200 - OK                   | 200 - OK                  | 200 - OK                  |
| Stopping | 503 - 無法使用| 200 - OK                   | 503 - 無法使用|
| Down     | 503 - 無法使用| 503 - 無法使用| 503 - 無法使用|
| Errored  |500 - 伺服器錯誤|500 - 伺服器錯誤|500 - 伺服器錯誤|

## 將性能檢查新增至現有 Node.js 應用程式
{: #add-healthcheck-existing}

從建立新的路徑開始，將最小的性能或存活檢查新增至現有應用程式，如下列範例所示：
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

使用瀏覽器，透過存取 `/health` 端點的方式，來檢查應用程式的狀態。

## 透過 Node.js 入門範本套件應用程式存取性能檢查
{: #node-healthcheck-starterkit}

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

## readiness 及 liveness 探測的建議
{: #node-readiness-probes}

如果下游服務無法使用時沒有可接受的撤回措施，readiness 探測可以在其結果中包含下游服務連線的可行性。這並不表示直接呼叫下游服務所提供的性能檢查，因為基礎架構會為您檢查。相反地，請考慮驗證您應用程式對下游服務之現有參照的性能：這可能是與 WebSphere MQ 的 JMS 連線，或已起始設定的 Kafka 消費者或生產者。如果您檢查了對下游服務之內部參照的可行性，請將結果予以快取，將性能檢查對您應用程式的影響降至最低。

相反地，liveness 探測對於檢查的對象很慎重，因為失敗會導致立即終止處理程序。簡單的 http 端點、一律傳回 `{"status": "UP"}` 與狀態碼 `200`，便是一個合理的選項。

### 新增 Kubernetes readiness 及 liveness 的支援
{: #kube-readiness-add}

來自 [CloudNativeJS](https://github.com/cloudnativejs){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 的 [`cloud-health-connect`](https://github.com/CloudNativeJS/cloud-health-connect){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 程式庫提供架構以便定義 Node 中不同的 liveness 和 readiness 端點，它們允許針對每個端點的狀態組合來源。

## 在 Kubernetes 中配置 readiness 和 liveness 探測
{: #kube-readiness-config}

請與 Kubernetes 部署同時宣告 liveness 和 readiness 探測。兩種探測都使用相同的配置參數：

* kubelet 在第一個探測之前等待 `initialDelaySeconds`。

* kubelet 每隔 `periodSeconds` 秒探測一次服務。預設值為 1。

* 探測會在 `timeoutSeconds` 秒之後逾時。預設及最小值為 1。

* 如果在失敗之後成功 `successThreshold` 次，探測便成功。預設及最小值為 1。liveness 探測的值必須為 1。

* 當 Pod 啟動而探測失敗時，Kubernetes 會嘗試重新啟動 Pod `failureThreshold` 次，然後放棄。最小值為 1，預設值為 3。
    - 針對 liveness 探測，「放棄」表示會重新啟動 Pod。
    - 針對 readiness 探測，「放棄」表示會將 Pod 標示為 `Unready`。

為了避免重新啟動循環，請將 `livenessProbe.initialDelaySeconds` 設為比起始設定服務更長，且長度必須足夠安全。然後您便可以針對 `readinessProbe.initialDelaySeconds` 使用較短的值，只要服務就緒便將要求遞送給服務。

請參閱下列範例配置：
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```
{: codeblock}

如需相關資訊，請參閱如何 [Configure liveness and readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
