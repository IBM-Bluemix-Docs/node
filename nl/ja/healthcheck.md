---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js アプリでのヘルス・チェックの使用
{: #healthcheck}

ヘルス・チェックには、サーバー・サイド・アプリケーションが正常に動作しているかどうかを判別するための単純なメカニズムが備わっています。 [Kubernetes](https://www.ibm.com/cloud/container-service) や [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) などのクラウド環境は、サービスのインスタンスがトラフィックを受け入れる準備ができているかどうかを判別するためにヘルス・エンドポイントを定期的にポーリングするように構成できます。

## ヘルス・チェックの概要
{: #overview}

ヘルス・チェックには、サーバー・サイド・アプリケーションが正常に動作しているかどうかを判別するための単純なメカニズムが備わっています。 通常は HTTP を介してコンシュームされ、標準の戻りコードを使用して UP または DOWN の状況を示します。 ヘルス・チェックの戻り値は変数ですが、`{"status": "UP"}` のような最小の JSON 応答が一般的です。

Kubernetes では、プロセスのヘルスについて繊細な概念があります。 次の 2 つのプローブを定義します。

- _**readiness**_ プローブは、プロセスが要求を処理できるかどうかを示すために使用されます (ルーティング可能)。

  Kubernetes は、失敗した readiness プローブを持つコンテナーに作業をルーティングしません。 サービスの初期化が完了していないか、ビジー状態、過負荷、または要求を処理できない場合、readiness プローブは失敗する可能性があります。

- _**liveness**_ プローブは、プロセスを再始動するかどうかを示すために使用されます。

  Kubernetes は、失敗した `liveness` プローブを持つコンテナーを停止し、再始動します。 メモリーが使い果たされた場合、または内部プロセスがクラッシュした後にコンテナーが適切に停止しなかった場合など、リカバリー不能な状態のプロセスを、liveness プローブを使用してクリーンアップします。

比較のための注意点として、Cloud Foundry は 1 つのヘルス・エンドポイントを使用します。 このチェックが失敗すると、プロセスは再始動されますが、成功した場合は要求がそのエンドポイントにルーティングされます。 この環境では、プロセスが実行中であるときは、エンドポイントは最小限の成功です。 初期遅延は、再始動サイクルを回避するためにサービスの初期化が完了するまでヘルス・チェックを延期するように構成されています。

次の表は、readiness、liveness、および特異なヘルスのエンドポイントが提供する応答に関するガイダンスを示しています。

| 状態    | readiness                   | liveness                   | ヘルス                    |
|----------|-----------------------------|----------------------------|---------------------------|
|          | 非 OK。ロードされない原因となる       | 非 OK。再始動されない原因となる      | 非 OK。再始動されない原因となる     |
| 開始中 | 503 - 使用不可           | 200 - OK                   | 遅延を使用してテストを回避   |
| アップ       | 200 - OK                    | 200 - OK                   | 200 - OK                  |
| 停止中 | 503 - 使用不可           | 200 - OK                   | 503 - 使用不可         |
| ダウン     | 503 - 使用不可           | 503 - 使用不可          | 503 - 使用不可         |
| エラー発生  | 500 - サーバー・エラー          | 500 - サーバー・エラー         | 500 - サーバー・エラー        |

## 既存の Node.js アプリへのヘルス・チェックの追加
{: #add-healthcheck-existing}

次の例に示すように、新しいルートを導入することによって、既存のアプリケーションに最小限のヘルス・チェックまたは liveness チェックを追加します。
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

ブラウザーで `/health` エンドポイントにアクセスして、アプリの状況をチェックします。

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

## readiness プローブおよび liveness プローブの推奨
{: #readiness-recommend}

readiness プローブには、ダウンストリーム・サービスが使用できないとき、許容可能なフォールバックがない場合に、その結果にダウンストリーム・サービスへの接続の可能性を含めることができます。 接続の可能性はインフラストラクチャーによってチェックされるため、これはダウンストリーム・サービスによって提供されるヘルス・チェックを直接呼び出すという意味ではありません。 代わりに、アプリケーションがダウンストリーム・サービスに対して持っている既存の参照のヘルスを検証することを検討してください。これは、WebSphere MQ への JMS 接続、または初期化された Kafka コンシューマーまたはプロデューサーである可能性があります。 ダウンストリーム・サービスへの内部参照の可能性をチェックする場合は、結果をキャッシュして、アプリケーションに対するヘルス・チェックの影響を最小限に抑えます。

対照的に、liveness プローブでは、失敗はプロセスの即時終了という結果になるため、検査の対象は意図的に選択されます。 状況コード `200` の `{"status": "UP"}` を常に返す単純な HTTP エンドポイントを選択することは、妥当と言えます。

### Kubernetes の readiness および liveness のサポートの追加
{: #kube-readiness-add}

[CloudNativeJS] からの [`cloud-health-connect`](https://github.com/CloudNativeJS/cloud-health-connect) ライブラリーは、liveness エンドポイントと readiness エンドポイントを Node 内に別個に定義するためのフレームワークを提供します。これにより、各エンドポイントの状態に対するソースの構成が可能になります。

## Kubernetes での readiness プローブと liveness プローブの構成
{: #kube-readiness-config}

Kubernetes デプロイメントと一緒に、readiness プローブと liveness プローブを宣言します。 どちらのプローブも同じ構成パラメーターを使用します。

* kubelet は、最初のプローブの前に `initialDelaySeconds` を待機します。

* kubelet は、`periodSeconds` 秒ごとにサービスをプローブします。 デフォルトは、1 です。

* プローブは `timeoutSeconds` 秒後にタイムアウトします。 デフォルトおよび最小値は 1 です。

* 失敗の後、`successThreshold` 回成功すると、プローブは成功です。 デフォルトおよび最小値は 1 です。liveness プローブの値は 1 でなければなりません。

* ポッドが開始し、プローブが失敗すると、Kubernetes は、再始動を `failureThreshold` 回試行した後、再始動の試行を中止します。 最小値は 1 で、デフォルト値は 3 です。
    - liveness プローブの場合、「試行中止」はポッドの再始動を意味します。
    - readiness プローブの場合、「試行中止」は、ポッドに `Unready` のマークを付けることを意味します。

再始動のサイクルを回避するには、`livenessProbe.initialDelaySeconds` を、サービスの初期化にかかる時間よりも十分に長い値に設定してください。 その後、`readinessProbe.initialDelaySeconds` にさらに短い値を使用すると、準備が整うとすぐにサービスに要求をルーティングできます。

次の構成例を参照してください。
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```
{: codeblock}

詳しくは、[Configure liveness and readiness probes (readiness プローブと liveness プローブの構成)](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)方法を参照してください。
