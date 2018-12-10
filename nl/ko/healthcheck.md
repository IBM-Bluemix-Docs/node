---

copyright:
  years: 2018
lastupdated: "2018-10-17"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js 앱에서 상태 검사 사용
{: #healthcheck}

상태 검사는 서버 측 애플리케이션이 올바르게 작동하는지 여부를 판별할 수 있는 단순 메커니즘을 제공합니다. [Kubernetes](https://www.ibm.com/cloud/container-service) 및 [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry)와 같은 클라우드 환경은 서비스의 인스턴스가 트래픽을 수신할 준비가 되어 있는지 판별하기 위해 상태 엔드포인트를 주기적으로 폴링하도록 구성될 수 있습니다.

## 상태 검사 개요
{: #overview}

상태 검사는 서버 측 애플리케이션이 올바르게 작동하는지 여부를 판별할 수 있는 단순 메커니즘을 제공합니다. 일반적으로 HTTP를 통해 이용되고 UP 또는 DOWN 상태를 표시하는 데 표준 리턴 코드를 사용합니다. 상태 검사의 리턴값은 변수이지만, `{"status": "UP"}`과 같은 최소한의 JSON 응답이 일반적입니다. 

Kubernetes에는 프로세스 상태의 미묘한 개념이 있습니다. 다음과 같이 두 개의 프로브를 정의합니다.

- _**준비**_ 프로브는 프로세스가 요청을 처리할 수 있는지 여부를 표시하는 데 사용합니다(라우트 가능함). 

  Kubernetes는 준비 프로브가 실패한 상태로 컨테이너에 대한 작업을 라우트하지 않습니다. 준비 프로브는 서비스의 초기화가 완료되지 않는 경우 또는 서비스가 사용 중인거나, 과부화되거나, 요청을 처리할 수 없는 경우 실패할 수 있습니다.

- _**라이브니스**_ 프로브는 프로세스의 다시 시작 여부를 표시하는 데 사용됩니다.

  Kubernetes는 `liveness` 프로브가 실패한 상태로 컨테이너를 중지하고 다시 시작합니다. 예를 들어, 메모리가 소진되거나 컨테이너가 내부 프로세스 손상 후 올바르게 중지되지 않은 경우 라이브니스 프로브를 사용하여 복구 불가능한 상태의 프로세스를 정리하십시오. 

비교를 위한 참고사항으로 Cloud Foundry는 하나의 상태 엔드포인트를 사용합니다. 이 상태 검사에 실패하는 경우 프로세스가 다시 시작되지만 성공하는 경우 요청이 프로세스로 라우트됩니다. 이 환경에서 프로세스가 라이브 상태일 때 엔드포인트가 최소한으로 성공합니다. 초기화 지연은 서비스가 다시 시작 주기를 실행하지 않기 위해 초기화를 완료할 때까지 상태 검사를 지연시키도록 구성됩니다. 

다음 표는 준비, 라이브니스 및 단일 상태 엔드포인트가 제공할 응답에 대한 지시사항을 제공합니다. 

|상태    | 준비                        | 라이브니스                 | 성능 상태                    |
|----------|-----------------------------|----------------------------|---------------------------|
|          | 확인 상태가 아니면 로드되지 않음 | 확인 상태가 아니면 다시 시작되지 않음 | 확인 상태가 아니면 다시 시작되지 않음 |
| 시작 중  | 503 - 사용 불가능           | 200 - 확인                 | 테스트하지 않으려면 지연을 사용함 |
| 작동     | 200 - 확인                  | 200 - 확인                 | 200 - 확인                |
| 중지 중  | 503 - 사용 불가능           | 200 - 확인                 | 503 - 사용 불가능         |
| 작동 중단 | 503 - 사용 불가능           | 503 - 사용 불가능          | 503 - 사용 불가능         |
| 오류 발생| 500 - 서버 오류             | 500 - 서버 오류            | 500 - 서버 오류           |

## 기존 Node.js 앱에 상태 검사 추가
{: #add-healthcheck-existing}

다음 예에 표시되어 있는 바와 같이 새 라우트를 도입하여 기존 애플리케이션에 최소 상태 또는 라이브니스 확인을 추가하십시오.
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

`/health` 엔드포인트에 액세스하여 브라우저에서 앱의 상태를 확인하십시오.

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

## 준비 및 라이브니스 프로브에 대한 권장사항

준비 프로브는 다운스트림 서비스가 사용 불가능한 경우 허용 가능한 폴백이 없을 때 결과에서 다운스트림 서비스에 대한 연결의 실행 가능성을 포함할 수 있습니다. 이는 인프라가 사용자를 위해 확인함에 따라 다운스트림 서비스에서 직접 제공되는 상태 검사의 호출을 의미하지는 않습니다. 대신 애플리케이션에 있는 다운스트림 서비스에 대한 기존 참조의 상태 확인을 고려하십시오. 이는 WebSphere MQ에 대한 JMS 연결 또는 초기화된 Kafka 이용자 또는 생성자일 수 있습니다. 다운스트림 서비스에 대한 내부 참조의 실행 가능성을 확인하는 경우 애플리케이션에 있는 영향 상태 검사를 최소화하도록 결과를 캐시하십시오. 

반대로 라이브니스 프로브는 실패 시 프로세스가 즉각적으로 종료되므로 확인 항목에 대해 신중합니다. 상태 코드 `200`과 함께 `{"status": "UP"}`을 항상 리턴하는 단순 http 엔드포인트를 선택하는 것은 적절합니다. 

### Kubernetes 준비 및 라이브니스에 대한 지원 추가

[CloudNativeJS]의 [`cloud-health-connect`](https://github.com/CloudNativeJS/cloud-health-connect) 라이브러리는 각 엔드포인트의 상태에 대한 소스의 구성을 허용하는 노드에서 별도의 라이브니스 및 준비 엔드포인트를 정의하기 위해 프레임워크를 제공합니다.

## Kubernetes에서 준비 및 라이브니스 프로브 구성

Kubernetes 배치 옆에 라이브니스 및 준비 프로브를 선언하십시오. 두 프로브는 동일한 구성 매개변수를 사용합니다. 

* kubelet은 첫 번째 프로브 전에 `initialDelaySeconds`를 기다립니다. 

* kubelet은 `periodSeconds`초마다 서비스를 프로브합니다. 기본값은 1입니다.

* 프로브는 `timeoutSeconds`초 후에 제한시간을 초과합니다. 기본값 및 최소값은 1입니다. 

* 프로브는 실패 후 `successThreshold`번 성공하면 성공 상태가 됩니다. 기본값 및 최소값은 1입니다. 라이브니스 프로브의 값은 1이어야 합니다.

* 팟(Pod)이 시작되고 프로브가 실패하는 경우 Kubernetes는 다시 시작을 `failureThreshold`번 시도한 후 포기합니다. 최소값은 1이고 기본값은 3입니다.
    - 라이브니스 프로브의 경우 "포기"는 팟(Pod)의 다시 시작을 의미합니다.
    - 준비 프로브의 경우 "포기"는 팟(Pod) `Unready` 표시를 의미합니다.

주기를 다시 시작하지 않으려면 서비스를 초기화하는 데 소요되는 시간보다 길게 `livenessProbe.initialDelaySeconds`를 설정하십시오. 그런 다음 준비되는 즉시 `readinessProbe.initialDelaySeconds`에 대한 더 짧은 값을 사용하여 요청을 서비스로 라우트할 수 있습니다. 

다음 구성 예제를 참조하십시오. 
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```
{: codeblock}

자세한 정보는 [라이브니스 및 준비 프로브 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) 방법을 참조하십시오.
