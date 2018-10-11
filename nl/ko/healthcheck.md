---

copyright:
  years: 2018
lastupdated: "2018-09-18"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js 앱에서 상태 검사 사용
{: #healthcheck}

상태 검사는 서버 측 애플리케이션이 올바르게 작동하는지 여부를 판별할 수 있는 단순 메커니즘을 제공합니다. 많은 배치 환경([Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) 및 [Kubernetes](https://www.ibm.com/cloud/container-service) 등)은 서비스의 인스턴스가 트래픽을 수신할 준비가 되어 있는지 판별하기 위해 상태 엔드포인트를 주기적으로 폴링하도록 구성될 수 있습니다. 

## 상태 검사 개요
{: #overview}

상태 검사는 일반적으로 `HTTP`를 통해 액세스되며 `UP` 또는 `DOWN` 상태를 표시하는 데 표준 리턴 코드를 사용합니다. 예를 들어, `UP`의 경우 `200`을 리턴하고 `DOWN`의 경우 `5xx`를 리턴합니다. 예를 들면, `503` 리턴 코드는 애플리케이션이 요청을 처리할 수 없는 경우에 사용되고, `500`은 서버에서 오류 상태가 발생하는 경우 사용됩니다. 상태 검사의 리턴값은 변수이지만, `{“status”: “UP”}`과 같은 최소한의 JSON 응답은 일관성을 제공합니다. 

Kubernetes는 [라이브니스](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) 및 [준비](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) 프로브를 둘 다 정의합니다. 

* 라이브니스 프로브는 애플리케이션이 프로세스의 다시 시작 가능 여부를 표시할 수 있게 해 줍니다. 여기에는 이에 해당하는 고려사항이 적용됩니다. 예를 들면, 메모리 임계값에 도달하는 경우 `liveness` 확인이 실패할 수 있습니다. 앱이 Kubernetes에서 실행되는 경우에는 프로세스가 필요한 경우 확실히 다시 시작할 수 있도록 `liveness` 엔드포인트를 추가하는 것을 고려하십시오. 

* 준비 프로브는 자동 라우팅 의사결정에 사용됩니다. 성공 또는 실패는 애플리케이션이 새 작업을 수신할 수 있는지를 나타냅니다. 이 엔드포인트는 필수 다운스트림 서비스에 대한 고려사항을 포함합니다. 시스템의 전체 로드를 최소화하기 위해 다운스트림 서비스 확인의 결과를 캐싱하는 것을 고려하십시오(예: 1초에 최대 한 번 데이터베이스 연결을 테스트). 

Cloud Foundry는 하나의 상태 엔드포인트만 사용합니다. 이 확인이 실패하는 경우 Cloud Foundry는 프로세스를 다시 시작합니다. 그러나 성공하는 경우 Cloud Foundry는 프로세스가 새 작업을 처리할 수 있다고 가정합니다. 이 상태 검사는 하나의 엔드포인트가 라이브니스 프로브와 준비 프로브가 조합된 것과 같은 역할을 수행하도록 합니다. 

## 기존 Node.js 앱에 상태 검사 추가
{: #add-healthcheck-existing}

기존 앱에 상태 검사 엔드포인트를 추가하려면 먼저 다음 예에 표시되어 있는 바와 같이 새 라우트를 도입하십시오. 
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

여기서 의미 있는 기준을 추가하면 `/health` 엔드포인트의 응답이 서비스가 요청을 처리할 준비가 되었음을 올바르게 표시합니다. 

## Node.js 스타터 킷 앱에서 상태 검사 액세스
{: #healthcheck-starterkit}

기본적으로 스타터 킷을 사용하여 Node.js 앱을 생성하면 `/health`에서 앱의 상태(UP/DOWN)를 확인할 수 있는 기본(권한 없는) 상태 검사 엔드포인트가 사용 가능합니다. 

이 상태 검사 엔드포인트 코드는 다음 `/server/routers/health.js` 파일에 의해 제공됩니다. 
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

애플리케이션이 트래픽을 수신할 준비가 된 경우에만 성공적으로 리턴하도록 이 확인을 사용자 정의할 수 있습니다.
{: tip}
