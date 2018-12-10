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

# 既存の Node.js アプリケーションのクラウド・デプロイメント使用可能化
{: #enable_existing}

[{{site.data.keyword.dev_cli_long}} CLI enable コマンド](https://console.bluemix.net/docs/cli/idt/commands.html#enable)を使用して、Node.js アプリケーションを {{site.data.keyword.cloud}} で実行できるようにするために必要なファイルを生成できます。

## アプリケーションの使用可能化
{: #enable_app}

Node プロジェクトのルート・ディレクトリーから、次のコマンドを入力します。
```
ibmcloud dev enable
```
{: codeblock}

Node.js プロジェクトの確認を求めるプロンプトが出されたら、「`y`」と応答します。`enable` コマンドは、サービスを作成して、それをアプリケーションにバインドすることもできます。この基本的な例では、「`n`」と応答します。

次の出力例を参照してください。
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

## クラウド使用可能 Node アプリケーションのビルドおよびデプロイ
{: #build_deploy}

次に、[`build`](/docs/cli/idt/commands.html#build) コマンドを使用してアプリケーションをビルドします。
```
ibmcloud dev build
```
{: codeblock}

ビルドが正常に完了した場合は、次の [`deploy`](/docs/cli/idt/commands.html#deploy) コマンドを使用して {{site.data.keyword.cloud_notm}} にアプリケーションをデプロイできます。
```
ibmcloud dev deploy
```
{: codeblock}

## 使用可能アプリケーションがビルドまたはデプロイされていない場合の対応
{: #build_failure}

`enable` コマンドによってすべてのアプリケーションが正常に使用可能になる訳ではありません。このコマンドは、アプリケーションの「クラウド使用可能化」に必要な構成を判別するためにベスト・エフォートを試行します。少なくとも、元のアプリケーションは、`npm install` と `npm start` を正常に実行できるようにする必要があります。`npm start` とは異なる実行コマンドを使用するアプリケーションの場合は、`Dockerfile` の `CMD` 行を変更できます。

`ibmcloud dev enable` を実行した後にアプリケーションがビルドやデプロイを行わない場合は、生成されたファイルを手動で変更してクラウド使用可能化を完了できます。

### 生成されたクラウド使用可能化ファイルの手動による変更
{: #manual_modify}

使用可能化された後、アプリケーションはローカルで実行するために Docker を使用します。Dockerfile がまだなければ、作成され、ポート `3000` が公開されます。

ただし、アプリケーションが、別のコンテナーで実行される MongoDB や Redis などのサービスを必要とする場合は、アプリケーションと必要なサービスの両方を同時に立ち上げる `docker-compose` の使用をお勧めします。

ルート・ディレクトリー内に作成できる、次の `docker-compose.yml` の例を参照してください。
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

これにより、アプリケーションがポート `3000` に公開され、MongoDB サーバーがポート `27017` 経由でクライアントに接続できるようになります。

`cli-config.yml` の次の行を更新して、新しい Dockerfile ファイルを指すようにする必要があります。 
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "Dockerfile"
```
{: codeblock}

次のように更新します。
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## 次のステップ
{: #next_steps notoc}

詳しくは、[IBM Cloud Developer Tools CLI](https://console.bluemix.net/docs/cli/idt/commands.html#idt-cli)を参照してください。