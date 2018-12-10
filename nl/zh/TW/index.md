---

copyright:
  years: 2018
lastupdated: "2018-10-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 入門指導教學

下列指導教學會引導您完成建置、在本端執行以及使用 {{site.data.keyword.cloud_notm}} 提供的工具來部署 Node.js 應用程式的步驟。您可以在指令行或 Web 型 [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://console.bluemix.net/developer/appservice/dashboard) 中使用 [{{site.data.keyword.dev_cli_long}}](https://console.bluemix.net/docs/cloudnative/dev_cli.html#add-cli)，如下列指導教學步驟所示。使用上述任一種方法，只要幾分鐘即可產生可正式作業的 Node.js 應用程式。

請確定您使用最新的 Node.js LTS 版本。

## 建立 Node.js 應用程式
{: #create_project}

1. 從 {{site.data.keyword.dev_console}} 的[入門範本套件](https://console.bluemix.net/developer/appservice/starter-kits)頁面中，選取以 `Node.js` 撰寫的「入門範本套件」。您也可以藉由按一下**建立應用程式**，選取 `Node.js` 作為語言，來建立空白入門範本應用程式。

    您必須登入 {{site.data.keyword.cloud_notm}} 帳戶，才能建立專案。如果您沒有帳戶，則可以[登錄免費帳戶](https://console.bluemix.net/registration)。
    {: tip}

2. 按一下**建立應用程式**。
3. 建立應用程式的**名稱**。如果您要使用，則會提供一個一般應用程式名稱。
4. 輸入**唯一主機名稱**。主機名稱用來存取您的應用程式，例如：`expressjs-project.mybluemix.net`。
5. 按一下**建立**。建立專案之後，您可以使用工具鏈來部署，或者從指令行繼續建置及部署專案。
6. 如果您選擇建立工具鏈，請按一下**部署至雲端**，然後選取下列其中一種部署方法。
    * **Cloud Foundry 應用程式** - 您不需要管理基礎架構。
    * **Kubernetes 叢集** - 您必須佈建一組工作者節點。例如，您可以使用 VM 來部署及管理高可用性應用程式容器。您可以建立叢集或部署至現有的叢集。

7. 最後確定您的選項，然後按一下**建立**來建立工具鏈。

8. 如果您選擇繼續使用 CLI，而非工具鏈，請將專案下載至本端機器，將它 `unzip`，然後 `cd` 至根目錄。現在，您可以使用平台指令方法來安裝必備項目：

    MacOS：
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows（以系統管理員身分執行）：
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## 新增服務
{: #add_service}

1. 回到 {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} 中的專案。
2. 按一下**新增服務**，選取您要新增的服務種類，按**下一步**，然後選擇您的服務。例如，若要將 NoSQL 資料庫新增至應用程式，請按一下**資料**種類，然後選取 **Cloudant**，以提供精簡方案進行免費開發。根據選取的方案，{{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} 會為您佈建服務。
附註：如果您先前已佈建計劃要使用的服務，請選擇**現有**種類。
3. 佈建服務之後，請按一下**下載程式碼**，以使用連接至服務的 SDK 來重新產生專案。

<!--
<video of creating a project and adding a service>
-->

## 在本端執行應用程式
{: #run_local}

1. 使用 `ibmcloud dev build` 指令來建置應用程式。
2. 使用 `ibmcloud dev run` 指令，在本端執行應用程式。您的應用程式是在本端系統的 Docker 容器中執行。您可以在瀏覽器中存取 `http://localhost:3000`，以測試應用程式。
3. 性能檢查端點位於 `http://localhost:3000/health`。
4. 您可以存取位於 `http://localhost:3000/appmetrics-dash` 的 [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/) 儀表板。

<!--
<video>
-->

## 部署至雲端
{: #deploy_cloud}

使用 `ibmcloud dev deploy` 指令，以部署至 {{site.data.keyword.cloud_notm}} 作為 Cloud Foundry 應用程式。 

若要部署至 {{site.data.keyword.cloud_notm}} 中的 IBM Container Services，請使用下列指令：
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## 後續步驟
{: #nextsteps}

請繼續參閱 Node.js 程式設計手冊中的各主題，或者，如需其他進階部署，您可以瞭解如何建立 Kubernetes 叢集，並在其中部署 Node.js 應用程式。

### 設定 Kubernetes 叢集
如需在 {{site.data.keyword.cloud_notm}} 中設定 Kubernetes 叢集的相關資訊，請參閱[指導教學步驟](https://console.bluemix.net/docs/containers/cs_clusters.html#clusters)。

### 將 Node.js 應用程式部署至 Kubernetes 叢集
瞭解如何[將 Node.js 應用程式部署至 Kubernetes 叢集](../containers/cs_tutorials_apps.html)。
