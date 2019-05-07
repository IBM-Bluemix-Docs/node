---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-30"

keywords: nodejs metrics, application metrics nodejs, node appmetrics, nodejs autoscaling, nodejs dash, appmetrics-dashs nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js アプリでのアプリケーション・メトリックの使用
{: #metrics}

Node.js アプリケーションのメトリックをインストール、アクセス、および解釈する方法について説明します。 [Node Application Metrics](https://developer.ibm.com/open/projects/node-application-metrics/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") ダッシュボードで Node.js アプリをモニターし、Web ベースのフロントエンドでメトリックを表示することによって、Node.js アプリケーションのパフォーマンスを視覚化できます。
{: shortdesc}

## 視覚的な問題識別
{: #identify-problems}

アプリケーション・メトリックは、アプリケーションのパフォーマンスをモニターするのに重要です。 CPU、メモリー、待ち時間、HTTP などのメトリックをライブで表示できることは、長時間にわたりアプリケーションが効果的に実行されていることを確認するために不可欠です。 メトリックに依存するクラウド・サービス (例えば、Cloud Foundry の[自動スケーリング](/docs/services/Auto-Scaling?topic=Auto-Scaling-get-started)など) を使用して、現行作業負荷に合わせて動的にインスタンスをスケーリングできます。 アプリケーション・メトリックを使用することによって、インスタンスのスケーリングや不要インスタンスのクリーンアップを行うタイミングを正確に把握して、コストを低く抑えることができます。

アプリケーション・メトリックは、時系列データとして収集されます。 収集されたメトリックを集約して視覚化することは、以下のような一般的なパフォーマンス上の問題を特定するのに役立ちます。

* 一部またはすべての経路で HTTP 応答時間が遅くなる
* アプリケーションのスループットが低下する
* 需要の急上昇によりスローダウンが発生する
* CPU 使用率が予想より高い
* メモリー使用量が多いか、増加している (メモリー・リークの可能性)

組み込みの Application Metrics Dashboard ([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")) には「その他の要求」についてのグラフも含まれていて、サポートされるデータベース (MongoDB、MySQL、Postgres、LevelDB、および Redis)、Socket.IO、および Riak イベントのデータベース要求所要時間が示されます。

このダッシュボードからノード・レポートまたはヒープ・スナップショットを生成して、より詳細な分析を行うことができます。

## 既存の Node.js アプリへのメトリックの追加
{: #add-appmetrics-existing}

多数の構成オプションを渡すための [`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") コンストラクターを使用して、既存の Express アプリケーションにモニター機能を追加します。 例えば、`appmetrics-dash` で追加のサーバーを開始するのではなく、既存のサーバーを使用するというオプションがあります。

### ダッシュボードのインストール
{: #install-appmetrics}

1. 例として、以下の単純な express アプリケーション「Hello World」を使用します。
  ```js
  var express = require('express')
  var app = express()
  app.get('/', function (req, res) {
      res.send('Hello World!')
  })
  app.listen(3000, function () {
      console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

2. 以下の [npm](https://nodejs.org/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") コマンドを使用して `appmetrics` ダッシュボードをインストールします。
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. 以下のコードを追加することによって、既存のアプリに `appmetrics-dash` サポートを追加します。
  ```js
  var dash = require('appmetrics-dash').attach()
  ```
  {: codeblock}

  このコマンドは、`appmetrics-dash` に、既に作成済みのサーバーを使用し、`appmetrics-dash` エンドポイントを追加するように指示します。

  変更後のコードは、以下の例のようになります。
  ```js
  var express = require('express')
  var app = express()
  var dash = require('appmetrics-dash').attach()
  app.get('/', function (req, res) {
    res.send('Hello World!')
  })
  app.listen(3000, function () {
    console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

## スターター・キットからのメトリックの使用
{: #appmetrics-starterkits}

スターター・キットから作成される Node.js アプリケーションには、`appmetrics-dash`とそのダッシュボードがデフォルトで自動的に付属しますが、使用するためには有効にする必要があります。

appmetrics コードは、`/app_name/server/server.js` という名前の生成されたアプリケーション・ソース・ファイル内にあります。
```js
// Uncomment following to enable zipkin tracing, tailor to fit your network configuration:
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## ダッシュボードへのアクセス
{: #access-dashboard}

アプリケーションを開始した後、ブラウザーで `http://<hostname>:<port>/appmetrics-dash` にアクセスします。

ローカルで実行されているアプリには、デフォルトの `localhost:3001/appmetrics-dash` を使用します。
{: tip}

Application Metrics for Node.js モニタリング・ダッシュボード UI では、HTTP 要求およびイベント・ループ待ち時間などの広範なメトリックが提供されます。詳しくは、ビデオ [Monitoring Metrics for Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") をご覧ください。

## データの解釈
{: #understanding-data}

![Appmetrics ダッシュボード](images/appmetricsdash-1.png)

ほとんどのデータは折れ線グラフとして作図されます。 「HTTP 着信要求」、「HTTP 発信要求」、および「その他の要求」では、時間に対するイベント所要時間が示されます。 「HTTP スループット」では、1 秒当たりの要求数が示されます。 「平均応答時間」では、平均所要時間が最も長い、最も使用された 5 つの着信 HTTP 要求が示されます。 「CPU」グラフおよび「メモリー」グラフでは、システムおよびプロセスの使用量が経時的に示されます。 「ヒープ」では、最大ヒープ・サイズと使用ヒープ・サイズが経時的に示されます。 「イベント・ループ待ち時間」では、Node.js イベント・ループから一定の間隔で採取された待ち時間サンプルが示されます。採取されたサンプルごとに、最短、平均、および最長の待ち時間を示す点が 1 つずつ示されます。

グラフに点がある場合、その上にカーソルを移動すると追加情報が表示されます。 例えば`「HTTP 着信要求」`の場合、応答時間および要求された URL が表示されます。

すべてのグラフで最大 15 分のデータが表示されます。

モニター対象のアプリケーションが大量のデータを生成している場合、ダッシュボードは自動的にデータの集約表示を開始します。 「HTTP 着信要求」グラフを再度見ると、それぞれの点が 2 秒間のすべての要求を示していることが分かります。 以下のツールチップは、要求の総数と、平均所要時間および最長時間を示しています。 プロットされている値は最長時間です。

![ツールチップの表示](images/tooltip-1.png)




