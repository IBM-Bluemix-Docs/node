---

copyright:
  years: 2018
lastupdated: "2018-09-10"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 概説チュートリアル

以下のチュートリアルでは、{{site.data.keyword.cloud_notm}} 提供のツールを使用して Node.js アプリをビルドし、ローカルで実行し、デプロイする手順について説明します。チュートリアルの以下の手順で示すように、コマンド・ラインまたは Web ベースの [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://console.bluemix.net/developer/appservice/dashboard)で [{{site.data.keyword.dev_cli_long}}](https://console.bluemix.net/docs/cloudnative/dev_cli.html#add-cli) を使用できます。これらの方法のいずれかを使用して、実動用の Node.js アプリケーションを数分で生成できます。

最新の Node.js LTS リリースを使用していることを確認してください。

## Node.js アプリの作成
{: #create_project}

1. {{site.data.keyword.dev_console}}の[スターター・キット](https://console.bluemix.net/developer/appservice/starter-kits)のページから、`Node.js` で作成されたスターター・キットを選択します。**「アプリの作成」** をクリックし、言語として `Node.js` を選択することによって、ブランクのスターター・アプリを作成することもできます。

    プロジェクトを作成するには、{{site.data.keyword.cloud_notm}} アカウントにログインしている必要があります。アカウントをお持ちでない場合は、[無料アカウントの登録](https://console.bluemix.net/registration)を行うことができます。
    {: tip}

2. **「アプリの作成」**をクリックします。 
3. アプリの**名前**を指定します。汎用的なアプリ名前が提示されるので、それを使用することもできます。
4. **固有のホスト名**を入力します。このホスト名は、アプリケーションにアクセスするために使用されます (例: `expressjs-project.mybluemix.net`)。
5. **「作成」**をクリックします。 プロジェクトが作成されたら、ツールチェーンを使用してデプロイするか、コマンド・ラインからプロジェクトのビルドとデプロイを続行することができます。
6. ツールチェーンの作成を選択した場合は、**「クラウドにデプロイ」**をクリックし、以下のいずれかのデプロイメント方式を選択します。
    * **Cloud Foundry アプリ** - 基礎となるインフラストラクチャーを管理する必要はありません。
    * **Kubernetes クラスター** - ワーカー・ノードのセットをプロビジョンする必要があります。例えば、VM を使用して、可用性の高いアプリケーション・コンテナーをデプロイおよび管理できます。クラスターを作成したり、既存のクラスターにデプロイしたりすることができます。

7. オプションを確定し、**「作成」** をクリックしてツールチェーンを作成します。

8. ツールチェーンの代わりに CLI を使用して続行することを選択した場合は、プロジェクトをローカル・マシンにダウンロードして `unzip` し、ルート・ディレクトリーに `cd` します。これで、プラットフォームのコマンド・メソッドを使用して前提条件をインストールできるようになります。

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows (管理者として実行):
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## サービスの追加
{: #add_service}

1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}でプロジェクトに戻ります。
2. **「サービスの追加」**をクリックし、追加するサービスのカテゴリーを選択してから**「次へ」**をクリックし、サービスを選択します。 例えば、NoSQL データベースをアプリケーションに追加するには、「データ」カテゴリーをクリックして、 無料開発用のライト・プランが提供される Cloudant を選択します。{{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}は、選択されたプランに基づいてサービスをプロビジョンします。
注: 使用する予定のサービスを以前にプロビジョンした場合は、**「既存」** カテゴリーを選択してください。
3. サービスがプロビジョンされたら、**「コードのダウンロード」** をクリックして、サービスに接続する SDK と共にプロジェクトを再生成します。

<!--
<video of creating a project and adding a service>
-->

## ローカルでのアプリケーションの実行
{: #run_local}

1. `ibmcloud dev build` コマンドを使用して、アプリケーションをビルドします。
2. `ibmcloud dev run` コマンドを使用して、アプリケーションをローカルで実行します。アプリケーションは、ローカル・システム上の Docker コンテナーで実行されます。`http://localhost:3000` にアクセスすることによって、ブラウザーでアプリケーションをテストできます。
3. ヘルス・チェック・エンドポイントは `http://localhost:3000/health` で使用可能です。
4. [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/) ダッシュボードには、`http://localhost:3000/appmetrics-dash` でアクセスできます。

<!--
<video>
-->

## クラウドへのデプロイ
{: #deploy_cloud}

Cloud Foundry アプリケーションとして {{site.data.keyword.cloud_notm}} にデプロイするには、`ibmcloud dev deploy` コマンドを使用します。 

{{site.data.keyword.cloud_notm}} 内の IBM Container Services にデプロイするには、以下のコマンドを使用します。
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## 次のステップ
{: #nextsteps}

続けて Node.js プログラミング・ガイドのトピックを参照するか、または、もっと上級のデプロイメントに関心がある場合は、Kubernetes クラスターを作成してそこに Node.js アプリをデプロイする方法を学習してください。

### Kubernetes クラスターのセットアップ
{{site.data.keyword.cloud_notm}} での Kubernetes クラスターのセットアップについて詳しくは、[チュートリアルのステップ](https://console.bluemix.net/docs/containers/cs_clusters.html#clusters)を参照してください。

### Kubernetes クラスターへの Node.js アプリのデプロイ
[Node.js アプリを Kubernetes クラスターにデプロイ](../containers/cs_tutorials_apps.html)する方法を学習できます。
