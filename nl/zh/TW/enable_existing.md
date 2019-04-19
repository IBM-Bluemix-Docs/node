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

# 啟用現有 Node.js 應用程式以進行雲端部署
{: #enable_existing}

您可以使用 [{{site.data.keyword.dev_cli_long}} CLI enable 指令](/docs/cli/idt?topic=cloud-cli-idt-cli#enable)產生必要的檔案，讓 Node.js 應用程式能夠在 {{site.data.keyword.cloud}} 上執行。

## 啟用應用程式
{: #enable_app}

從 Node 專案的根目錄輸入下列指令：
```
ibmcloud dev enable
```
{: codeblock}

系統會提示您驗證 Node.js 專案，請回覆 `y`。`enable` 指令也可以建立服務，並將它們連結至應用程式。針對這個基本的範例，請回覆 `n`。

請參閱下列範例輸出：
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

## 建置並部署具有雲端功能的 Node 應用程式
{: #build_deploy}

接下來，使用 [`build`](/docs/cli/idt?topic=cloud-cli-idt-cli#build) 指令建置您的應用程式：
```
ibmcloud dev build
```
{: codeblock}

如果建置順利完成，您可以使用下列 [`deploy`](/docs/cli/idt?topic=cloud-cli-idt-cli#deploy) 指令，將應用程式部署至 {{site.data.keyword.cloud_notm}}：
```
ibmcloud dev deploy
```
{: codeblock}

## 如果您的已啟用應用程式無法建置或部署，怎麼辦？
{: #build_failure}

並非所有應用程式都會順利由 `enable` 指令啟用。需要最佳效能嘗試，以確定「雲端啟用」您的應用程式所需的配置。至少，原始應用程式需要能夠順利執行 `npm install` 及 `npm start`。對於使用非 `npm start` 執行指令的應用程式，您可以修改 `Dockerfile` 中的 `CMD` 行。

如果您的應用程式無法在執行 `ibmcloud dev enable` 之後建置及/或部署，可以手動修改所產生的檔案以完成雲端啟用。

### 手動修改所產生的雲端啟用檔案
{: #manual_modify}

啟用之後，您的應用程式會使用 docker 在本端執行。如果您尚未有 Dockerfile，則會為您建立一個，並公開埠 `3000`。

不過，如果您的應用程式需要在個別容器執行的服務，例如 MongoDB 或 Redis，我們建議使用 `docker-compose` 同步啟動應用程式和必要服務。

請參閱下列 `docker-compose.yml` 範例，您可以在根目錄中建立：
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

這會在埠 `3000` 上公開您的應用程式，而 MongoDB 伺服器將可以透過埠 `27017` 接受用戶端連線。

您必須在 `cli-config.yml` 中更新以下這行，以指向新的 Dockerfile： 
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "Dockerfile"
```
{: codeblock}

更新為：
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## 後續步驟
{: #next_steps-existing notoc}

如需相關資訊，請參閱 [IBM Cloud Developer Tools CLI](/docs/cli/idt?topic=cloud-cli-idt-cli#idt-cli)。
