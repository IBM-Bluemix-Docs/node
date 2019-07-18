---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-10"

keywords: cos nodejs, object storage nodejs, nodejs data, file storage nodejs, ibm-cos-sdk nodejs, creating object nodejs, downloading object nodejs, static nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 在 Object Storage 中儲存靜態內容
{: #object-storage}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://cloud.ibm.com/docs/services/cloud-object-storage/libraries?topic=cloud-object-storage-node -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://cloud.ibm.com/docs/services/cloud-object-storage?topic=cloud-object-storage-about -->

{{site.data.keyword.cos_full_notm}} 是雲端運算的基礎元件，提供強大的功能給 Apple 開發人員及其應用程式。與將資訊儲存在檔案階層（例如「區塊」或「檔案」儲存空間）中不同，物件儲存庫只包含檔案及其 meta 資料。這些檔案儲存在稱為儲存區的集合中。依據定義，這些物件都是不可變的，因此非常適用於影像、視訊及其他靜態文件等資料。對於經常變更的資料或關聯式資料，您可以使用 [{{site.data.keyword.cloudant_short_notm}}](/docs/node?topic=nodejs-cloudant) 資料庫服務。

{{site.data.keyword.cos_short}} (COS) 是一個儲存空間系統，可用來儲存有彈性、具成本效益且可擴充的非結構化資料。該資料可透過 SDK 存取，或使用 IBM 使用者介面進行存取。您可以使用 {{site.data.keyword.cos_short}}，透過 RESTful API 及 SDK 支援的自助式入口網站，來存取您的非結構化資料。

## 開始之前
{: #prereqs-cos}

請確定已備妥下列必要條件：
1. 您必須具有 [{{site.data.keyword.cloud}} 帳戶](https://cloud.ibm.com/registration){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
2. 您必須具有 [{{site.data.keyword.cos_short}} SDK for Node.js ](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
3. 您必須具有 Node 4.x+。
4. 找出稍後要用於 SDK 起始設定的認證金鑰值：

    * _**endpoint**_ - Cloud Object Storage 的公用端點。您可以從 [{{site.data.keyword.cloud_notm}} 儀表板 ](https://cloud.ibm.com/resources){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 取得端點。
    * _**api-key**_ - 建立服務認證時產生的 API 金鑰。需要有寫入權，才能建立及刪除範例。
    * _**resource-instance-id**_ - Cloud Object Storage 的資源 ID。您可以透過 [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cloud-cli-getting-started) 或 [{{site.data.keyword.cloud_notm}} 儀表板 ](https://cloud.ibm.com/resources){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 取得資源 ID。

## 步驟 1. 建立 {{site.data.keyword.cos_short}} 實例
{: #create-instance-cos}

1. 在 [{{site.data.keyword.cloud_notm}} 型錄](https://cloud.ibm.com/catalog){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中，選取**儲存空間**種類，然後按一下 {{site.data.keyword.cos_short}}。即會開啟服務配置頁面。
2. 提供服務實例的名稱，或使用預設名稱。
3. 選取定價方案，然後按一下**建立**。即會開啟 Object Storage 實例頁面。
4. 在導覽功能表中，選取**服務認證**。
5. 在「服務認證」頁面中，按一下**新建認證**。
6. 在「新增認證」頁面中，確定角色設為**作者**，然後按一下**新增**。即會建立新認證，並將其顯示在「服務認證」頁面上。

## 步驟 2. 安裝 SDK
{: #install-cos}

從指令行使用 [npm](https://nodejs.org/en/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 套件管理程式來安裝 {{site.data.keyword.cos_short}} SDK for Node.js：
```
npm install ibm-cos-sdk
```
{: pre}

## 步驟 3. 起始設定 SDK
{: #initialize-cos}

在應用程式中起始設定 SDK 之後，您可以使用 {{site.data.keyword.cos_short}} 來儲存資料。若要起始設定連線，請提供您的認證，並在一切就緒時提供要執行的回呼函數。

1. 將下列 `require` 定義新增至 `server.js` 檔案，以載入用戶端程式庫。
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. 提供認證，以起始設定用戶端程式庫。
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

  如果需要協助尋找應用程式的認證金鑰值，請查看[開始之前](#prereqs-cos)區段中的*步驟 4*，以瞭解有關在何處尋找這些值的詳細資料。
    還可以參閱 [{{site.data.keyword.cos_short}} 的服務認證](/docs/services/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials)。
  {: tip}

3. 將下列程式碼新增至 `server.js` 檔案。
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

### 使用基本作業管理資料
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### 建立儲存區
```js
function doCreateBucket() {
    console.log('Creating bucket');
    return cos.createBucket({
        Bucket: 'my-bucket',
        CreateBucketConfiguration: {
          LocationConstraint: 'us-standard'
        },
    }).promise();
}
```
{: codeblock}

#### 建立、上傳或改寫物件
```js
function doCreateObject() {
    console.log('Creating object');
    return cos.putObject({
        Bucket: 'my-bucket',
        Key: 'foo',
        Body: 'bar'
    }).promise();
}
```
{: codeblock}

#### 下載物件
<!-- Verify this snippet with Nick when he returns from vacation -->
```js
function doGetObject() {
 console.log('Getting object');
 return cos.getObject({
   Bucket: 'my-bucket',
   Key: 'foo'
 }).createReadStream().pipe(fs.createWriteStream('./MyObject'));
}
```
{: codeblock}

#### 刪除物件
```js
function doDeleteObject() {
    console.log('Deleting object');
    return cos.deleteObject({
        Bucket: 'my-bucket',
        Key: 'foo'
    }).promise();
}
```
{: codeblock}

請參閱[完整文件](/docs/services/cloud-object-storage?topic=cloud-object-storage-node)，以瞭解多組件上傳、安全特性及其他作業。

## 步驟 4. 測試應用程式
{: #test-cos}

所有項目都正確設定嗎？請測試看看！

1. 執行應用程式，確定啟動起始設定及個別作業，例如，建立儲存區，以及將資料新增至儲存區。
2. 回到先前在 Web 瀏覽器中建立的 {{site.data.keyword.cos_short}} 服務實例，然後開啟服務儀表板。
3. 選取使用的儲存區，您會在儀表板中看到新建立的物件。

有困難嗎？請參閱 [{{site.data.keyword.cos_short}} API 參考資料](/docs/services/cloud-object-storage?topic=cloud-object-storage-compatibility-api)。

## 後續步驟
{: #next-cos notoc}

做得好！您已為應用程式新增一個安全持續性等級。嘗試下列其中一個選項，以保持動力：

* 檢視 [{{site.data.keyword.cos_short}} SDK for Node.js ](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 原始碼。
* 請參閱[儲存區及物件作業範例程式碼 ](https://github.com/ibm/ibm-cos-sdk-js#example-code){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
* 「入門範本套件」是使用 {{site.data.keyword.cloud_notm}} 功能最快的方式之一。請檢視[行動開發人員儀表板 ](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中的可用入門範本套件。下載程式碼。執行應用程式！
* 若要進一步瞭解並充分運用 {{site.data.keyword.cos_short}} 提供的所有特性，[請參閱文件](/docs/services/cloud-object-storage?topic=cloud-object-storage-about)。
