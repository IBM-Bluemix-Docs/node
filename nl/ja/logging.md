---

copyright:
  years: 2018
lastupdated: "2018-09-18"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js でのロギング
{: #logging_nodejs}

ログ・メッセージは、ログ・エントリーが作成された時点のマイクロサービスの状態およびアクティビティーに関するコンテキスト情報を含むストリングです。ログは、サービスでの障害がなぜどのように起こったのかを診断するために必須であり、アプリケーション正常性のモニタリングにおいて [appmetrics](appmetrics.html) を支援する役割を果たします。

クラウド環境でのプロセスの一過性の性質を考慮すると、ログは収集されて、分析のために他の場所 (通常は一元管理の場所) に送信される必要があります。クラウド環境でロギングを行う最も一貫性のある方法は、ログ・エントリーを標準出力とエラー・ストリームに送信する方法です (こうするとインフラストラクチャーが残りを処理します)。

## Node.js アプリへの Log4js サポートの追加
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) は、Node.js 用の一般的なロギング・フレームワークであり、以下のような多くの固有の利点があります。 
* `stdout` または `stderr` へのログの書き込み
* 各種の付加オプション
* 構成可能なログ・メッセージ・レイアウトおよびパターン
* さまざまなログ・カテゴリー用のログ・レベルの使用

1. Log4js を使用するには、アプリケーションのルート・ディレクトリーで次の [npm](https://nodejs.org/) コマンドを実行します。これにより、パッケージがインストールされ、`package.json` ファイルに追加されます。
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. これをアプリケーションで使用するために、以下のコード行を追加します。
  ```javascript
  var log4js = require('log4js');
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **stdout 出力**:
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

アペンダー、ログ・レベル、および構成詳細でのログ・メッセージのカスタマイズについて詳しくは、公式の [log4js-node 資料](https://log4js-node.github.io/log4js-node/)を参照してください。

## App Service を使用したログのモニタリング
{: #monitoring}

{{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard) を使用して作成された Node.js アプリには、デフォルトで Log4js が付属しています。ネイティブにアプリを実行するか、クラウド環境でアプリを実行すると、`2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000` のような出力が生成されます。出力は以下のように表示できます。
* ローカルで実行している場合は、`stdout` を使用します。
* [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) および [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) デプロイメントのログに、`ibmcloud app logs --recent <APP_NAME>` および `kubectl logs <deployment name>` によってアクセスします。

`server/server.js` ファイル内に次のようなコードがあります。
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

ログ・レベルはデフォルトでは `INFO` に設定され、アプリケーションの **LOG_LEVEL** 環境変数でオーバーライドできます。
{: tip}

## 次のステップ
{: #next_steps notoc}

各デプロイメント環境でのログの表示について詳しくは、次の各ページを参照してください。
* [Kubernetes のログ](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cloud Foundry のログ](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} のログおよびモニタリング](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

ログ統合機能の使用:
* [{{site.data.keyword.cloud_notm}} Log Analysis](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK スタック](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
