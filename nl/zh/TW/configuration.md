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

# 配置 Node.js 環境
{: #configure-nodejs}

藉由實作雲端原生原則，Node.js 應用程式可以從某個環境移至另一個環境（從測試環境移至正式作業環境），而不需要變更程式碼或執行未測試過的程式碼路徑。

取決於開發環境，配置呈現方式存在重大差異時，會發生此問題。例如，使用字串化 JSON 物件的 Cloud Foundry 與使用固定值或字串化 JSON 物件的 Kubernetes。除了 Kubernetes 之外，本端開發還有不同的考量。可以在公用及專用中以不同方式呈現認證，這進一步讓應用程式難以在不同環境中保持不變。

不論您需要將 {{site.data.keyword.cloud}} 支援新增至現有應用程式，還是使用「入門範本套件」來建立應用程式，目標都是為任何開發平台上的 Node.js 應用程式提供可攜性。

## 將 {{site.data.keyword.cloud_notm}} 配置新增至現有 Node.js 應用程式
{: #addcloud-env-nodejs}

[`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env) 模組會聚集來自各種雲端提供者（例如 CloudFoundry 及 Kubernetes）的環境變數，使應用程式與環境無關。

### 安裝 `ibm-cloud-env` 模組
{: #install-module-nodejs}

1. 使用下列指令，安裝 `ibm-cloud-env` 模組：
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. 在程式碼中，藉由參照 `mappings.json` 來起始設定模組，如下所示：
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  如果未在 `IBMCloudEnv.init()` 中指定對映檔案路徑，則模組會嘗試從 `/server/config/mappings.json` 預設路徑載入對映。
  {: tip}

  範例 `mappings.json` 檔案：
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

### 使用 Node.js 應用程式中的值
{: #values-nodejs}

使用下列指令，以擷取應用程式中的值。

1. 擷取變數 `service1credentials`：
  ```js
  // this will be a dictionary
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. 擷取變數 `service2username`：
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // this will be a string
  ```
  {: codeblock}

現在，您可以藉由摘要從不同「雲端」運算提供者建立的差異，以在任何運行環境中實作應用程式。

### 過濾標記和標籤的值
{: #filter-values-nodejs}

您可以根據服務標記和服務標籤來過濾模組所產生的認證，如下列範例所示：
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // returns a Json with credentials for specified service tag and label
```
{: codeblock}

## 透過入門範本套件應用程式使用 Node.js 配置管理程式
{: #nodejs-config-skit}

使用[入門範本套件](https://cloud.ibm.com/developer/appservice/starter-kits/)建立的 Node.js 應用程式，會自動隨附在許多雲端部署環境（CF、K8、VSI 及 Functions）中執行所需的認證及配置。

### 瞭解服務認證
{: #credentials-nodejs}

您的服務應用程式配置資訊儲存在 `/server/config` 目錄的 `localdev-config.json` 檔案中。此檔案位於 `.gitignore` 目錄中，可避免將機密性資訊儲存至 Git。在本端執行之任何已配置服務的連線資訊（例如使用者名稱、密碼及主機名稱）都儲存在這個檔案中。

應用程式會使用配置管理程式，從環境及這個檔案中讀取連線及配置資訊。它使用位於 `server/config` 目錄的自訂建置 `mappings.json`，以瞭解可以在哪裡找到每個服務的認證。

本端執行的應用程式可以使用從 `mappings.json` 檔案讀取的未連結認證，來連接至 {{site.data.keyword.cloud_notm}} 服務。如果您需要建立未連結認證，則可以從 {{site.data.keyword.cloud_notm}} Web 主控台或使用 [CloudFoundry CLI](https://docs.cloudfoundry.org/cf-cli/) 的 `cf create-service-key` 指令來執行此作業。

當您將應用程式推送至 {{site.data.keyword.cloud_notm}} 時，不會再使用這些值。相反地，應用程式會使用環境變數自動連接至連結服務。

* **Cloud Foundry**：服務認證取自 `VCAP_SERVICES` 環境變數。若為 Cloud Foundry Enrprise Edition，如需相關資訊，請參閱此[入門指導教學](/docs/cloud-foundry/getting-started.html#getting-started)。

* **Kubernetes**：服務認證取自每個服務的個別環境變數。

* **{{site.data.keyword.cloud_notm}} Container Service**：服務認證取自 VSI 或 {{site.data.keyword.openwhisk}} (Openwhisk)。

## 後續步驟
{: #next_steps-config notoc}

`ibm-cloud-config` 支援使用三種搜尋型樣類型來搜尋值：`cloudfoundry`、`env` 及 `file`。如果您要參閱其他受支援搜尋型樣及搜尋型樣範例，請查看[用法](https://github.com/ibm-developer/ibm-cloud-env#usage)小節。
