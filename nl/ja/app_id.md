---

copyright:
  years: 2018
lastupdated: "2018-10-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# ユーザー認証の追加
{: #authentication}

アプリケーション・セキュリティーは複雑過ぎて手に負えなくなることがあります。 これはほとんどの開発者にとって、アプリ作成における難題の一つとなっています。 確実にユーザー情報を保護するにはどうすればよいでしょうか? アプリに {{site.data.keyword.appid_full}} を統合することによって、セキュリティーに関する経験があまりなくても、リソースを保護し、認証を追加することができます。

ユーザーにアプリへのサインインを要求することによって、ユーザー・データ (アプリ設定や公開ソーシャル・プロファイルなど) を保管できます。 その後、そのデータを使用して、アプリ内での各ユーザーのエクスペリエンスをカスタマイズできます。 {{site.data.keyword.appid_short_notm}} にはログイン・フレームワークが用意されていますが、独自ブランドのサインイン・ページをクラウド・ディレクトリーと共に使用することもできます。

{{site.data.keyword.appid_short_notm}} の使用法とアーキテクチャーについて詳しくは、[{{site.data.keyword.appid_short_notm}} について](/docs/services/appid/about.html)を参照してください。

## 始める前に
{: #before}

以下の前提条件が整っていることを確認します。
1. [{{site.data.keyword.cloud}} アカウント ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} を持っている必要があります。
2. [{{site.data.keyword.cloud_notm}} CLI](../cli/index.html) をインストールします。
3. [npm ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://nodejs.org/){: new_window} パッケージ管理サポートをインストールします。
4. [Express フレームワーク ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](http://expressjs.com/){: new_window} を使用して Node.js サーバーを実装します。 Express フレームワークをインストールするには、コマンド・ラインを使用して、Node.js アプリのディレクトリーを開き、以下のコマンドを実行します。
  ```
  npm install --save express
  ```
  {: codeblock}

5. Passport をインストールします。 コマンド・ラインを使用して、Node.js アプリのディレクトリーを開き、以下のコマンドを実行します。
  ```
  npm install --save passport
  ```
  {: codeblock}

  **注:** 他のフレームワークは `Express` フレームワークを使用します (LoopBack など)。 {{site.data.keyword.appid_short_notm}} Server SDK は、それらのどのフレームワークでも使用可能です。

6. 後で SDK の初期化に使用される資格情報キー値を見つけます。

    * _**tenantID**_ - テナント ID は、アプリの初期化に使用される固有 ID です。 この値を入手するには、{{site.data.keyword.appid_short_notm}} サービス・ダッシュボードの**「サービス資格情報」**タブで**「資格情報の表示」**をクリックします。
    * _**clientID**_、_**secret**_、_**oauth-server-url**_ - これらの値を入手するには、サービス・ダッシュボードの**「サービス資格情報」**タブで**「資格情報の表示」**をクリックします。
    * _**CALLBACK_URL**_ - ログイン後にユーザーに表示されるページの URL。
    * _**redirectUri**_ - 以下の 3 つの方法で `redirectUri` 値を指定できます。
      * 新規 `WebAppStrategy({redirectUri: "...."})` に手動で指定する。
      * `redirectUri` という名前の環境変数として指定する。 
      * `redirectUri` 値が指定されていない場合、App ID SDK は、{{site.data.keyword.cloud_notm}} で実行されているアプリケーションの `application_uri` を取得し、デフォルトの接尾部 `/ibm/cloud/appid/callback` を付加します。

## ステップ 1. {{site.data.keyword.appid_short_notm}} のインスタンスの作成
{: #create-instance}

**サービス・インスタンスのプロビジョン**
1. [{{site.data.keyword.cloud_notm}} の「カタログ」](https://console.bluemix.net/catalog/)で、**「Web およびモバイル」**カテゴリーを選択してから、「{{site.data.keyword.appid_short_notm}}」をクリックします。サービス構成ページが開きます。
2. サービス・インスタンスに名前を付けます。または、事前設定された名前を使用します。
3. 料金プランを選択し、**「作成」**をクリックします。

## ステップ 2. SDK のインストール
{: #install}

1. コマンド・ラインを使用して、Node.js アプリのディレクトリーを開きます。
2. {{site.data.keyword.appid_short_notm}} サービスをインストールします。
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## ステップ 3. SDK の初期化
{: #initialize}

1. `server.js` ファイルに以下の `require` 定義を追加します。
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. express-session ミドルウェアを使用するように express アプリをセットアップします。 **注:** このミドルウェアには、実稼働環境用の適切なセッション・ストレージを構成する必要があります。 詳しくは、[expressjs の資料 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/expressjs/session){: new_window} を参照してください。
    ```js
    var app = express();
    app.use(session({
        secret: "123456",
        resave: true,
        saveUninitialized: true
        }));
    app.use(passport.initialize());
    app.use(passport.session());
    ```
    {: codeblock}

3. `tenant ID` および資格情報を渡して SDK を初期化します。
    ```js
    passport.use(new WebAppStrategy({
        tenantId: "{tenant-id}",
        clientId: "{client-id}",
        secret: "{secret}",
        oauthServerUrl: "{oauth-server-url}",
        redirectUri: "{app-url}" + CALLBACK_URL
    }));
    ```
    {: codeblock}

    アプリ用の資格情報のキー値を知るためにヘルプが必要な場合、『[始める前に](app_id.html#before)』セクションの*ステップ 5* で、それらの値を入手できる場所についての詳しい説明を参照してください。 
    {: tip}

4. Passport にシリアライゼーションとデシリアライゼーションを構成します。 この構成手順は、複数の HTTP 要求にわたって認証済みセッション・パーシスタンスを維持するために必要です。 詳しくは、[Passport の資料 ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](http://passportjs.org/docs){: new_window} を参照してください。
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. サービス・リダイレクトを発行するための以下のコードを `server.js` ファイルに追加します。
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  **注:** サービスは、以下の順序でリダイレクトされます。
  1. 認証プロセスを起動した要求の元の URL は、`WebAppStrategy.ORIGINAL_URL` キーの下で HTTP セッション内で永続的に保持されます。
  2. 成功したリダイレクト (`passport.authenticate(name, {successRedirect: "...."}` で指定)。
  3. アプリケーションのルート・ディレクトリー (「/」)。

詳しくは、[{{site.data.keyword.appid_short_notm}} Node.js GitHub リポジトリー ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window} を参照してください。

## ステップ 4. サインイン・エクスペリエンスの管理
{: #manage-sign-in}

<!--The following content is similar to https://console.stage1.bluemix.net/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} で用意されているログイン・ウィジェットを使用して、機密保護機能のあるサインイン・オプションをユーザーに提供できます。

ID プロバイダーを使用するようにアプリが構成されている場合、アプリへの訪問者はログイン・ウィジェットによってサインイン・ページに誘導されます。 デフォルトでは、1 つのプロバイダーのみが**オン**に設定されている場合、訪問者はその ID プロバイダーの認証ページにリダイレクトされます。 ログイン・ウィジェットを使用して、デフォルトのサインイン・ページを表示することができます。あるいは、クラウド・ディレクトリーを使用して既存の UI を再利用することもできます。

ID プロバイダーが提供するユーザー認証情報を使用して、ユーザーに権限を与えることができます。 {{site.data.keyword.appid_short_notm}} では、Facebook や Google+ などのソーシャル ID プロバイダーを使用することも、クラウド・ディレクトリーを利用してユーザー・レジストリーを管理することもできます。

ソース・コードを変更することなく、いつでもサインイン・フローを更新できます。
{: tip}

このサービスは `OAuth 2` 付与タイプを使用して許可プロセスをマップします。 Facebook などのソーシャル ID プロバイダーを構成する場合は、[Oauth2 許可付与フロー ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} を使用してログイン・ウィジェットが呼び出されます。 独自の UI ページを表示する場合は、[リソース所有者パスワード資格情報フロー ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} を使用して、ログインと、アクセス権限および ID トークンの取得が行われます。

[ソーシャル ID プロバイダー](/docs/services/appid/identity-providers.html)および[クラウド・ディレクトリー](/docs/services/appid/cloud-directory.html)の設定を構成した後、コードの実装を始めることができます。

### ソーシャル ID プロバイダーの構成
{: #social-identity}

ソーシャル ID プロバイダーを構成するには、以下のステップを実行します。

1. {{site.data.keyword.appid_short_notm}} ダッシュボードを開いて**「ID プロバイダー」>「管理」**と進みます。
2. 使用する ID プロバイダーを**「オン」**に設定します。 複数の ID プロバイダーを任意に組み合わせることができますが、カスタマイズされたサインオン・ページを使用したい場合はクラウド・ディレクトリーのみを有効にしてください。
3. [デフォルト構成](/docs/services/appid/identity-providers.html)を自分の資格情報に更新します。 {{site.data.keyword.appid_short_notm}} には、このサービスを試してみるために使用できる IBM 資格情報が用意されています。 アプリを公開する前に、構成を更新する必要があります。
4. [事前構成されたサインイン・ページをカスタマイズ](#login-widget)して、選択したイメージと色が表示されるようにします。

### クラウド・ディレクトリーの構成
{: #cloud-directory}

{{site.data.keyword.appid_short_notm}} では、クラウド・ディレクトリーという独自のユーザー・レジストリーを管理できます。 クラウド・ディレクトリーによって、モバイル・アプリおよび Web アプリにユーザーが各自の E メールとパスワードを使用して登録/サインインすることが可能になります。

クラウド・ディレクトリーを構成するには、[クラウド・ディレクトリー](/docs/services/appid/cloud-directory.html)を参照してください。

### デフォルト・サインイン・ページのカスタマイズ
{: #login-widget}

事前構成されたサインイン・ページをカスタマイズして、選択したロゴと色が表示されるようにできます。

ページをカスタマイズするには、以下のようにします。

1. {{site.data.keyword.appid_short_notm}} サービス・ダッシュボードを開きます。
2. **「ログインのカスタマイズ」**セクションを選択します。 ログイン・ウィジェットの外観を自社のブランドに合うように変更できます。
3. ローカル・システムにある PNG ファイルまたは JPG ファイルを選択し、自社のロゴをアップロードします。 推奨される画像サイズは 320 x 320 ピクセルです。 ファイルの最大サイズは 100 KB です。
4. ウィジェットのヘッダー・カラーをカラー・ピッカーから選択するか、または別のカラーの 16 進コードを入力します。
5. プレビュー・ペインでカスタマイズを検査し、問題がなければ**「変更を保存」**をクリックします。 確認メッセージが表示されます。
6. ブラウザーでログイン・ページを最新表示し、変更内容を確認します。

### デフォルト・ページの表示
{: #default-pages}

{{site.data.keyword.appid_short_notm}} には、表示する独自の UI ページがない場合に呼び出すことができる、デフォルトのログイン・ページが用意されています。

表示できるページは、ID プロバイダーの構成によって異なります。 このサービスでは、ソーシャル ID プロバイダー用の拡張機能は提供していません。ユーザーのアカウント情報へのアクセス権限がないためです。 ユーザーは、自身で ID プロバイダーにアクセスして、自分の情報を管理する必要があります。 例えば、ユーザーは、Facebook のパスワードを変更したい場合は [https://www.facebook.com](https://www.facebook.com) にアクセスする必要があります。

ID プロバイダーのタイプごとの表示できるページは次の表のとおりです。

| 表示ページ | ソーシャル ID プロバイダー | クラウド・ディレクトリー | 
|:-----|:-----|:-----|
| サインイン | <img src="images/confirm.png" width="32" alt="機能は使用可能" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="機能は使用可能" style="width:32px;" /> |
| 登録 |  | <img src="images/confirm.png" width="32" alt="機能は使用可能" style="width:32px;" /> |
| パスワードを忘れた場合 |  | <img src="images/confirm.png" width="32" alt="機能は使用可能" style="width:32px;" /> |
| パスワードの変更 |  | <img src="images/confirm.png" width="32" alt="機能は使用可能" style="width:32px;" /> |
| アカウント詳細 |  | <img src="images/confirm.png" width="32" alt="機能は使用可能" style="width:32px;" /> |

デフォルトのページを表示するには、次のようにします。

1. {{site.data.keyword.appid_short_notm}} ダッシュボードを開き、**「ID プロバイダーの管理」**タブに移動して、クラウド・ディレクトリーを**「オン」**に設定します。
2. [ディレクトリーおよびメッセージの設定](/docs/services/appid/cloud-directory.html)を構成します。
3. 表示するサインオン・ページの組み合わせを選択し、それらのページを呼び出すコードをアプリケーション内に配置します。

**サインイン**
1. ID プロバイダー設定でクラウド・ディレクトリーを**「オン」**に設定し、コールバック・エンドポイントを指定します。
2. ユーザー名とパスワードのパラメーターを使用して呼び出せる post ルートをアプリに追加し、リソース所有者のパスワードを使用してログインします。
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy` により、ユーザーはユーザー名とパスワードを使用して Web アプリにサインインできます。 ログインに成功すると、ユーザーのアクセス・トークンは HTTP セッションに保管され、セッション中は使用可能です。 HTTP セッションが破棄されるか期限切れになると、トークンは無効になります。
  {: tip}

**登録**
1. クラウド・ディレクトリーの設定で、**「登録とパスワード・リセットをユーザーに許可する」**を**「オン」**に設定します。 「いいえ」に設定した場合、プロセスはアクセス・トークンと識別トークンを取得せずに終了します。
2. WebAppStrategy に `show` プロパティーを渡し、それを `WebAppStrategy.SIGN_UP` に設定します。
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

**パスワードを忘れた場合**
1. クラウド・ディレクトリーの設定で**「登録とパスワード・リセットをユーザーに許可する」**と**「パスワードを忘れた場合の E メール」**を**「オン」**に設定します。 「いいえ」に設定した場合、プロセスはアクセス・トークンと識別トークンを取得せずに終了します。
2. WebAppStrategy に `show` プロパティーを渡し、それを `WebAppStrategy.FORGOT_PASSWORD` に設定します。
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

**パスワードの変更**
1. クラウド・ディレクトリーの設定で、**「登録とパスワード・リセットをユーザーに許可する」**を**「オン」**に設定します。
2. WebAppStrategy に `show` プロパティーを渡し、それを `WebAppStrategy.CHANGE_PASSWORD` に設定します。
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

**アカウント詳細**
{: #default-account-details}
1. クラウド・ディレクトリーの設定で、**「登録とパスワード・リセットをユーザーに許可する」**を**「オン」**に設定します。
2. WebAppStrategy に `show` プロパティーを渡し、それを `WebAppStrategy.CHANGE_DETAILS` に設定します。
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

ユーザーにカスタム・エクスペリエンスを提供したい場合は、[{{site.data.keyword.appid_short_notm}} の資料](/docs/services/appid/login-widget.html#branding)で、独自の UI ページを表示する方法についての説明を参照してください。
{: tip}

## ステップ 5. アプリのテスト
{: #test}

すべて正しくセットアップされましたか? テストしてみましょう!

1. 従属関係をインストールし、アプリケーション・サーバーを開始します。
2. GUI を使用してアプリケーションへのサインイン・プロセスをウォークスルーします。 クラウド・ディレクトリーを構成した場合は、すべてのページが意図したとおりに表示されることを確認します。
3. {{site.data.keyword.appid_short_notm}} ダッシュボードで ID プロバイダーまたはログイン・ウィジェットのページを更新します。 **「アクティビティーの確認」**をクリックして、発生した認証イベントを表示します。
4. ステップ 1 と 2 を繰り返して、変更が即時に実装されることを確認します。 アプリ・コードを更新する必要はありません。

問題がある場合、[{{site.data.keyword.appid_short_notm}} のトラブルシューティング](/docs/services/appid/ts_index.html)を参照してください。

## 次のステップ
{: #next notoc}

お疲れさまでした。 アプリに認証ステップを追加しました。 この調子で、以下のいずれかのオプションを試してみてください。

* {{site.data.keyword.appid_short_notm}} が提供する機能のすべてについてさらに学習し、それらの機能を利用するには、[資料](/docs/services/appid/index.html)をお読みください。
* スターター・キットは、{{site.data.keyword.cloud}} の機能を素早く使用するための 1 つの方法です。 [モバイル開発者ダッシュボード ![外部リンク・アイコン](../icons/launch-glyph.svg "外部リンク・アイコン")](https://console.bluemix.net/developer/mobile/dashboard){: new_window} で、使用可能なスターター・キットを確認できます。 コードをダウンロードし、アプリを実行してみてください。
