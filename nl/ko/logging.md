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

# Node.js에서의 로깅
{: #logging_nodejs}

로그 메시지는 로그 항목이 작성된 시점의, 마이크로서비스의 상태 및 활동에 대한 컨텍스트 정보를 포함하는 문자열입니다. 로그는 서비스가 어떻게, 그리고 왜 실패했는지 진단하는 데 필요하며, 애플리케이션 상태 모니터링에 있어서 [appmetrics](appmetrics.html)를 지원하는 역할을 수행합니다. 

클라우드 환경에서 프로세스의 임시성으로 인해, 로그는 일반적으로 수집된 후 분석을 위해 준비된 중심 위치로 전송됩니다. 클라우드 환경에서의 가장 일관된 로그 방법은 로그 항목을 표준 출력 및 오류 스트림으로 전송하여 인프라가 나머지를 처리하도록 하는 것입니다. 

## Node.js 앱에 Log4js 지원 추가
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node)는 Node.js에 많이 사용되는 로깅 프레임워크이며 다음과 같은 여러 기본적인 이점을 제공합니다.  
* `stdout` 또는 `stderr`에 로깅
* 다양한 추가 옵션
* 구성 가능한 로그 메시지 레이아웃 및 패턴
* 다양한 로그 카테고리에 대해 로그 레벨 사용

1. Log4js를 사용하려면 애플리케이션의 루트 디렉토리에서 다음 [npm](https://nodejs.org/) 명령을 실행하십시오. 이 명령은 패키지를 설치하고 이를 `package.json` 파일에 추가합니다. 
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. 이를 자신의 애플리케이션에서 사용하려면 다음 코드 행을 추가하십시오. 
  ```javascript
  var log4js = require('log4js');
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **stdout 출력**:
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

어펜더, 로그 레벨 및 구성 세부사항을 사용하여 로그 메시지를 사용자 정의하는 데 대한 자세한 정보는 공식 [log4js-node 문서](https://log4js-node.github.io/log4js-node/)를 참조하십시오. 

## App Service를 사용한 로그 모니터링
{: #monitoring}

{{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard)를 사용하여 작성된 Node.js 앱에는 기본적으로 Log4js가 제공됩니다. 앱을 기본 실행하거나 클라우드 환경에서 실행하면 `2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`과 같은 출력이 생성됩니다. 출력은 다음과 같이 볼 수 있습니다. 
* 로컬에서 실행하는 경우에는 `stdout`을 사용하십시오. 
* [Cloud Foundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) 및 [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) 배치에 대한 로그. 이는 `ibmcloud app logs --recent <APP_NAME>` 및 `kubectl logs <deployment name>`을 통해 액세스됩니다. 

`server/server.js` 파일에서는 다음 코드를 볼 수 있습니다. 
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

기본적으로 로그 레벨은 `INFO`로 설정되며, 애플리케이션의 **LOG_LEVEL** 환경 변수로 대체될 수 있습니다.
{: tip}

## 다음 단계
{: #next_steps notoc}

각 배치 환경에서 로그를 보는 방법에 대해 더 자세히 알아보십시오. 
* [Kubernetes 로그](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cloud Foundry 로그](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} 로그 및 모니터링](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

로그 집계기 사용:
* [{{site.data.keyword.cloud_notm}} Log Analysis](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK 스택](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
