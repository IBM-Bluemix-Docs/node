---

copyright:
  years: 2018
lastupdated: "2018-10-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 시작하기 튜토리얼

다음 튜토리얼은 {{site.data.keyword.cloud_notm}} 제공 도구를 사용하여 Node.js 앱을 빌드하고, 로컬에서 실행하고, 배치하는 단계를 안내합니다. 다음 튜토리얼 단계에 표시되어 있는 바와 같이 [{{site.data.keyword.dev_cli_long}}](https://console.bluemix.net/docs/cloudnative/dev_cli.html#add-cli)을 명령행 또는 웹 기반 [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://console.bluemix.net/developer/appservice/dashboard)에서 사용할 수 있습니다. 이러한 방법 중 하나를 사용하면 즉시 프로덕션 가능한 Node.js 애플리케이션을 몇 분 안에 생성할 수 있습니다.

최신 Node.js LTS 릴리스를 사용하고 있는지 확인하십시오.

## Node.js 앱 작성
{: #create_project}

1. {{site.data.keyword.dev_console}}의 [스타터 킷](https://console.bluemix.net/developer/appservice/starter-kits)에서 `Node.js`로 작성된 스타터 킷을 선택하십시오. **앱 작성**을 클릭하고 `Node.js`를 언어로 선택하여 비어 있는 스타터 앱을 작성할 수도 있습니다.

    프로젝트를 작성하려면 {{site.data.keyword.cloud_notm}} 계정에 로그인해야 합니다. 계정이 없는 경우에는 [무료 계정을 등록](https://console.bluemix.net/registration)할 수 있습니다.
    {: tip}

2. **앱 작성**을 클릭하십시오.
3. 앱의 **이름**을 지정하십시오. 일반적인 앱 이름을 사용하고자 하는 경우에는 이러한 이름이 제공됩니다.
4. **고유 호스트 이름**을 입력하십시오. 호스트 이름은 애플리케이션에 액세스하는 데 사용됩니다(예: `expressjs-project.mybluemix.net`).
5. **작성**을 클릭하십시오. 프로젝트가 작성되고 나면 도구 체인을 사용하여 배치하거나, 빌드를 계속하다 명령행에서 프로젝트를 배치할 수 있습니다.
6. 도구 체인을 작성하기로 선택한 경우에는 **클라우드에 배치**를 클릭하고 다음 배치 방법 중 하나를 선택하십시오.
    * **Cloud Foundry 앱** - 기본 인프라를 관리할 필요가 없습니다.
    * **Kubernetes 클러스터** - 작업자 노드 세트를 프로비저닝해야 합니다. 예를 들면, VM을 사용하여 고가용성 애플리케이션 컨테이너를 배치하고 관리할 수 있습니다. 사용자는 클러스터를 작성하거나 기존 클러스터에 배치할 수 있습니다.

7. 선택사항을 마무리한 후 **작성**을 클릭하여 도구 체인을 작성하십시오.

8. 도구 체인 대신 CLI를 사용하여 계속 진행하려는 경우에는 프로젝트를 로컬 시스템에 다운로드하고, `unzip`한 후 루트 디렉토리로 `cd`하십시오. 이제 플랫폼 명령 방법을 사용하여 필수 소프트웨어를 설치할 수 있습니다.

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows(관리자 권한으로 실행):
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## 서비스 추가
{: #add_service}

1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}에서 자신의 프로젝트로 돌아가십시오.
2. **서비스 추가**를 클릭하고, 추가할 서비스의 카테고리를 선택하고, **다음**을 클릭한 후 서비스를 선택하십시오. 예를 들어, NoSQL 데이터베이스를 애플리케이션에 추가하려면 **데이터** 카테고리를 클릭한 후 무료 개발을 위한 Lite 플랜을 제공하는 **Cloudant**를 선택하십시오. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}에서 선택한 플랜에 따라 서비스를 프로비저닝합니다.
참고: 사용하려는 서비스를 이전에 프로비저닝한 경우에는 **기존** 카테고리를 선택하십시오.
3. 서비스가 프로비저닝되고 나면 **코드 다운로드**를 클릭하여 서비스에 연결하는 SDK로 프로젝트를 다시 생성하십시오.

<!--
<video of creating a project and adding a service>
-->

## 앱을 로컬에서 실행
{: #run_local}

1. `ibmcloud dev build` 명령을 사용하여 애플리케이션을 빌드하십시오.
2. `ibmcloud dev run` 명령을 사용하여 애플리케이션을 로컬에서 실행하십시오. 애플리케이션은 로컬 시스템의 Docker 컨테이너에서 실행됩니다. `http://localhost:3000`에 액세스하여 브라우저에서 애플리케이션을 테스트할 수 있습니다.
3. 상태 검사 엔드포인트는 `http://localhost:3000/health`에서 사용 가능합니다.
4. [앱 메트릭](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/) 대시보드는 `http://localhost:3000/appmetrics-dash`에서 액세스할 수 있습니다.

<!--
<video>
-->

## 클라우드에 배치
{: #deploy_cloud}

{{site.data.keyword.cloud_notm}}에 Cloud Foundry 애플리케이션으로서 배치하려면 `ibmcloud dev deploy` 명령을 사용하십시오. 

{{site.data.keyword.cloud_notm}}의 IBM Container Services에 배치하려면 다음 명령을 사용하십시오.
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## 다음 단계
{: #nextsteps}

Node.js 프로그래밍 안내서의 주제를 계속 참조하거나, 고급 배치를 위해 Kubernetes 클러스터에 대해 알아본 후 여기에 Node.js 앱을 배치할 수 있습니다.

### Kubernetes 클러스터 설정
{{site.data.keyword.cloud_notm}}에서 Kubernetes 클러스터를 설정하는 데 대한 자세한 정보는 [튜토리얼 단계](https://console.bluemix.net/docs/containers/cs_clusters.html#clusters)를 참조하십시오.

### Node.js 앱을 Kubernetes 클러스터에 배치
[Node.js 앱을 Kubernetes 클러스터에 배치하는 방법](../containers/cs_tutorials_apps.html)에 대해 알아보십시오.
