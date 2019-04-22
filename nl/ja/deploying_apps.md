---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-28"

keywords: deploy nodejs app, serverless nodejs, back-end nodejs, generated sdk nodejs, cloud foundry deploy nodejs, kubernetes deploy nodejs, virtual nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js アプリのデプロイと統合
{: #deploy_apps-nodejs}

ツールチェーンまたはコマンド・ライン・インターフェースを使用して Node.js アプリをデプロイできます。 ツールチェーンは、ツール統合の集合です。 コマンド・ライン・インターフェースは、アプリとサービス・インスタンスをデプロイするための簡単な方法です。

詳しくは、[アプリのデプロイ](/docs/apps/dep-app-tool.html#deploying-apps)を参照してください。

## サーバーレス Node.js アプリの開発
{: #serverless-nodejs}

サーバーレス開発を円滑に行うため、IBM の Functions as a Service (FaaS) オファリングである {{site.data.keyword.openwhisk}} を使用できます。 {{site.data.keyword.openwhisk_short}} を使用すると、サーバーのプロビジョニングや管理を行うことなく、HTTP 経由での Web アプリまたはモバイル・アプリからの直接呼び出しまたはイベントへの応答としてアプリケーション・ロジックを実行できます。

{{site.data.keyword.openwhisk_short}} が自動スケーリング、可用性管理、保守などのシステム管理を実行するため、開発者はアプリケーション・ロジックの作成に注力できます。

詳しくは、[サーバーレス・アプリの開発](/docs/apps/deploying/functions.html#serverless)を参照してください。

## 生成された SDK によるバックエンド・サービスの統合
{: #backend_gensdk}

{{site.data.keyword.IBM_notm}} SDK Generator プラグインは、生成された SDK によって、容易にバックエンド・サービスをアプリに統合します。 REST API が変更された場合は、SDK を再生成し、古い SDK を置換してシームレスな SDK アップグレードを行うことができます。 また、CLI を DevOps パイプラインに統合することによって、アプリをビルドするたびに、SDK が API スペックに常に整合していることを確認することができます。

詳しくは、[生成された SDK によるバックエンド・サービスの統合](/docs/swift/backend/cli_sdkgen.html#sdkgen-cli)を参照してください。

## Kubernetes クラスターへのデプロイ
{: #deploy_kube}

Watson Tone Analyzer を使用するコンテナー化 Node.js アプリを {{site.data.keyword.cloud_notm}} Kubernetes Service を使用してデプロイする方法を学ぶことができます。 用意されているシナリオでは、架空の PR 会社が {{site.data.keyword.cloud_notm}} サービスを使用して自社のプレス・リリースを分析し、自社のメッセージのトーンに関するフィードバックを受け取ります。 詳しくは、チュートリアルの [Kubernetes クラスターへのアプリのデプロイ](/docs/containers/cs_tutorials_apps.html#cs_apps_tutorial)を参照してください。

## Cloud Foundry へのデプロイ
{: #node-deploy-cf}

このオプションを使用すると、基盤となるインフラストラクチャーを管理する必要なく、クラウド・ネイティブ・アプリがデプロイされます。

アプリを [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry/index.html#about) にデプロイする計画がある場合は、[{{site.data.keyword.cloud_notm}} アカウントを準備する](/docs/cloud-foundry/prepare-account.html#prepare)必要があります。

アカウントに {{site.data.keyword.cfee_full_notm}} に対するアクセス権限がある場合は、エンタープライズ専用の Cloud Foundry アプリケーションをホストするための分離環境を作成および管理するために使用できる、**[パブリック・クラウド](/docs/cloud-foundry-public/about-cf.html#about-cf)**または**[エンタープライズ環境](/docs/cloud-foundry-public/cfee.html#cfee)**のデプロイヤー・タイプを選択できます。

## 仮想サーバーへのデプロイ
{: #virtual_deploy}

仮想サービスでは、あらゆるワークロード・タイプのために、高いレベルの透過性、予測性、自動化を提供します。 ご使用のインフラストラクチャーを安全かつ効率的にビルドし、変更し、バージョン管理するためには、Terraform が使用されます。

詳しくは、[仮想サーバーへのデプロイ](/docs/apps/vsi-deploy.html#vsi-deploy)を参照してください。
