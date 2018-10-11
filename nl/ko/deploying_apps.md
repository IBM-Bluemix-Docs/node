---

copyright:
  years: 2018
lastupdated: "2018-08-15"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js 앱 배치 및 통합
{: #deploy_apps}

도구 체인 또는 명령행 인터페이스를 사용하여 Node.js 앱을 배치할 수 있습니다. 도구 체인은 도구 통합 세트입니다. 명령행 인터페이스는 앱 및 서비스 인스턴스를 배치하는 간단한 방법입니다. 

자세한 정보는 [앱 배치](../apps/dep-app-tool.html)를 참조하십시오. 

## 서버리스 Node.js 앱 개발
{: #serverless}

서버리스 개발을 용이하게 하려는 경우에는 IBM FaaS(Functions as a Service) 오퍼링인 {{site.data.keyword.openwhisk}}를 사용할 수 있습니다. {{site.data.keyword.openwhisk_short}}는 웹 또는 모바일 앱이 서버를 프로비저닝하거나 관리하지 않고 이벤트 또는 직접 호출에 대한 응답으로서 HTTP를 통해 애플리케이션 로직을 실행할 수 있도록 합니다. 

{{site.data.keyword.openwhisk_short}}는 자동 스케일링 기능, 가용성 관리, 유지보수와 같은 시스템 관리를 수행하여 개발자가 애플리케이션 로직을 작성하는 데 집중할 수 있게 해 줍니다. 

자세한 정보는 [서버리스 앱 개발](../apps/deploying/functions.html)을 참조하십시오. 

## 백엔드 서비스를 생성된 SDK와 통합
{: #backend_gensdk}

{{site.data.keyword.IBM_notm}} SDK Generator 플러그인은 생성된 SDK를 사용하여 백엔드 서비스를 앱과 손쉽게 통합합니다. REST API가 변경되면 사용자는 SDK를 다시 생성하고, 원활한 SDK 업그레이드를 위해 이전 SDK를 대체할 수 있습니다. 또한 CLI를 DevOps 파이프라인과 통합하여 SDK가 앱이 빌드될 때마다 항상 API 스펙과 일치하도록 할 수도 있습니다. 

자세한 정보는 [백엔드 서비스를 생성된 SDK와 통합](../apps/deploying/api_management.html)을 참조하십시오. 

## Kubernetes 클러스터에 배치
{: #deploy_kube}

{{site.data.keyword.cloud_notm}} Kubernetes Service를 사용하여 Watson Tone Analyzer를 사용하는 컨테이너화된 Node.js 앱을 배치하는 방법을 알아볼 수 있습니다. 제공된 시나리오에서, 가상의 홍보 회사는 {{site.data.keyword.cloud_notm}} 서비스를 사용하여 보도 자료를 분석하고 자사 메시지의 어조에 대한 피드백을 받습니다. 자세한 정보는 [클러스터에 앱 배치](../containers/cs_tutorials_apps.html) 튜토리얼을 참조하십시오. 

## Virtual Server에 배치
{: #virtual_deploy}

가상 서비스는 모든 워크로드 유형에 대해 더 높은 수준의 투명성, 예측 가능성 및 자동화를 제공합니다. Terraform은 인프라를 안전하고 효율적으로 빌드하고, 변경하고, 버전화하는 데 사용됩니다. 

자세한 정보는 [Virtual Server에 배치](../apps/vsi-deploy.html)를 참조하십시오. 
