---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-30"

keywords: cos nodejs, object storage nodejs, nodejs data, file storage nodejs, ibm-cos-sdk nodejs, creating object nodejs, downloading object nodejs, static nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Object Storage での静的コンテンツの保管
{: #object-storage}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://cloud.ibm.com/docs/services/cloud-object-storage/libraries/node.html#using-node-js -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://cloud.ibm.com/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage -->

{{site.data.keyword.cos_full_notm}} は、クラウド・コンピューティングにおける基本的なコンポーネントであり、Apple 開発者およびアプリケーションに強力な機能を提供します。 ファイル階層での情報の保管 (ブロック・ストレージやファイル・ストレージなど) とは異なり、オブジェクト・ストアはファイルおよびファイルのメタデータのみで構成されます。 これらのファイルは、バケットと呼ばれる集合で保管されます。 定義上、それらのオブジェクトは変更不可能なため、イメージ、ビデオ、他の静的文書などのデータにとって最適です。 頻繁に変更されるデータやリレーショナル・データには、[{{site.data.keyword.cloudant_short_notm}}](/docs/node?topic=nodejs-cloudant) データベース・サービスを使用できます。

{{site.data.keyword.cos_short}} (COS) は、非構造化データの保管のために利用できる、柔軟でコスト効率が高く、スケーラブルなストレージ・システムです。 データには、SDK または IBM ユーザー・インターフェースを使用してアクセスできます。 {{site.data.keyword.cos_short}} を使用して、RESTful API および SDK に基づいたセルフサービス・ポータルを介して非構造化データにアクセスできます。

## 始める前に
{: #prereqs-cos}

以下の前提条件が整っていることを確認します。
1. [{{site.data.keyword.cloud}} アカウント](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を持っている必要があります。
2. [{{site.data.keyword.cos_short}} SDK for Node.js](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") が必要です。
3. Node 4.x 以降が必要です。
4. 後で SDK の初期化に使用される資格情報キー値を見つけます。

    * _**endpoint**_ - Cloud Object Storage のパブリック・エンドポイント。 このエンドポイントは、[{{site.data.keyword.cloud_notm}} ダッシュボード](https://cloud.ibm.com/dashboard/apps){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") から入手できます。
    * _**api-key**_ - サービス資格情報の作成時に生成される API キー。 作成および削除の例では、書き込み権限が必要です。
    * _**resource-instance-id**_ - Cloud Object Storage のリソース ID。 このリソース ID は [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli) または [{{site.data.keyword.cloud_notm}} ダッシュボード](https://cloud.ibm.com/dashboard/apps){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を介して入手できます。

## ステップ 1. {{site.data.keyword.cos_short}} のインスタンスの作成
{: #create-instance-cos}

1. [{{site.data.keyword.cloud_notm}} カタログ](https://cloud.ibm.com/catalog/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") から**「ストレージ」**カテゴリーを選択し、{{site.data.keyword.cos_short}} をクリックします。 サービス構成ページが開きます。
2. サービス・インスタンスに名前を付けます。または、事前設定された名前を使用します。
3. 料金プランを選択し、**「作成」**をクリックします。 Object Storage インスタンスのページが開きます。
4. ナビゲーション・メニューで**「サービス資格情報」**を選択します。
5. 「サービス資格情報」ページで**「新規資格情報」**をクリックします。
6. 「新規資格情報の追加」ページで、役割が **「ライター」** に設定されていることを確認し、**「追加」**をクリックします。 新規資格情報が作成され、「サービス資格情報」ページに表示されます。

## ステップ 2. SDK のインストール
{: #install-cos}

コマンド・ラインから [npm](https://nodejs.org/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") パッケージ・マネージャーを使用して、{{site.data.keyword.cos_short}} SDK for Node.js をインストールします。
```
npm install ibm-cos-sdk
```
{: pre}

## ステップ 3. SDK の初期化
{: #initialize-cos}

アプリでの SDK の初期化後、{{site.data.keyword.cos_short}} を使用してデータを保管できます。 資格情報を入力し、すべての準備ができたときに実行されるコールバック関数を指定することによって、接続を初期化します。

1. 以下の `require` 定義を `server.js` ファイルに追加することによって、クライアント・ライブラリーをロードします。
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. 資格情報を指定することによって、クライアント・ライブラリーを初期化します。
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

  アプリ用の資格情報のキー値を知るためにヘルプが必要な場合、『[始める前に](#prereqs-cos)』セクションの*ステップ 4* で、それらの値を入手できる場所についての詳しい説明を参照してください。
  {: tip}

3. 以下のコードを `server.js` ファイルに追加します。
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

### 基本操作によるデータの管理
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### バケットの作成
```js
function doCreateBucket() {
    console.log('Creating bucket');
    return cos.createBucket({
        Bucket: 'my-bucket',
        CreateBucketConfiguration: {
          LocationConstraint: 'us-standard'
        },
    }).promise();
}
```
{: codeblock}

#### オブジェクトの作成、アップロード、または上書き
```js
function doCreateObject() {
    console.log('Creating object');
    return cos.putObject({
        Bucket: 'my-bucket',
        Key: 'foo',
        Body: 'bar'
    }).promise();
}
```
{: codeblock}

#### オブジェクトのダウンロード
<!-- Verify this snippet with Nick when he returns from vacation -->
```js
function doGetObject() {
 console.log('Getting object');
 return cos.getObject({
   Bucket: 'my-bucket',
   Key: 'foo'
 }).createReadStream().pipe(fs.createWriteStream('./MyObject'));
}
```
{: codeblock}

#### オブジェクトの削除
```js
function doDeleteObject() {
    console.log('Deleting object');
    return cos.deleteObject({
        Bucket: 'my-bucket',
        Key: 'foo'
    }).promise();
}
```
{: codeblock}

マルチパート・アップロード、セキュリティー機能、およびその他の操作については、[完全な資料](/docs/services/cloud-object-storage?topic=cloud-object-storage-node)を参照してください。

## ステップ 4. アプリのテスト
{: #test-cos}

すべて正しくセットアップされましたか? テストしてみましょう!

1. アプリケーションを実行します。このとき、初期化操作と、それぞれの操作 (バケットの作成やバケットへのデータの追加など) を必ず開始するようにしてください。
2. Web ブラウザーで以前に作成した {{site.data.keyword.cos_short}} サービス・インスタンスに戻り、サービス・ダッシュボードを開きます。
3. 使用されるバケットを選択すると、新規に作成されたオブジェクトがダッシュボードに表示されます。

問題がある場合、[{{site.data.keyword.cos_short}} API リファレンス](/docs/services/cloud-object-storage?topic=cloud-object-storage-compatibility-api)を参照してください。

## 次のステップ
{: #next-cos notoc}

お疲れさまでした。 ある程度のセキュア・パーシスタンスをアプリに追加しました。 この調子で、以下のいずれかのオプションを試してみてください。

* [{{site.data.keyword.cos_short}} SDK for Node.js](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") のソース・コードを調べます。
* [バケット操作およびオブジェクト操作のコード例](https://github.com/ibm/ibm-cos-sdk-js#example-code){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を参照します。
* スターター・キットは、{{site.data.keyword.cloud_notm}} の機能を素早く使用するための 1 つの方法です。 [モバイル開発者ダッシュボード](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") で、使用可能なスターター・キットを確認できます。 コードをダウンロードし、アプリを実行してみてください。
* {{site.data.keyword.cos_short}} が提供する機能のすべてについてさらに学習し、それらの機能を利用するには、[資料](/docs/services/cloud-object-storage?topic=cloud-object-storage-about)をお読みください。
