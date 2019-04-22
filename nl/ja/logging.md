---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-04"

keywords: nodejs logging, view logs nodejs, add logging nodejs, log4j nodejs, stdout nodejs, nodejs log, output nodejs, nodejs logger

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js でのロギング
{: #logging_nodejs}

ログ・メッセージは、ログ・エントリーが作成された時点のマイクロサービスの状態およびアクティビティーに関するコンテキスト情報を含むストリングです。 ログは、サービスでの障害がなぜどのように起こったのかを診断するために必須であり、アプリケーション正常性のモニタリングにおいて [appmetrics](/docs/node?topic=nodejs-metrics) を支援する役割を果たします。

クラウド環境でのプロセスの一過性の性質を考慮すると、ログは収集されて、分析のために他の場所 (通常は一元管理の場所) に送信される必要があります。 クラウド環境でロギングを行う最も一貫性のある方法は、ログ・エントリーを標準出力とエラー・ストリームに送信する方法です (こうするとインフラストラクチャーが残りを処理します)。

Node.js 用の一般的なロギング・フレームワークである [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を使用できます。これには、以下のような多くの固有の利点があります。 
* `stdout` または `stderr` へのログの書き込み
* 各種の付加オプション
* 構成可能なログ・メッセージ・レイアウトおよびパターン
* さまざまなログ・カテゴリー用の`ログ・レベル`の使用

## 既存の Node.js アプリへの Log4js サポートの追加
{: #add_log4j}

1. まず、アプリケーションのルート・ディレクトリーで次の [npm](https://nodejs.org/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") コマンドを実行して `log4js` をインストールします。これにより、パッケージがインストールされ、`package.json` ファイルに追加されます。
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. これをアプリケーションで使用するために、以下のコード行をアプリに追加します。
  ```js
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

3. ログ・イベントを標準エラー・ストリームに書き込むために、次の例のようにアペンダーを構成できます。
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  アペンダー、`ログ・レベル`、および構成詳細でのログ・メッセージのカスタマイズについて詳しくは、公式の [log4js-node 資料](https://log4js-node.github.io/log4js-node/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を参照してください。

## App Service アプリでのモニタリング
{: #monitoring}

{{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を使用して作成された Node.js アプリには、デフォルトで Log4js が付属しています。`server/server.js` ファイルを開くと、次の Log4js が表示されます。
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

`ログ・レベル`はデフォルトでは `OFF` に設定されているため、ライブラリーでは安全に使用できますが、アプリケーションの **LOG_LEVEL** 環境変数によってオーバーライドできます。
{: tip}

### ログ出力の表示
{: #node-view-log-output}

以下は、アプリをネイティブに実行した場合、またはクラウド環境で実行した場合のログ出力のサンプルです。
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

ログ出力を表示するには、以下のメソッドを使用します。
* ローカル環境の場合は、`stdout` を使用します。
* [Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html) デプロイメントの場合は、以下を実行すると、ログにアクセスできます。
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) デプロイメントの場合は、以下を実行すると、ログにアクセスできます。
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## 次のステップ
{: #next_steps-logging notoc}

各デプロイメント環境でのログの表示についての詳細:
* [Kubernetes のログ](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")
* [Cloud Foundry のログ](/docs/services/CloudLogAnalysis/cfapps?topic=cloudloganalysis-logging_cf_apps#logging_cf_apps)
* [{{site.data.keyword.openwhisk}} のログおよびモニタリング](/docs/openwhisk?topic=cloud-functions-openwhisk_logs#openwhisk_logs)

ログ統合機能の使用:
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK スタック](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")
