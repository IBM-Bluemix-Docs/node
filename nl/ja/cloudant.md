---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-10"

keywords: nodejs storage, nodejs cloudant, nodejs iam, initialize sdk nodejs, test nodejs app, dbaas nodejs, nodejs-cloudant, store documents nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# {{site.data.keyword.cloud_notm}} への文書の保管
{: #cloudant}

{{site.data.keyword.cloudantfull}} は、文書向けの Database as a Service (DBaaS) です。 JSON 形式の文書としてデータを保管します。 {{site.data.keyword.cloudant_short_notm}} は、スケーラビリティー、高可用性、耐久性を考慮に入れて構築されており、Node.js アプリケーションで使用するために簡単に構成できるようになっています。 これには、MapReduce、{{site.data.keyword.cloudant_short_notm}} Query、全文索引付け、地理情報索引付けなど、さまざまな索引付けオプションが付属しています。 複製機能により、データベース・クラスター、デスクトップ PC、モバイル・デバイス間で簡単にデータを同期させておくことができます。
{:shortdesc}

詳しくは、[{{site.data.keyword.cloudant_short_notm}} 概要](/docs/services/Cloudant/basics?topic=cloudant-ibm-cloudant-basics)を参照してください。

## 始める前に
{: #prereqs-cloudant}

以下の前提条件が整っていることを確認します。
 * [Nodejs-cloudant](https://github.com/cloudant/nodejs-cloudant){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") 2.3.0+ クライアント・ライブラリー。
 * [{{site.data.keyword.cloud}} アカウント](https://cloud.ibm.com/registration){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を持っている必要があります。
 * {{site.data.keyword.cloudant_short_notm}} にアクセスするには、[{{site.data.keyword.cloud_notm}} ダッシュボード ](https://cloud.ibm.com/resources){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") でサービスを作成し、そのサービス・インスタンスから {{site.data.keyword.cloudant_short_notm}} ダッシュボードを開始する必要があります。
 * 以下の手順で示すコード・スニペットでは、IAM 認証を使用しています。
 
### {{site.data.keyword.cloudant_short_notm}} での IAM の有効化
{: #enable_IAM-cloudant}

新規 {{site.data.keyword.cloudant_short_notm}} サービス・インスタンスは {{site.data.keyword.cloud_notm}} IAM と共にのみ使用できます。

すべての新規 {{site.data.keyword.cloudant_short_notm}} サービス・インスタンスでは、プロビジョン時に {{site.data.keyword.cloud_notm}} ID およびアクセス管理 (IAM) の使用が有効化されます。 {{site.data.keyword.cloud_notm}} カタログから新規インスタンスをプロビジョンするときに、**「IAM のみを使用」**認証方式を選択してください。 このモードは、サービス・バインディングおよび資格情報の生成で IAM 資格情報のみが提供されることを意味します。 詳しくは、[{{site.data.keyword.cloud_notm}} ID およびアクセス管理 (IAM)](/docs/services/Cloudant/guides?topic=cloudant-ibm-cloud-identity-and-access-management-iam-#ibm-cloud-identity-and-access-management-iam-) を参照してください。

## 手順 1. {{site.data.keyword.cloudant_short_notm}} インスタンスの作成
{: #create-instance-cloudant}

{{site.data.keyword.cloudant_short_notm}} のインスタンスを作成するときには、データベースも作成します。

1. {{site.data.keyword.cloud_notm}} アカウントにログインします。
2. [{{site.data.keyword.cloud_notm}} ダッシュボード](https://cloud.ibm.com/resources){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") で**「リソースの作成」**をクリックします。 {{site.data.keyword.cloud_notm}} カタログが開きます。
3. [{{site.data.keyword.cloud_notm}} カタログ](https://cloud.ibm.com/catalog){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") から**「データベース」**カテゴリーを選択し、{{site.data.keyword.cloudant_short_notm}} をクリックします。 サービス構成ページが開きます。
4. 以下のフィールドに情報を入力します。
  * **サービス名** - サービス・インスタンスの名前を入力するか、事前設定された名前を使用します。
  * **デプロイする地域/ロケーションの選択** - サービスをデプロイする地域を選択します。
  * **リソース・グループの選択** - リソース・グループを選択するか、デフォルトを受け入れます。
  * **使用可能な認証方式 (Available authentication methods)** - 認証方式として**「IAM のみを使用 (Use only IAM)」**を選択します。
5. 料金プランを選択し、**「作成」**をクリックします。 サービス・インスタンスのページが開きます。
6. サービス資格情報を作成するには、以下の手順を実行します。
  1. ナビゲーション・メニューから、**「サービス資格情報」**を選択します。
  2. **「新規資格情報」**をクリックします。 「新規資格情報の追加」ページが開きます。
  3. 「新規資格情報の追加」ページの各フィールドに入力し、**「追加」**をクリックします。 新規サービス資格情報がサービス・インスタンスに追加されます。
  4. サービス資格情報の詳細を表示するには、新規資格情報の**「アクション」**列で**「資格情報の表示」**をクリックします。
7. ナビゲーション・メニューから**「管理」**を選択し、**「Cloudant ダッシュボードの起動 (Launch Cloudant Dashboard)」**をクリックします。
8. ナビゲーション・メニューから、**「データベース」**アイコンをクリックします。
9. **「データベースの作成 (Create Database)」**をクリックし、データベース名を入力し、**「作成」**をクリックします。 データベース・ページが開きます。

{{site.data.keyword.cloud_notm}} サービスのインスタンスのプロビジョンについての関連情報は、[IBM Cloud での IBM Cloudant インスタンスの作成チュートリアル](/docs/services/Cloudant/tutorials?topic=cloudant-creating-an-ibm-cloudant-instance-on-ibm-cloud#creating-a-cloudant-nosql-db-instance-on-ibm-cloud)を参照してください。

## ステップ 2. SDK のインストール
{: #install-cloudant}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

独自の Node.js プロジェクトから始めて、この作業を依存関係として定義します。 言い換えると、{{site.data.keyword.cloudant_short_notm}} を package.json 依存関係に入れます。 SDK をインストールするには、以下のようにコマンド・ラインから [npm](https://nodejs.org/){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") パッケージ・マネージャーを使用します。
```
npm install --save @cloudant/cloudant
```
{: codeblock}

`package.json` ファイルに Cloudant パッケージが示されるようになったことに注目してください。

## ステップ 3. SDK の初期化
{: #initialize-cloudant}

アプリでの SDK の初期化後、{{site.data.keyword.cloudant_short_notm}} を使用してデータを保管できます。 接続を初期化するには、資格情報を入力し、すべての準備ができたときに実行されるコールバック関数を指定します。

1. 以下の `require` 定義を `server.js` ファイルに追加することによって、クライアント・ライブラリーをロードします。
  ```js
  var Cloudant = require('@cloudant/cloudant');
  ```
  {: codeblock}

2. 資格情報を指定することによって、クライアント・ライブラリーを初期化します。 `iamauth` プラグインを使用して、IAM API キーを持つデータベース・クライアントを作成します。 
  ```js
  var cloudant = new Cloudant({ url: 'https://examples.cloudant.com', plugins: { iamauth: { iamApiKey: 'xxxxxxxxxx' } } });
  ```
  {: codeblock}

3. 以下のコードを `server.js` ファイルに追加することによって、データベースをリストします。
  ```javascript
  cloudant.db.list(function(err, body) {
  body.forEach(function(db) {
    console.log(db);
    });
  });
  ```
  {: codeblock}

大文字の `Cloudant` は、`require ()` を使用してロードするパッケージです。 小文字の `cloudant` は、{{site.data.keyword.cloudant_short_notm}} サービスへの認証済み接続です。
{: tip}

### 基本操作によるデータの管理
{: #basic_operations-cloudant}
<!--Borrowed from https://github.com/cloudant/nodejs-cloudant/blob/master/example/crud.js-->

基本的な操作として、初期化したクライアントを使用して文書を作成、読み取り、更新、削除するアクションを以下に示します。

#### 文書の作成
```js
var createDocument = function(callback) {
  console.log("Creating document 'mydoc'");
  // specify the id of the document so you can update and delete it later
  db.insert({ _id: 'mydoc', a: 1, b: 'two' }, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

#### 文書の読み取り
```js
var readDocument = function(callback) {
  console.log("Reading document 'mydoc'");
  db.get('mydoc', function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep a copy of the doc so you know its revision token
    doc = data;
    callback(err, data);
  });
};
```
{: codeblock}

#### 文書の更新
```js
var updateDocument = function(callback) {
  console.log("Updating document 'mydoc'");
  // make a change to the document, using the copy we kept from reading it back
  doc.c = true;
  db.insert(doc, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep the revision of the update so we can delete it
    doc._rev = data.rev;
    callback(err, data);
  });
};
```
{: codeblock}

#### 文書の削除
```js
var deleteDocument = function(callback) {
  console.log("Deleting document 'mydoc'");
  // supply the id and revision to be deleted
  db.destroy(doc._id, doc._rev, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

## 手順 4. アプリのテスト
{: #test-cloudant}

すべて正しくセットアップされましたか? テストしてみましょう。

1. アプリケーションを実行して、初期化操作と各操作 (文書作成など) を開始します。
2. [{{site.data.keyword.cloud_notm}} ダッシュボード](https://cloud.ibm.com/resources){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") で、前に作成した {{site.data.keyword.cloudant_short_notm}} サービス・インスタンスをクリックします。 サービス・インスタンスが開いたら、**「Cloudant ダッシュボードの起動 (Launch Cloudant Dashboard)」**をクリックします。
3. {{site.data.keyword.cloudant_short_notm}} ダッシュボードで、新規文書を作成したデータベースを選択します。

問題がある場合は、 [{{site.data.keyword.cloudant_short_notm}} API リファレンス](/docs/services/Cloudant?topic=cloudant-api-reference-overview)を確認してください。

## 次のステップ
{: #next-cloudant notoc}

お疲れさまでした。 ある程度のセキュア・パーシスタンスをアプリに追加しました。 この調子で、以下のいずれかのオプションを試してみてください。

* [{{site.data.keyword.cloudant_short_notm}} SDK for Node.js](https://github.com/cloudant/nodejs-cloudant){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") のソース・コードを調べます。
* [データベース操作および文書操作のコード例 ](https://github.com/cloudant/nodejs-cloudant/tree/master/example){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") を確認します。
* スターター・キットは、{{site.data.keyword.cloud}} の機能を素早く使用するための 1 つの方法です。 [モバイル開発者ダッシュボード](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン") で、使用可能なスターター・キットを確認できます。 コードをダウンロードして アプリを実行してください。
* {{site.data.keyword.cloudant_short_notm}} が提供する機能のすべてについてさらに学習し、それらの機能を利用するには、[資料](/docs/services/Cloudant?topic=cloudant-getting-started)をお読みください。
