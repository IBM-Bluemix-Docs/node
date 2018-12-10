---

copyright:
  years: 2018
lastupdated: "2018-10-18"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 部署及整合 Node.js 應用程式
{: #deploy_apps}

您可以使用工具鏈或使用指令行介面，來部署您的 Node.js 應用程式。工具鏈是一組工具整合。指令行介面是部署應用程式和服務實例的簡單方法。

如需相關資訊，請參閱[部署應用程式](../apps/dep-app-tool.html)。

## 開發無伺服器 Node.js 應用程式
{: #serverless}

為了協助無伺服器開發，您可以使用 IBM 的「函數即服務 (FaaS)」供應項目 {{site.data.keyword.openwhisk}}。{{site.data.keyword.openwhisk_short}} 可執行應用程式邏輯，以回應事件，或回應來自 Web 或行動應用程式透過 HTTP 的直接呼叫，而不需佈建或管理伺服器。

{{site.data.keyword.openwhisk_short}} 會執行系統管理，例如，自動調整、可用性管理及維護，讓開發人員專注在撰寫應用程式邏輯。

如需相關資訊，請參閱[開發無伺服器應用程式](../apps/deploying/functions.html)。

## 使用產生的 SDK 來整合後端服務
{: #backend_gensdk}

「{{site.data.keyword.IBM_notm}} SDK 產生器」外掛程式會使用產生的 SDK，輕鬆地將後端服務整合至您的應用程式。當 REST API 發生變更時，您可以重新產生 SDK 並取代舊的 SDK，以進行 SDK 無縫升級。您也可以將 CLI 整合到 DevOps 管線，並確保 SDK 在每次建置應用程式時一律與 API 規格保持一致。

如需相關資訊，請參閱[使用產生的 SDK 來整合後端服務](/docs/swift/backend/cli_sdkgen.html)。

## 部署至 Kubernetes 叢集
{: #deploy_kube}

您可以瞭解如何使用 {{site.data.keyword.cloud_notm}} Kubernetes Service 來部署使用 Watson Tone Analyzer 的容器化 Node.js 應用程式。在提供的情境中，虛構的公關公司使用 {{site.data.keyword.cloud_notm}} 服務來分析其新聞稿，並收到對其訊息基調的意見。如需相關資訊，請參閱[將應用程式部署至叢集](../containers/cs_tutorials_apps.html)指導教學。

## 部署至虛擬伺服器
{: #virtual_deploy}

虛擬服務針對所有工作負載類型，提供較高程度的透通性、可預測性及自動化。Terraform 用來有效率且安全地建置、變更及版本化您的基礎架構。

如需相關資訊，請參閱[部署至虛擬伺服器](../apps/vsi-deploy.html)。
