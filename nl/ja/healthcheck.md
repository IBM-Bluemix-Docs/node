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

# Node.js アプリでのヘルス・チェックの使用
{: #healthcheck}

ヘルス・チェックには、サーバー・サイド・アプリケーションが正常に動作しているかどうかを判別するための単純なメカニズムが備わっています。[Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) や [Kubernetes](https://www.ibm.com/cloud/container-service) などの多くのデプロイメント環境は、サービスのインスタンスがトラフィックを受け入れる準備ができているかどうかを判別するためにヘルス・エンドポイントを定期的にポーリングするように構成できます。

## ヘルス・チェックの概要
{: #overview}

ヘルス・チェックは、通常は `HTTP` を介してアクセスされ、標準の戻りコードを使用して `UP` または `DOWN` の状況を示します。戻りコードの例としては、`UP` を示す `200`、`DOWN` を示す `5xx` があります。例えば、アプリケーションが要求を処理できない場合は戻りコード `503` が、サーバーでエラー状態が発生した場合は戻りコード `500` が使用されます。ヘルス・チェックの戻り値は変数ですが、`{“status”: “UP”}` のような最小限の JSON 応答によって一貫性が確保されています。

Kubernetes は、[liveness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) プローブと [readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) プローブの両方を定義します。

* liveness プローブは、プロセスを再始動できるかどうかをアプリケーションが示すことを可能にします。一部の考慮事項が当てはまります。例えば、メモリーしきい値に達した場合に失敗になる `liveness` チェックが考えられます。アプリが Kubernetes で実行されている場合、必要な場合にプロセスが確実に再始動されるように、`liveness` エンドポイントを追加することを検討してください。

* readiness プローブは、自動ルーティング決定に使用されます。アプリケーションが新しい作業を受け取ることができるかどうかが、成功または失敗で示されます。このエンドポイントには、必要なダウンストリーム・サービスに関する考慮事項を含めることができます。ダウンストリーム・サービスのチェック結果をキャッシュに入れて、システム全体の負荷を最小化する (例えば、1 秒当たり最大 1 回だけデータベース接続をテストする) ことを検討してください。

Cloud Foundry は、1 つのみのヘルス・エンドポイントを使用します。このチェックが失敗した場合、Cloud Foundry はプロセスを再始動します。しかし、成功した場合、Cloud Foundry はプロセスが新しい作業を処理できると想定します。このヘルス・チェックは、単一のエンドポイントを liveness と readiness を組み合わせたものにしています。

## 既存の Node.js アプリへのヘルス・チェックの追加
{: #add-healthcheck-existing}

ヘルス・チェック・エンドポイントを既存のアプリに追加するには、まず、以下の例に示すように新しいルートを導入します。
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

ここで意味のある基準を追加すると、`/health` エンドポイントからの成功応答によって、サービスが要求を処理する準備ができたことが確実に正しく示されるようになります。

## Node.js スターター・キット・アプリからのヘルス・チェックへのアクセス
{: #healthcheck-starterkit}

デフォルトでは、スターター・キットを使用して Node.js アプリを生成すると、アプリの状況 (UP/DOWN) をチェックするための 1 つの基本的な (無許可) ヘルス・チェック・エンドポイントが `/health` で使用可能になります。

ヘルス・チェック・エンドポイントのコードは、以下の `/server/routers/health.js` ファイルによって提供されます。
```js
var express = require('express');

module.exports = function(app) {
  var router = express.Router();

  router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });

  app.use("/health", router);
}
```
{: codeblock}

アプリケーションがトラフィックを受け入れる準備ができたときにのみ成功して戻るように、チェックをカスタマイズできます。
{: tip}
