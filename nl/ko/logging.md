---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-04"

keywords: nodejs logging, view logs nodejs, add logging nodejs, log4j nodejs, stdout nodejs, nodejs log, output nodejs, nodejs logger

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js에서의 로깅
{: #logging_nodejs}

로그 메시지는 로그 항목이 작성된 시점의, 마이크로서비스의 상태 및 활동에 대한 컨텍스트 정보를 포함하는 문자열입니다. 로그는 서비스가 어떻게, 그리고 왜 실패했는지 진단하는 데 필요하며, 애플리케이션 상태 모니터링에 있어서 [appmetrics](/docs/node?topic=nodejs-metrics)를 지원하는 역할을 수행합니다.

클라우드 환경에서 프로세스의 임시성으로 인해, 로그는 일반적으로 수집된 후 분석을 위해 준비된 중심 위치로 전송됩니다. 클라우드 환경에서의 가장 일관된 로그 방법은 로그 항목을 표준 출력 및 오류 스트림으로 전송하여 인프라가 나머지를 처리하도록 하는 것입니다.

Node.js에 많이 사용되는 로깅 프레임워크이며 다음과 같은 여러 기본적인 이점을 제공하는 [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")를 사용할 수 있습니다. 
* `stdout` 또는 `stderr`에 로깅
* 다양한 추가 옵션
* 구성 가능한 로그 메시지 레이아웃 및 패턴
* 다양한 로그 카테고리에 대해 `Log Levels` 사용

## 기존 Node.js 앱에 Log4js 지원 추가
{: #add_log4j}

1. 먼저 다음 [npm](https://nodejs.org/){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘") 명령을 실행하여 `log4js`를 설치하십시오. 이 명령은 패키지를 설치하고 이를 `package.json` 파일에 추가합니다.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. 이를 자신의 애플리케이션에서 사용하려면 앱에 다음 코드 행을 추가하십시오.
  ```js
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

3. 표준 오류 시스템에 대한 로그 이벤트를 작성하기 위해 다음 예와 같이 어펜더를 구성할 수 있습니다.
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  어펜더를 사용하여 로그 메시지를 사용자 정의하는 데 대한 자세한 정보는 공식 [log4js-node 문서](https://log4js-node.github.io/log4js-node/){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")를 참조하십시오.

## App Service 앱을 사용한 모니터링
{: #monitoring}

{{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")를 사용하여 작성된 Node.js 앱에는 기본적으로 Log4js가 제공됩니다. `server/server.js` 파일을 열어 다음 Log4js 코드를 볼 수 있습니다.
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

기본적으로 `Log Level`은 라이브러리에서 안전하게 사용될 수 있도록 `OFF`로 설정되지만, 애플리케이션의 **LOG_LEVEL** 환경 변수로 대체될 수 있습니다.
{: tip}

### 로그 출력 보기
{: #node-view-log-output}

앱을 기본 방식으로 실행하거나 클라우드 환경에서 실행하여 다음 샘플 로그 출력을 확인하십시오.
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

다음 방법을 사용하여 로그 출력을 볼 수 있습니다.
* 로컬 환경의 경우 `stdout`을 사용하십시오.
* [Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html) 배치의 경우 다음을 실행하여 로그에 액세스할 수 있습니다.
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) 배치의 경우 다음을 실행하여 로그에 액세스할 수 있습니다.
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## 다음 단계
{: #next_steps-logging notoc}

각 배치 환경에서 로그 보기에 대해 자세히 알아보려면 다음을 참조하십시오.
* [Kubernetes 로그](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")
* [Cloud Foundry 로그](/docs/services/CloudLogAnalysis/cfapps?topic=cloudloganalysis-logging_cf_apps#logging_cf_apps)
* [{{site.data.keyword.openwhisk}} 로그 및 모니터링](/docs/openwhisk?topic=cloud-functions-openwhisk_logs#openwhisk_logs)

로그 집계기 사용:
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK 스택](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")
