---

copyright:
  years: 2018
lastupdated: "2018-09-19"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 애플리케이션 메트릭을 Node.js 앱과 함께 사용
{: #metrics}

Node.js 애플리케이션 메트릭을 설치하고 액세스하는 방법과 이에 대한 자세한 정보를 알아보십시오. [Node Application Metrics](https://developer.ibm.com/code/open/projects/node-application-metrics/) 대시보드로 Node.js 앱을 모니터하여 웹 기반 프론트 엔드에서 메트릭을 표시함으로써 Node.js 애플리케이션의 성능을 시각화할 수 있습니다.
{: shortdesc}

## 문제점을 시각적으로 식별
{: #identify-problems}

애플리케이션 메트릭은 애플리케이션의 성능을 모니터하는 데 있어서 중요합니다. CPU, 메모리, 대기 시간 등의 메트릭과 HTTP 메트릭에 대한 실시간 보기를 확보하는 것은 애플리케이션이 시간 경과에 따라 효율적으로 실행되고 있는지 확인하는 데 있어서 필수적입니다. 메트릭에 의존하는 Cloud Foundry의 [Auto-Scaling](/docs/services/Auto-Scaling/index.html)과 같은 클라우드 서비스를 사용하여 현재 워크로드에 맞도록 인스턴스를 동적으로 스케일링할 수 있습니다. 애플리케이션 메트릭을 사용하면 인스턴스의 스케일링 업, 스케일링 다운 또는 정리 시점을 정확하게 파악하여 사용 비용을 적게 유지할 수 있습니다. 

애플리케이션 메트릭은 시계열 데이터로 캡처됩니다. 캡처된 메트릭을 집계하고 시각화하면 다음과 같은 일반적인 성능 문제점을 식별하는 데 도움이 됩니다. 

* 일부 또는 모든 라우트의 느린 HTTP 응답 시간
* 애플리케이션의 낮은 처리량
* 속도를 저하시키는 수요의 급증
* 예상보다 높은 CPU 사용량
* 높거나 증가하는 메모리 사용량(잠재적 메모리 누수)

기본 제공 애플리케이션 메트릭 대시보드([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash))에는 지원되는 데이터베이스(MongoDB, MySQL, Postgres, LevelDB 및 Redis), Socket.IO 및 Riak 이벤트의 데이터베이스 요청 지속 시간을 보여주는 '기타 요청'에 대한 차트도 포함되어 있습니다. 

더 자세한 분석을 위해 이 대시보드에서 노드 보고서 또는 힙 스냅샷을 생성할 수 있습니다. 

## 기존 Node.js 앱에 메트릭 추가
{: #add-appmetrics-existing}

[`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash) 생성자를 사용해 기존 Express 애플리케이션에 모니터링 기능을 추가하여 몇 가지 구성 옵션을 전달하도록 하십시오. 예를 들면, 이러한 옵션 중 하나는 `appmetrics-dash`가 추가 서버를 시작하도록 하는 대신 기존 서버를 사용합니다. 

### 대시보드 설치

1. 예를 들면, 간단한 다음 "Hello World" Express 애플리케이션을 사용하십시오. 
  ```js
  var express = require('express')
  var app = express()
  app.get('/', function (req, res) {
      res.send('Hello World!')
  })
  app.listen(3000, function () {
      console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

2. 다음 [npm](https://nodejs.org/) 명령을 사용하여 `appmetrics` 대시보드를 설치하십시오. 
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. 다음 코드를 추가하여 기존 앱에 `appmetrics-dash` 지원을 추가하십시오. 
  ```js
  var dash = require('appmetrics-dash').attach()
  ```
  {: codeblock}

  이 명령은 `appmetrics-dash`에게 이미 작성된 서버를 사용하고, `appmetrics-dash` 엔드포인트를 추가하도록 지시합니다. 

  개정된 코드는 이제 다음 예와 같습니다. 
  ```js
  var express = require('express')
  var app = express()
  var dash = require('appmetrics-dash').attach()
  app.get('/', function (req, res) {
    res.send('Hello World!')
  })
  app.listen(3000, function () {
    console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

## 스타터 킷에서 메트릭 사용
{: #appmetrics-starterkits}

스타터 킷에서 작성된 Node.js 애플리케이션에는 기본적으로 `appmetrics-dash`가 제공되지만, 해당 대시보드는 사용하도록 설정해야 합니다. 

appmetrics 코드는 `/app_name/server/server.js`라는 생성된 애플리케이션 소스 파일에서 찾을 수 있습니다. 
```js
// Uncomment following to enable zipkin tracing, tailor to fit your network configuration:
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## 대시보드 액세스
{: #access-dashboard}

애플리케이션을 시작한 후 브라우저에서 `http://<hostname>:<port>/appmetrics-dash`로 이동하십시오. 

로컬에서 실행 중인 앱에 대해서는 기본값인 `localhost:3001/appmetrics-dash`를 사용하십시오.
{: tip}

Node.js용 애플리케이션 메트릭 모니터링 대시보드 UI는 동영상 [Monitoring Metrics for Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be)에서 볼 수 있는 바와 같이 HTTP 요청 및 이벤트 루프 대기 시간을 포함한 다양한 메트릭을 제공합니다. 

## 데이터 이해
{: #understanding-data}

![Appmetrics 대시보드](images/appmetricsdash-1.png)

대부분의 데이터는 선 그래프로 표시됩니다. HTTP 수신 요청, HTTP 발신 요청 및 기타 요청은 시간에 대해 이벤트 지속 시간을 표시합니다. HTTP 처리량은 초당 요청 수를 보여줍니다. 평균 응답 시간은 평균적으로 가장 오랜 시간이 소요된, 다섯 개의 가장 많이 사용된 HTTP 요청을 보여줍니다. CPU 및 메모리 그래프는 시간 경과에 따른 시스템 및 프로세스 사용량을 보여줍니다. 힙은 최대 힙 크기와 시간 경과에 따라 사용된 힙 크기를 보여줍니다. 이벤트 루프 대기 시간은 Node.js 이벤트 루프의 간격에서 취한 대기 시간 샘플을 보여주며, 취한 각 샘플에 대해 대기 시간이 가장 짧은 지점, 평균인 지점, 가장 긴 지점을 하나씩 보여줍니다. 

그래프에 점이 있는 경우 이 위로 마우스 커서를 이동하면 추가 정보가 제공됩니다. 예를 들어, `HTTP Incoming Requests`는 응답 시간과 요청된 URL을 보여줍니다. 

모든 그래프에는 최대 15분 분량의 데이터가 표시됩니다. 

모니터되는 애플리케이션에서 대량의 데이터가 생성되는 경우 대시보드는 자동으로 데이터를 표시하기 시작합니다. HTTP Incoming Requests 차트를 다시 보면 각 지점이 2초 기간에 대한 모든 요청을 표시하는 것을 볼 수 있습니다. 다음 도구 팁은 총 요청 수와 소요된 평균 시간, 가장 긴 시간을 보여줍니다. 표시되는 값은 가장 긴 시간입니다. 

![도구 팁 표시](images/tooltip-1.png)




