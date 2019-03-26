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

# Node.js 환경 구성
{: #configure-nodejs}

클라우드 기반 원리를 구현하면 Node.js 애플리케이션이 코드를 변경하거나 테스트되지 않은 코드 경로를 사용하지 않으면서 한 환경에서 다른 환경으로, 테스트에서 프로덕션으로 이동할 수 있습니다.

개발 환경에 따라, 구성이 표시되는 방식에 상당한 차이가 있는 경우에는 문제점이 발생합니다. 예를 들면, 문자열화된 JSON 오브젝트를 사용하는 Cloud Foundry와 단순 값 또는 문자열화된 JSON 오브젝트를 사용하는 Kubernetes 간의 관계와 같습니다. Kubernetes를 제외하면 로컬 개발 또한 여러 고려사항이 있습니다. 인증 정보 또한 공용 환경과 개인용 환경 간에 다르게 표시될 수 있으며, 이는 앱이 여러 환경 간에 변경되지 않은 상태를 유지하는 것을 더욱 어렵게 합니다.

기존 애플리케이션에 {{site.data.keyword.cloud}} 지원을 추가해야 하든, 또는 스타터 킷을 사용하여 앱을 작성해야 하든, 목표는 모든 개발 플랫폼에서 Node.js 앱에 이식성을 제공하는 것입니다.

## 기존 Node.js 애플리케이션에 {{site.data.keyword.cloud_notm}} 구성 추가
{: #addcloud-env-nodejs}

[`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env) 모듈은 애플리케이션이 환경에 대해 독립적일 수 있도록 하기 위해 다양한 클라우드 제공자(Cloud Foundry 및 Kubernetes 등)로부터 환경 변수를 수집합니다.

### `ibm-cloud-env` 모듈 설치
{: #install-module-nodejs}

1. 다음 명령을 사용하여 `ibm-cloud-env` 모듈을 설치하십시오.
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. 다음과 같이 `mappings.json`을 참조하여 코드 내의 모듈을 초기화하십시오.
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  맵핑 파일 경로가 `IBMCloudEnv.init()`에 지정되지 않은 경우 모듈은 기본 경로인 `/server/config/mappings.json`에서 맵핑을 로드하려 시도합니다.
  {: tip}

  `mappings.json` 파일 예:
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

### Node.js 앱 내의 값 사용
{: #values-nodejs}

다음 명령을 사용하여 애플리케이션 내의 값을 검색하십시오.

1. 변수 `service1credentials`를 검색하십시오.
  ```js
  // this will be a dictionary
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. 변수 `service2username`을 검색하십시오.
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // this will be a string
  ```
  {: codeblock}

이제 여러 클라우드 컴퓨팅 제공자가 도입한 차이점을 생략하여 애플리케이션을 임의의 런타임 환경에 구현할 수 있습니다.

### 태그 및 레이블의 값 필터링
{: #filter-values-nodejs}

다음 예에 표시되어 있는 바와 같이 서비스 태그 및 서비스 레이블을 기반으로 모듈이 생성한 인증 정보를 필터링할 수 있습니다.
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // returns a Json with credentials for specified service tag and label
```
{: codeblock}

## 스타터 킷 앱에서 Node.js 구성 관리자 사용
{: #nodejs-config-skit}

[스타터 킷](https://cloud.ibm.com/developer/appservice/starter-kits/)을 사용하여 작성된 Node.js 앱에는 많은 클라우드 배치 환경(CF, K8s, VSI 및 Functions)에서 실행되는 데 필요한 인증 정보 및 구성이 자동으로 제공됩니다.

### 서비스 인증 정보 이해
{: #credentials-nodejs}

서비스의 애플리케이션 구성 정보는 `/server/config` 디렉토리의 `localdev-config.json` 파일에 저장됩니다. 이 파일은 민감한 정보가 Git에 저장되는 것을 방지하기 위해 `.gitignore` 디렉토리에 있습니다. 로컬에서 실행되는 모든 구성된 서비스에 대한 연결 정보(사용자 이름, 비밀번호, 호스트 이름 등)는 이 파일에 저장됩니다.

애플리케이션은 환경 및 이 파일에서 연결 및 구성 정보를 읽는 데 구성 관리자를 사용합니다. 이는 사용자 정의 빌드된 `mappings.json`(`server/config` 디렉토리에 있음)을 사용하여 각 서비스의 인증 정보를 찾을 수 있는 위치와 통신합니다.

로컬에서 실행 중인 애플리케이션은 `mappings.json` 파일에서 읽는 바인드되지 않은 인증 정보를 사용하여 {{site.data.keyword.cloud_notm}} 서비스에 연결할 수 있습니다. 바인드되지 않은 인증 정보를 작성해야 하는 경우에는 {{site.data.keyword.cloud_notm}} 웹 콘솔에서, 또는 [Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/) `cf create-service-key` 명령을 사용하여 이를 작성할 수 있습니다.

애플리케이션을 {{site.data.keyword.cloud_notm}}에 푸시하면 이러한 값이 더 이상 필요하지 않습니다. 대신 애플리케이션은 환경 변수를 사용하여 바인드된 서비스에 자동으로 연결합니다.

* **Cloud Foundry**: `VCAP_SERVICES` 환경 변수에서 서비스 인증 정보를 가져옵니다. Cloud Foundry Enterprise Edition의 경우 자세한 정보는 이 [시작하기 튜토리얼](/docs/cloud-foundry/getting-started.html#getting-started)을 참조하십시오.

* **Kubernetes**: 서비스별 개별 환경 변수에서 서비스 인증 정보를 가져옵니다.

* **{{site.data.keyword.cloud_notm}} Container Service**: VSI 또는 {{site.data.keyword.openwhisk}}(Openwhisk)에서 서비스 인증 정보를 가져옵니다.

## 다음 단계
{: #next_steps-config notoc}

`ibm-cloud-config`는 세 가지 검색 패턴 유형(`cloudfoundry`, `env` 및 `file`)을 사용하여 값을 검색하는 것을 지원합니다. 기타 지원되는 검색 패턴 및 검색 패턴 예를 참조하려면 [Usage](https://github.com/ibm-developer/ibm-cloud-env#usage) 섹션을 참조하십시오.
