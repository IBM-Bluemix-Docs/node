---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-13"

keywords: deploy nodejs app, serverless nodejs, back-end nodejs, generated sdk nodejs, cloud foundry deploy nodejs, kubernetes deploy nodejs, virtual nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Node.js アプリのデプロイと統合
{: #deploy_apps-nodejs}

Node.js アプリは、ツールチェーンまたはコマンド・ライン・インターフェースを使用してデプロイできます。 ツールチェーンは、ツール統合の集合です。 コマンド・ライン・インターフェースは、アプリとサービス・インスタンスをデプロイするための簡単な方法です。

詳しくは、[アプリのデプロイ](/docs/apps?topic=creating-apps-deploying-apps)を参照してください。

## サーバーレス Node.js アプリの開発
{: #serverless-nodejs}

サーバーレス開発を円滑に行うため、IBM の Functions as a Service (FaaS) オファリングである {{site.data.keyword.openwhisk}} を使用できます。 {{site.data.keyword.openwhisk_short}} を使用すると、サーバーのプロビジョニングや管理を行うことなく、HTTP 経由での Web アプリまたはモバイル・アプリからの直接呼び出しまたはイベントへの応答としてアプリケーション・ロジックを実行できます。

{{site.data.keyword.openwhisk_short}} が自動スケーリング、可用性管理、保守などのシステム管理を実行するため、開発者はアプリケーション・ロジックの作成に注力できます。

詳しくは、[サーバーレス・アプリの開発](/docs/apps/deploying?topic=creating-apps-serverless)を参照してください。

## 生成された SDK によるバックエンド・サービスの統合
{: #backend_gensdk}

{{site.data.keyword.IBM_notm}} SDK Generator プラグインは、生成された SDK によって、容易にバックエンド・サービスをアプリに統合します。 REST API が変更された場合は、SDK を再生成し、古い SDK を置換してシームレスな SDK アップグレードを行うことができます。 また、CLI を DevOps パイプラインに統合することによって、アプリをビルドするたびに、SDK が API スペックに常に整合していることを確認することができます。

詳しくは、[生成された SDK によるバックエンド・サービスの統合](/docs/swift/backend?topic=swift-sdkgen-cli)を参照してください。

## Kubernetes クラスターへのデプロイ
{: #deploy_kube}

Watson Tone Analyzer を使用するコンテナー化 Node.js アプリを {{site.data.keyword.cloud_notm}} Kubernetes Service を使用してデプロイする方法を学ぶことができます。 用意されているシナリオでは、架空の PR 会社が {{site.data.keyword.cloud_notm}} サービスを使用して自社のプレス・リリースを分析し、自社のメッセージのトーンに関するフィードバックを受け取ります。 詳しくは、チュートリアル: [Kubernetes クラスターへのアプリのデプロイ](/docs/containers?topic=containers-cs_apps_tutorial)を参照してください。

## Cloud Foundry へのデプロイ
{: #node-deploy-cf}

このオプションでは、基礎となるインフラストラクチャーの管理を必要とせずに、クラウド・ネイティブ・アプリをデプロイします。

アプリを [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry?topic=cloud-foundry-about) にデプロイする計画の場合は、[{{site.data.keyword.cloud_notm}} アカウントを準備する](/docs/cloud-foundry?topic=cloud-foundry-prepare)必要があります。

ご使用のアカウントで {{site.data.keyword.cfee_full_notm}} にアクセスできる場合、デプロイヤー・タイプの **[Public Cloud](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** または **[Enterprise Environment](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)** のいずれかを選択できます。後者を使用すれば、お客様の会社専用に Cloud Foundry アプリケーションをホストするための分離された環境を作成して管理することができます。

## 仮想サーバーへのデプロイ
{: #virtual_deploy}

仮想サービスでは、あらゆるワークロード・タイプのために、高いレベルの透過性、予測性、自動化を提供します。 Terraform を使用して、インフラストラクチャーの構築、変更、バージョン管理を安全かつ効率的に行うことができます。

  いくつかのスターター・キットで VSI デプロイメントを使用できます。この機能を使用するには、[{{site.data.keyword.cloud_notm}} ダッシュボード](https://{DomainName})に移動し、**「アプリ」**タイルで**「アプリの作成」**をクリックします。
  {: note} 

詳しくは、[仮想サーバーへのデプロイ](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server)を参照してください。
