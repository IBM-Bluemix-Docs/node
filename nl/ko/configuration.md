---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-13"

keywords: configure node env, node environment, node credentials, ibm-cloud-env node

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Node.js 환경 구성
{: #configure-nodejs}

클라우드 기반 원리를 구현하면 Node.js 애플리케이션이 코드를 변경하거나 테스트되지 않은 코드 경로를 사용하지 않으면서 한 환경에서 다른 환경으로, 테스트에서 프로덕션으로 이동할 수 있습니다.

개발 환경에 따라, 구성이 표시되는 방식에 상당한 차이가 있는 경우에는 문제점이 발생합니다. 예를 들면, 문자열화된 JSON 오브젝트를 사용하는 Cloud Foundry와 단순 값 또는 문자열화된 JSON 오브젝트를 사용하는 Kubernetes 간의 관계와 같습니다. Kubernetes를 제외하면 로컬 개발 또한 여러 고려사항이 있습니다. 인증 정보 또한 공용 환경과 개인용 환경 간에 다르게 표시될 수 있으며, 이는 앱이 여러 환경 간에 변경되지 않은 상태를 유지하는 것을 더욱 어렵게 합니다.

기존 애플리케이션에 {{site.data.keyword.cloud}} 지원을 추가해야 하든, 또는 스타터 킷을 사용하여 앱을 작성해야 하든, 목표는 모든 개발 플랫폼에서 Node.js 앱에 이식성을 제공하는 것입니다.

## 기존 Node.js 애플리케이션에 {{site.data.keyword.cloud_notm}} 구성 추가
{: #addcloud-env-nodejs}

[`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘") 모듈은 다양한 클라우드 제공자(Cloud Foundry 및 Kubernetes 등)로부터 환경 변수를 수집하므로, 애플리케이션이 환경에 독립적입니다.

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

### 서비스 인증 정보 검색
{: #nodejs-get-creds}

다음 명령을 사용하여 애플리케이션 내의 값을 검색하십시오.

1. `service1credentials` 변수를 검색하십시오.
  ```js
  // this will be a dictionary
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. `service2username` 변수를 검색하십시오.
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // this will be a string
  ```
  {: codeblock}

이제 여러 클라우드 컴퓨팅 제공자가 도입한 차이점을 생략하여 애플리케이션을 임의의 런타임 환경에 구현할 수 있습니다.

### 태그 및 레이블의 값 필터링
{: #filter-values-nodejs}

다음 예에 표시된 대로 서비스 태그와 서비스 레이블을 기반으로 모듈에서 생성된 인증 정보를 필터링할 수 있습니다.
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // returns a Json with credentials for specified service tag and label
```
{: codeblock}

## 스타터 킷 앱에서 Node.js 구성 관리자 사용
{: #nodejs-config-skit}

[스타터 킷](https://cloud.ibm.com/developer/appservice/starter-kits){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")을 사용하여 작성된 Node.js 앱에는 여러 클라우드 배치 환경(예: [Kubernetes](/docs/containers?topic=containers-getting-started), [Cloud Foundry](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf), [{{site.data.keyword.cfee_full_notm}}](/docs/cloud-foundry?topic=cloud-foundry-about), [Virtual Server(VSI)](/docs/vsi?topic=virtual-servers-getting-started-tutorial) 또는 [{{site.data.keyword.openwhisk_short}}](/docs/openwhisk?topic=cloud-functions-getting_started))에서 실행하는 데 필요한 인증 정보 및 구성이 자동으로 제공됩니다.

  VSI 배치는 일부 스타터 킷에 사용할 수 있습니다. 이 기능을 사용하려면 [{{site.data.keyword.cloud_notm}} 대시보드](https://{DomainName})로 이동하여 **앱** 타일에서 **앱 작성**을 클릭하십시오.
  {: note} 

### 서비스 인증 정보 이해
{: #credentials-nodejs}

서비스에 대한 애플리케이션 구성 정보는 `/server/config` 디렉토리의 `localdev-config.json` 파일에 저장됩니다. Git에 민감한 정보가 저장되지 않도록 파일이 `.gitignore` 디렉토리에 있습니다. 로컬로 실행되는 구성된 서비스에 대한 연결 정보(예: 사용자 이름, 비밀번호 및 호스트 이름)가 이 파일에 저장됩니다.

애플리케이션이 구성 관리자를 사용하여 환경 및 이 파일의 연결 및 구성 정보를 읽습니다. `server/config` 디렉토리에 있는 사용자 정의 빌드 `mappings.json`을 사용하여 각 서비스에 대해 인증 정보를 찾을 수 있는 위치를 전달합니다.

로컬에서 실행 중인 애플리케이션은 `mappings.json` 파일에서 읽는 바인드되지 않은 인증 정보를 사용하여 {{site.data.keyword.cloud_notm}} 서비스에 연결할 수 있습니다. 바인드되지 않은 인증 정보를 작성해야 하는 경우에는 {{site.data.keyword.cloud_notm}} 웹 콘솔에서, 또는 [Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘") `cf create-service-key` 명령을 사용하여 이를 작성할 수 있습니다.

애플리케이션을 {{site.data.keyword.cloud_notm}}에 푸시하면 이러한 값이 더 이상 사용되지 않습니다. 대신 애플리케이션이 환경 변수를 사용하여 서비스를 바인드하도록 자동으로 연결됩니다.

* **Cloud Foundry**: 서비스 인증 정보는 `VCAP_SERVICES` 환경 변수에서 가져옵니다. Cloud Foundry Enterprise Edition의 경우 자세한 정보는 이 [시작하기 튜토리얼](/docs/cloud-foundry?topic=cloud-foundry-getting-started#getting-started)을 참조하십시오.

* **Kubernetes**: 서비스 인증 정보는 서비스마다 개별 환경 변수에서 가져옵니다.

* **{{site.data.keyword.cloud_notm}} Container Service**: VSI 또는 {{site.data.keyword.openwhisk}}(Openwhisk)에서 서비스 인증 정보를 가져옵니다.

## 다음 단계
{: #next_steps-config notoc}

`ibm-cloud-config`는 세 가지 검색 패턴 유형(`cloudfoundry`, `env` 및 `file`)을 사용하여 값을 검색하는 것을 지원합니다. 기타 지원되는 검색 패턴 및 검색 패턴 예를 참조하려면 [사용법](https://github.com/ibm-developer/ibm-cloud-env#usage){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘") 섹션을 참조하십시오.
