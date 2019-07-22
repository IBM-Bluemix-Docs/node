---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-05"

keywords: ibmcloud dev enable, nodejs cloud deployment, cloud enable nodejs, deploy nodejs, build nodejs cloud, nodejs debug

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 启用云部署的现有 Node.js 应用程序
{: #enable_existing}

您可以通过使用 [{{site.data.keyword.dev_cli_long}} CLI enable 命令](/docs/cli/idt?topic=cloud-cli-idt-cli#enable)，生成支持 Node.js 应用程序在 {{site.data.keyword.cloud}} 上运行所需的文件。

## 启用应用程序
{: #enable_app}

从 Node 项目的根目录输入以下命令：
```
ibmcloud dev enable
```
{: codeblock}

系统将提示您验证 Node.js 项目，请回复 `y`。`enable` 命令还可以创建服务，并将其绑定到您的应用程序。对于此基本示例，请回复 `n`。

请参阅以下样本输出：
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

## 构建并部署支持云的 Node 应用程序
{: #build_deploy}

接下来，使用 [`build`](/docs/cli/idt?topic=cloud-cli-idt-cli#build) 命令构建应用程序：
```
ibmcloud dev build
```
{: codeblock}

如果成功完成构建，您可以使用以下 [`deploy`](/docs/cli/idt?topic=cloud-cli-idt-cli#deploy) 命令将应用程序部署到 {{site.data.keyword.cloud_notm}}：
```
ibmcloud dev deploy
```
{: codeblock}

## 如果已启用的应用程序未构建或部署，该怎么做
{: #build_failure}

`enable` 命令并不能成功启用所有应用程序。该命令尽最大努力尝试确定对您的应用程序“启用云”所需的配置。至少，原始应用程序需要能够成功运行 `npm install` 和 `npm start`。对于使用与 `npm start` 不同的运行命令的应用程序，您可以修改 `Dockerfile` 中的 `CMD` 行。

如果在运行 `ibmcloud dev enable` 之后应用程序未构建和/或部署，您可以手动修改已生成的文件以完成云支持。

### 手动修改已生成的云支持文件
{: #manual_modify}

一旦启用，您的应用程序将使用 Docker 在本地运行。如果您还没有 Dockerfile，那么将为您创建一个并公开端口 `3000`。

但是，如果您的应用程序需要在独立的容器中运行的服务（如 MongoDB 或 Redis），那么我们建议使用 `docker-compose` 同时支持应用程序和所需的服务。

请参阅以下 `docker-compose.yml` 示例，您可以在根目录中创建该示例：
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

您的应用程序在端口 `3000` 上公开，MongoDB 服务器可用于端口 `27017` 上的客户连接。

您必须更新 `cli-config.yml` 中的以下行，以指向新 Dockerfile： 
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "Dockerfile"
```
{: codeblock}

更新为：
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## 后续步骤
{: #next_steps-existing notoc}

有关更多信息，请参阅 [IBM Cloud Developer Tools CLI](/docs/cli/idt?topic=cloud-cli-idt-cli#idt-cli)。
