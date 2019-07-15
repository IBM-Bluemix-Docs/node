---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-13"

keywords: deploy nodejs app, serverless nodejs, back-end nodejs, generated sdk nodejs, cloud foundry deploy nodejs, kubernetes deploy nodejs, virtual nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Node.js 앱 배치 및 통합
{: #deploy_apps-nodejs}

도구 체인 또는 명령행 인터페이스를 사용하여 Node.js 앱을 배치할 수 있습니다. 도구 체인은 도구 통합 세트입니다. 명령행 인터페이스는 앱 및 서비스 인스턴스를 배치하는 간단한 방법입니다.

자세한 정보는 [앱 배치](/docs/apps?topic=creating-apps-deploying-apps)를 참조하십시오.

## 서버리스 Node.js 앱 개발
{: #serverless-nodejs}

서버리스 개발을 용이하게 하려는 경우에는 IBM FaaS(Functions as a Service) 오퍼링인 {{site.data.keyword.openwhisk}}를 사용할 수 있습니다. {{site.data.keyword.openwhisk_short}}는 웹 또는 모바일 앱이 서버를 프로비저닝하거나 관리하지 않고 이벤트 또는 직접 호출에 대한 응답으로서 HTTP를 통해 애플리케이션 로직을 실행할 수 있도록 합니다.

{{site.data.keyword.openwhisk_short}}는 개발자가 애플리케이션 로직 작성에 계속 집중할 수 있도록 Auto-Scaling, 가용성 관리 및 유지보수와 같은 시스템 관리를 수행합니다.

자세한 정보는 [서버리스 앱 개발](/docs/apps/deploying?topic=creating-apps-serverless)을 참조하십시오.

## 생성된 SDK를 사용하여 백엔드 서비스 통합
{: #backend_gensdk}

{{site.data.keyword.IBM_notm}} SDK Generator 플러그인은 생성된 SDK를 사용하여 백엔드 서비스를 앱과 손쉽게 통합합니다. REST API가 변경되면 사용자는 SDK를 다시 생성하고, 원활한 SDK 업그레이드를 위해 이전 SDK를 대체할 수 있습니다. 또한 CLI를 DevOps 파이프라인과 통합하고, 앱이 빌드될 때마다 SDK가 항상 API 스펙과 일치하는지 확인할 수 있습니다.

자세한 정보는 [생성된 SDK를 사용하여 백엔드 서비스 통합](/docs/swift/backend?topic=swift-sdkgen-cli)을 참조하십시오.

## Kubernetes 클러스터에 배치
{: #deploy_kube}

{{site.data.keyword.cloud_notm}} Kubernetes Service를 사용하여 Watson Tone Analyzer를 사용하는 컨테이너화된 Node.js 앱을 배치하는 방법을 알아볼 수 있습니다. 제공된 시나리오에서 가상의 PR 회사는 {{site.data.keyword.cloud_notm}} 서비스를 사용하여 보도 자료를 분석하고 자사 메시지의 어조에 대한 피드백을 받습니다. 자세한 정보는 [Kubernetes 클러스터에 앱 배치](/docs/containers?topic=containers-cs_apps_tutorial) 튜토리얼을 참조하십시오.

## Cloud Foundry에 배치
{: #node-deploy-cf}

이 옵션은 기본 인프라를 관리하지 않고도 클라우드 고유 앱을 배치합니다.

[{{site.data.keyword.cfee_full}}](/docs/cloud-foundry?topic=cloud-foundry-about)에 앱을 배치하려면 [{{site.data.keyword.cloud_notm}} 계정을 준비](/docs/cloud-foundry?topic=cloud-foundry-prepare)해야 합니다.

계정에 {{site.data.keyword.cfee_full_notm}}에 대한 액세스 권한이 있는 경우, **[퍼블릭 클라우드](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** 또는 **[엔터프라이즈 환경](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)**의 배치자 유형을 선택할 수 있으며, 이는 사용자 엔터프라이즈에서 독점적으로 Cloud Foundry 애플리케이션을 호스팅하기 위한 격리 환경을 작성하고 관리하는 데 사용할 수 있습니다.

## 가상 서버에 배치
{: #virtual_deploy}

가상 서버 인스턴스(VSI)는 모든 워크로드 유형에 대해 보다 높은 수준의 투명성, 예측 가능성 및 자동화를 제공합니다. Terraform은 인프라를 안전하고 효율적으로 빌드, 변경 및 버전화하는 데 사용됩니다.

  VSI 배치는 일부 스타터 킷에 사용할 수 있습니다. 이 기능을 사용하려면 [{{site.data.keyword.cloud_notm}} 대시보드](https://{DomainName})로 이동하여 **앱** 타일에서 **앱 작성**을 클릭하십시오.
  {: note} 

자세한 정보는 [가상 서버에 배치](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server)를 참조하십시오.
