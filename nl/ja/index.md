---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-07"

keywords: node getting started, node cloud native, create node app, add node service, node programming guide, node guide

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 入門チュートリアル
{: #getting-started}

以下のチュートリアルでは、{{site.data.keyword.cloud_notm}} 提供のツールを使用して Node.js アプリをビルドし、ローカルで実行し、デプロイする手順について説明します。 コマンド・ラインで [{{site.data.keyword.dev_cli_long}}](/docs/cli?topic=cloud-cli-getting-started) を使用するか、以下のチュートリアルの手順で示すように、Web ベースの [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を使用することができます。これらの方法のいずれかを使用して、実動用の Node.js アプリケーションを数分で生成できます。

最新の Node.js LTS リリースを使用していることを確認してください。

## Node.js アプリの作成
{: #node-create-project}

1. {{site.data.keyword.dev_console}}の[スターター・キット](https://cloud.ibm.com/developer/appservice/starter-kits){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") のページから、`Node.js` で作成されたスターター・キットを選択します。 **「アプリの作成」** をクリックし、言語として `Node.js` を選択することによって、ブランクのスターター・アプリを作成することもできます。

    アプリを作成するには、{{site.data.keyword.cloud_notm}} アカウントにログインする必要があります。 アカウントをお持ちでない場合は、[無料アカウントの登録](https://cloud.ibm.com/registration){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を行うことができます。
    {: tip}

2. **「アプリの作成」**をクリックします。
3. アプリの**名前**を指定します。 汎用的なアプリ名が提示されるので、それを使用することもできます。
4. **「作成」**をクリックします。 プロジェクトが作成されたら、ツールチェーンを使用してデプロイするか、引き続きコマンド・ラインからプロジェクトをビルドしてからデプロイすることができます。
5. ダッシュボードでデプロイメント・ツールチェーンを作成するには、**「デプロイ」**をクリックします。 次の方法のうちの選択する方法に応じた手順に従って、デプロイメント・ターゲットをセットアップします。
  * **[IBM Kubernetes Service](/docs/containers?topic=containers-app) にデプロイ**します。 このオプションでは、可用性の高いアプリケーション・コンテナーをデプロイして管理するためのワーカー・ノードと呼ばれるホストのクラスターを作成します。 クラスターを作成したり、既存のクラスターにデプロイしたりすることができます。
  * **Cloud Foundry へのデプロイ**。 このオプションでは、基礎となるインフラストラクチャーの管理を必要とせずに、クラウド・ネイティブ・アプリをデプロイします。 ご使用のアカウントで {{site.data.keyword.cfee_full_notm}} にアクセスできる場合、デプロイヤー・タイプの **[Public Cloud](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** または **[Enterprise Environment](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)** のいずれかを選択できます。後者を使用すれば、お客様の会社専用に Cloud Foundry アプリケーションをホストするための分離された環境を作成して管理することができます。
  * **[仮想サーバー](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server)へのデプロイ**。 このオプションでは、仮想サーバー・インスタンスをプロビジョンし、アプリが含まれたイメージをロードし、DevOps ツールチェーンを作成し、最初のデプロイメント・サイクルを自動的に開始します。

6. オプションを確定し、**「作成」** をクリックしてツールチェーンを作成します。
7. ツールチェーンの代わりに CLI を使用して続行することを選択した場合は、プロジェクトをローカル・マシンにダウンロードして `unzip` し、ルート・ディレクトリーに `cd` で移動します。 これで、プラットフォームのコマンド・メソッドを使用して前提条件をインストールできるようになります。

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
{: #node-add-service}

1. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}でプロジェクトに戻ります。
2. **「サービスの追加」**をクリックし、追加するサービスのカテゴリーを選択してから**「次へ」**をクリックし、サービスを選択します。 例えば、NoSQL データベースをアプリケーションに追加するには、**「データ」**カテゴリーを選択して、 無料開発用のライト・プランが提供される**「Cloudant」**を選択します。 {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}は、選択されたプランに基づいてサービスをプロビジョンします。
注: 使用する予定のサービスを以前にプロビジョンした場合は、**「既存」** カテゴリーを選択してください。
3. サービスがプロビジョンされたら、**「コードのダウンロード」** をクリックして、サービスに接続するプロジェクトを SDK を使用して再生成します。

<!--
<video of creating a project and adding a service>
-->

## ローカルでのアプリケーションの実行
{: #node-run-local}

1. `ibmcloud dev build` コマンドを使用して、アプリケーションをビルドします。
2. `ibmcloud dev run` コマンドを使用して、アプリケーションをローカルで実行します。 アプリケーションは、ローカル・システム上の Docker コンテナーで実行されます。 `http://localhost:3000` にアクセスすることによって、ブラウザーでアプリケーションをテストできます。
3. ヘルス・チェック・エンドポイントは `http://localhost:3000/health` で使用可能です。
4. [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") ダッシュボードには、`http://localhost:3000/appmetrics-dash` でアクセスできます。

<!--
<video>
-->

## IBM Cloud へのデプロイ
{: #deploy_cloud}

Cloud Foundry アプリケーションとして {{site.data.keyword.cloud_notm}} にデプロイするには、`ibmcloud dev deploy` コマンドを使用します。 

{{site.data.keyword.cloud_notm}} 内の IBM Container Services にデプロイするには、以下のコマンドを使用します。
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## 次のステップ
{: #node-tutorial-nextsteps notoc}

続けて Node.js プログラミング・ガイドのトピックを参照するか、または、もっと上級のデプロイメントに関心がある場合は、Kubernetes クラスターを作成してそこに Node.js アプリをデプロイする方法を学習してください。

### Kubernetes クラスターのセットアップ
{{site.data.keyword.cloud_notm}} での Kubernetes クラスターのセットアップについて詳しくは、[チュートリアルのステップ](/docs/containers?topic=containers-clusters)を参照してください。

### Kubernetes クラスターへの Node.js アプリのデプロイ
[Node.js アプリを Kubernetes クラスターにデプロイ](/docs/containers?topic=containers-cs_apps_tutorial)する方法を学習できます。
