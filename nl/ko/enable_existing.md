---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-28"

keywords: ibmcloud dev enable, nodejs cloud deployment, cloud enable nodejs, deploy nodejs, build nodejs cloud, nodejs debug

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 클라우드 배치를 위해 기존 Node.js 애플리케이션 사용
{: #enable_existing}

[{{site.data.keyword.dev_cli_long}} CLI 사용 명령](/docs/cli/idt?topic=cloud-cli-idt-cli#enable)을 사용하여 Node.js 애플리케이션이 {{site.data.keyword.cloud}}에 실행되는 데 필요한 파일을 생성할 수 있습니다.

## 애플리케이션 사용
{: #enable_app}

Node 프로젝트의 루트 디렉토리에서 다음 명령을 입력하십시오.
```
ibmcloud dev enable
```
{: codeblock}

Node.js 프로젝트를 확인하라는 프롬프트가 표시되었습니다. `y`로 응답하십시오. `enable` 명령으로 서비스를 작성하고 이를 애플리케이션에 바인드할 수도 있습니다. 이 기본 예제에서는 `n`으로 응답하십시오.

다음 샘플 출력을 참조하십시오.
```
demo: $ ibmcloud dev enable
The enable feature is currently in Beta.
Please provide your experience and feedback at: https://ibm-cloud-tech.slack.com/messages/developer-tools/
Only server-side applications are supported by the enable feature
? Node application discovered. Do you want to proceed with this language choice? [y/n]> y

Using the resource group default (default) of your account
? Do you want to create a service and connect it to this application? [y/n]> n
                                    
The following files were added to your application:

.cfignore
.dockerignore
Dockerfile-tools
Dockerfile
Jenkinsfile
README.md.merge
cli-config.yml
run-debug
manifest.yml
run-dev
.bluemix/deploy.json
.bluemix/pipeline.yml
.bluemix/toolchain.yml
.bluemix/scripts/container_build.sh
.bluemix/scripts/kube_deploy.sh
chart/nodeapp/Chart.yaml
chart/nodeapp/bindings.yaml
chart/nodeapp/values.yaml
chart/nodeapp/templates/hpa.yaml
chart/nodeapp/templates/service.yaml
chart/nodeapp/templates/deployment.yaml
chart/nodeapp/templates/basedeployment.yaml
chart/nodeapp/templates/istio.yaml
.gitignore.merge
LICENSE


The following file conflicts need to be merged manually:

README.md.merge
.gitignore.merge

Files can be easily compared with 'git diff' or a similar tool.
The application, nodeapp, has been successfully saved into the current directory.
demo: $
```
{: screen}

## 노드 애플리케이션에 사용 가능한 클라우드 빌드 및 배치
{: #build_deploy}

그런 다음 [`build`](/docs/cli/idt?topic=cloud-cli-idt-cli#build) 명령을 사용하여 애플리케이션을 빌드하십시오.
```
ibmcloud dev build
```
{: codeblock}

빌드가 완료되면 다음 [`deploy`](/docs/cli/idt?topic=cloud-cli-idt-cli#deploy) 명령을 사용하여 애플리케이션을 {{site.data.keyword.cloud_notm}}에 배치할 수 있습니다.
```
ibmcloud dev deploy
```
{: codeblock}

## 사용으로 설정된 애플리케이션이 빌드하거나 배치하지 않는 경우 수행할 작업
{: #build_failure}

모든 애플리케이션이 `enable` 명령을 통해 사용으로 설정되지는 않습니다. 애플리케이션의 "클라우드 인에이블먼트"에 필요한 구성을 결정하기 위해 최선의 노력을 합니다. 최소한 원래의 애플리케이션은 `npm install` 및 `npm start`를 실행할 수 있어야 합니다. `npm start` 이외의 다른 실행 명령을 사용하는 애플리케이션의 경우 `Dockerfile`의 `CMD` 행을 수정할 수 있습니다.

`ibmcloud dev enable` 실행 후 애플리케이션이 빌드 및/또는 배치하지 않는 경우 클라우드 인에이블먼트를 완료하도록 생성된 파일을 수동으로 수정할 수 있습니다.

### 생성된 클라우드 인에이블먼트 파일을 수동으로 수정
{: #manual_modify}

사용으로 설정되면 애플리케이션은 Docker를 사용하여 로컬로 실행됩니다. 아직 Dockerfile이 없는 경우 사용자용 Dockerfile이 작성되고 포트 `3000`이 노출됩니다.

그러나 애플리케이션에 개별 컨테이너에서 실행되는 서비스(예: MongoDB 또는 Redis)가 필요한 경우 애플리케이션 및 필요한 서비스를 동시에 유지하도록 `docker-compose`를 사용하는 것이 좋습니다.

루트 디렉토리에서 작성할 수 있는 다음 `docker-compose.yml` 예제를 참조하십시오.
```yaml
version: '2'
services:
  web:
    build: .
    image: "app-express-run"
    container_name: "app-express-run"
    command: npm run start
    ports:
      - "3000:3000"
    links:
      - mongo
    environment:
      MONGO_URL: "mongo"
      MONGO_DB_NAME: "comments"
      NODE_ENV: "development"
      PORT: 3000
  mongo:
    image: mongo
    ports:
      - "27017:27017" 
```
{: codeblock}

이를 통해 포트 `3000`에서 애플리케이션이 노출되고 포트 `27017`을 통해 클라이언트 연결에 MongoDB 서버를 사용할 수 있게 됩니다.

새 Dockerfile을 가리키려면 `cli-config.yml`에 다음 행을 업데이트해야 합니다. 
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "Dockerfile"
```
{: codeblock}

다음으로 업데이트하십시오.
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## 다음 단계
{: #next_steps-existing notoc}

자세한 정보는 [IBM Cloud Developer Tools CLI](/docs/cli/idt?topic=cloud-cli-idt-cli#idt-cli)를 참조하십시오.
