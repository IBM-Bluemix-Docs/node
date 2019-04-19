---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-04"

keywords: nodejs storage, nodejs cloudant, nodejs iam, initialize sdk nodejs, test nodejs app, dbaas nodejs, nodejs-cloudant, store documents nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 將文件儲存在 {{site.data.keyword.cloud_notm}} 中
{: #cloudant}

{{site.data.keyword.cloudantfull}} 是文件導向的「資料庫即服務 (DBaaS)」。它會將資料儲存為 JSON 格式的文件。{{site.data.keyword.cloudant_short_notm}} 已內建可調整性、高可用性及延續性，而且可以輕鬆地配置成在 Node.js 應用程式中使用。並且隨附各種檢索選項，包括 MapReduce、{{site.data.keyword.cloudant_short_notm}} 查詢、全文檢索及地理空間檢索。抄寫功能讓您能輕鬆保持資料庫叢集、桌上型電腦和行動裝置之間的資料同步。
{:shortdesc}

如需相關資訊，請參閱 [{{site.data.keyword.cloudant_short_notm}} 基本](/docs/services/Cloudant/basics?topic=cloudant-ibm-cloudant-basics#ibm-cloudant-basics)。

## 開始之前
{: #prereqs-cloudant}

請確定已備妥下列必要條件：
 * [Nodejs-cloudant ](https://github.com/cloudant/nodejs-cloudant){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 2.3.0+ 用戶端程式庫。
 * 您必須具有 [{{site.data.keyword.cloud}} 帳戶](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
 * 若要存取 {{site.data.keyword.cloudant_short_notm}}，您必須在 [{{site.data.keyword.cloud_notm}} 儀表板](https://cloud.ibm.com/dashboard/apps){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中建立服務，然後從該服務實例啟動「{{site.data.keyword.cloudant_short_notm}} 儀表板」。
 * 這些指示中的程式碼 Snippet 使用 IAM 鑑別。
 
### 使用 {{site.data.keyword.cloudant_short_notm}} 啟用 IAM
{: #enable_IAM-cloudant}

只有新的 {{site.data.keyword.cloudant_short_notm}} 服務實例才能與 {{site.data.keyword.cloud_notm}} IAM 搭配使用。

已啟用所有新的 {{site.data.keyword.cloudant_short_notm}} 服務實例，以在佈建時使用 {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)。當您從「{{site.data.keyword.cloud_notm}} 型錄」中佈建新的實例時，請選擇**僅使用 IAM** 鑑別方法。此模式表示服務連結及認證產生僅提供 IAM 認證。您可以在 [{{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)](/docs/services/Cloudant/guides?topic=cloudant-ibm-cloud-identity-and-access-management-iam-#ibm-cloud-identity-and-access-management-iam-) 找到相關資訊。

## 步驟 1. 建立 {{site.data.keyword.cloudant_short_notm}} 實例
{: #create-instance-cloudant}

當您建立 {{site.data.keyword.cloudant_short_notm}} 的實例時，同時會建立資料庫。

1. 登入 {{site.data.keyword.cloud_notm}} 帳戶。
2. 從 [{{site.data.keyword.cloud_notm}} 儀表板](https://cloud.ibm.com/dashboard/apps){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中，按一下**建立資源**。即會開啟「{{site.data.keyword.cloud_notm}} 型錄」。
3. 在 [{{site.data.keyword.cloud_notm}} 型錄](https://cloud.ibm.com/catalog/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中，選取**資料庫**種類，然後按一下 {{site.data.keyword.cloudant_short_notm}}。即會開啟服務配置頁面。
4. 完成下列欄位中的資訊：
  * **服務名稱** - 鍵入服務實例的名稱，或是使用預設名稱。
  * **選擇要在其中部署的地區/位置** - 選取要在其中部署服務的地區。
  * **選取資源群組** - 選取資源群組，或接受預設值。
  * **可用的鑑別方法** - 選取**僅使用 IAM** 作為鑑別方法。
5. 選取定價方案，然後按一下**建立**。即會開啟服務實例的頁面。
6. 若要建立服務認證，請完成下列步驟：
  1. 從導覽功能表中，選取**服務認證**。
  2. 按一下**新建認證**。即會開啟「新增認證」頁面。
  3. 在「新增認證」頁面中，完成欄位，然後按一下**新增**。即會將新的服務認證新增至服務實例。
  4. 如果您要檢視服務認證詳細資料，請在新認證的**動作**直欄中按一下**檢視認證**。
7. 從導覽功能表中，選取**管理**，然後按一下**啟動 Cloudant 儀表板**。
8. 從導覽功能表中，按一下**資料庫**圖示。
9. 按一下**建立資料庫**，提供資料庫名稱，然後按一下**建立**。即會開啟資料庫頁面。

如果您要查看佈建 {{site.data.keyword.cloud_notm}} 服務實例的相關資訊，請參閱[在 IBM Cloud 上建立 IBM Cloudant 實例指導教學](/docs/services/Cloudant/tutorials?topic=cloudant-creating-an-ibm-cloudant-instance-on-ibm-cloud#creating-a-cloudant-nosql-db-instance-on-ibm-cloud)。

## 步驟 2. 安裝 SDK
{: #install-cloudant}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

以您自己的 Node.js 專案開始，並將此工作定義為相依關係。換言之，請將 {{site.data.keyword.cloudant_short_notm}} 放在 package.json 相依關係中。從指令行使用 [npm](https://nodejs.org/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 套件管理程式來安裝 SDK：
```
npm install --save @cloudant/cloudant
```
{: codeblock}

請注意，您的 `package.json` 檔案現在會顯示 Cloudant 套件。

## 步驟 3. 起始設定 SDK
{: #initialize-cloudant}

在應用程式中起始設定 SDK 之後，您可以使用 {{site.data.keyword.cloudant_short_notm}} 來儲存資料。若要起始設定連線，請輸入您的認證，並在一切就緒時提供回呼函數。

1. 將下列 `require` 定義新增至 `server.js` 檔案，以載入用戶端程式庫。
  ```js
  var Cloudant = require('@cloudant/cloudant');
  ```
  {: codeblock}

2. 提供認證，以起始設定用戶端程式庫。使用 `iamauth` 外掛程式，以 IAM API 金鑰建立資料庫用戶端。 
  ```js
  var cloudant = new Cloudant({ url: 'https://examples.cloudant.com', plugins: { iamauth: { iamApiKey: 'xxxxxxxxxx' } } });
  ```
  {: codeblock}

3. 將下列程式碼新增至 `server.js` 檔案，以列出資料庫。
  ```javascript
  cloudant.db.list(function(err, body) {
  body.forEach(function(db) {
    console.log(db);
    });
  });
  ```
  {: codeblock}

大寫 `Cloudant` 是您使用 `require()` 載入的套件。小寫 `cloudant` 是 {{site.data.keyword.cloudant_short_notm}} 服務的已鑑別連線。
{: tip}

### 使用基本作業管理資料
{: #basic_operations-cloudant}
<!--Borrowed from https://github.com/cloudant/nodejs-cloudant/blob/master/example/crud.js-->

這些基本作業說明使用已起始設定的用戶端來建立、讀取、更新及刪除文件的動作。

#### 建立文件
```js
var createDocument = function(callback) {
  console.log("Creating document 'mydoc'");
  // specify the id of the document so you can update and delete it later
  db.insert({ _id: 'mydoc', a: 1, b: 'two' }, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

#### 讀取文件
```js
var readDocument = function(callback) {
  console.log("Reading document 'mydoc'");
  db.get('mydoc', function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep a copy of the doc so you know its revision token
    doc = data;
    callback(err, data);
  });
};
```
{: codeblock}

#### 更新文件
```js
var updateDocument = function(callback) {
  console.log("Updating document 'mydoc'");
  // make a change to the document, using the copy we kept from reading it back
  doc.c = true;
  db.insert(doc, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep the revision of the update so we can delete it
    doc._rev = data.rev;
    callback(err, data);
  });
};
```
{: codeblock}

#### 刪除文件
```js
var deleteDocument = function(callback) {
  console.log("Deleting document 'mydoc'");
  // supply the id and revision to be deleted
  db.destroy(doc._id, doc._rev, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

## 步驟 4. 測試應用程式
{: #test-cloudant}

所有項目都正確設定嗎？請測試看看！

1. 執行應用程式，確定啟動起始設定及個別作業，例如，建立文件。
2. 從 [{{site.data.keyword.cloud_notm}} 儀表板](https://cloud.ibm.com/dashboard/apps){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中，按一下您先前建立的 {{site.data.keyword.cloudant_short_notm}} 服務實例。服務實例開啟時，請按一下**啟動 Cloudant 儀表板**。
3. 在「{{site.data.keyword.cloudant_short_notm}} 儀表板」中，選取您已在其中建立新文件的資料庫。

有困難嗎？請參閱 [{{site.data.keyword.cloudant_short_notm}} API 參考資料](/docs/services/Cloudant?topic=cloudant-api-reference-overview)。

## 後續步驟
{: #next-cloudant notoc}

做得好！您已為應用程式新增一個安全持續性等級。嘗試下列其中一個選項，以保持動力：

* 檢視 [{{site.data.keyword.cloudant_short_notm}} SDK for Node.js](https://github.com/cloudant/nodejs-cloudant){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 原始碼。
* 請參閱[資料庫及文件作業範例程式碼](https://github.com/cloudant/nodejs-cloudant/tree/master/example){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
* 「入門範本套件」是使用 {{site.data.keyword.cloud}} 功能最快的方式之一。請檢視[行動開發人員儀表板 ](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中的可用入門範本套件。下載程式碼。執行應用程式！
* 若要進一步瞭解並充分運用 {{site.data.keyword.cloudant_short_notm}} 提供的所有特性，[請參閱文件](/docs/services/Cloudant?topic=cloudant-getting-started-with-cloudant#getting-started-with-cloudant)。
