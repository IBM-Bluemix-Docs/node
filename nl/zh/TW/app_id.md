---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-10"

keywords: nodejs authentication, nodejs security, nodejs identity provider, nodejs cloud directory, nodejs facebook, nodejs login, nodejs social identity, add security nodejs, nodejs user authentication

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# 新增使用者鑑別
{: #authentication}

應用程式安全可能非常複雜。對於大部分開發人員而言，這是建立應用程式最困難的其中一部分。如何才能確定您正在保護使用者的資訊？藉由將 {{site.data.keyword.appid_full}} 整合至您的應用程式，即可保護資源並新增鑑別，即使您沒有太多的安全經驗也可以做到。

要求使用者登入您的應用程式，即可儲存使用者資料，例如應用程式喜好設定或公用社交設定檔。然後，您可以使用該資料來自訂應用程式內每位使用者的經驗。{{site.data.keyword.appid_short_notm}} 為您提供一個登入架構，但您也可以帶入自創品牌的登入頁面，以與 Cloud Directory 搭配使用。

如需可以使用 {{site.data.keyword.appid_short_notm}} 的所有方式的相關資訊以及架構資訊，請參閱[關於 {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-about#about)。

## 開始之前
{: #prereqs-appid}

請確定已備妥下列必要條件：
1. 您必須具有 [{{site.data.keyword.cloud}} 帳戶](https://cloud.ibm.com/registration){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
2. 安裝 [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cloud-cli-getting-started)。
3. 安裝 [npm](https://nodejs.org/en/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 套件管理支援。
4. 使用 [Express 架構](http://expressjs.com/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 實作 Node.js 伺服器。若要安裝 Express 架構，請使用指令行來開啟含有 Node.js 應用程式的目錄，然後執行下列指令：
  ```
  npm install --save express
  ```
  {: codeblock}

5. 安裝 Passport。使用指令行，開啟含有 Node.js 應用程式的目錄，然後執行下列指令：
  ```
  npm install --save passport
  ```
  {: codeblock}

  其他架構使用 `Express` 架構（例如 LoopBack）。您可以搭配使用 {{site.data.keyword.appid_short_notm}} 伺服器 SDK 與所有這些架構。{: note}

6. 找出稍後要用於 SDK 起始設定的認證金鑰值：

    * _**tenantID**_ - 承租戶 ID 是用來起始設定應用程式的唯一 ID。您可以按一下**服務認證**標籤中的**檢視認證**，以在 {{site.data.keyword.appid_short_notm}} 服務儀表板中找到值。
    * _**clientID**_、_**secret**_、_**oauth-server-url**_ - 您可以在服務儀表板的**服務認證**標籤中按一下**檢視認證**，以找到這些值。
    * _**CALLBACK_URL**_ - 使用者在登入之後看到之頁面的 URL。
    * _**redirectUri**_ - 可以透過三種方式提供 `redirectUri` 值：
      * 在新的 `WebAppStrategy({redirectUri: "...."})`
      * 作為名稱為 `redirectUri` 的環境變數。 
      * 如果未提供 `redirectUri` 值，則 App ID SDK 會擷取在 {{site.data.keyword.cloud_notm}} 上執行之應用程式的 `application_uri`，並加上預設字尾 `/ibm/cloud/appid/callback`。

## 步驟 1. 建立 {{site.data.keyword.appid_short_notm}} 實例
{: #create-instance-appid}

佈建服務的實例：

1. 在 [{{site.data.keyword.cloud_notm}} 型錄](https://cloud.ibm.com/catalog){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中，選取 **Web 及行動**種類，然後按一下 {{site.data.keyword.appid_short_notm}}。即會開啟服務配置頁面。
2. 提供服務實例的名稱，或使用預設名稱。
3. 選取定價方案，然後按一下**建立**。

## 步驟 2. 安裝 SDK
{: #install-appid}

1. 使用指令行，開啟含有 Node.js 應用程式的目錄。
2. 安裝 {{site.data.keyword.appid_short_notm}} 服務。
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## 步驟 3. 起始設定 SDK
{: #initialize-appid}

1. 將下列 `require` 定義新增至 `server.js` 檔案。
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. 設定 express 應用程式，以使用 express-session 中介軟體。**附註**：對於正式作業環境，您必須配置具有適當階段作業儲存空間的中介軟體。如需相關資訊，請參閱 [expressjs 文件](https://github.com/expressjs/session){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
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

3. 傳遞`承租戶 ID` 及認證，以起始設定 SDK。
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

    如果需要協助尋找應用程式的認證金鑰值，請查看[開始之前](#prereqs-appid)區段中的*步驟 5*，以瞭解在何處尋找這些值的相關詳細資料。
    {: tip}

4. 使用序列化及解除序列化來配置通行證。跨 HTTP 要求的已鑑別階段作業持續性需要此配置步驟。如需相關資訊，請參閱[通行證文件](http://www.passportjs.org/docs/){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. 將下列程式碼新增至 `server.js` 檔案，以發出服務重新導向：
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  服務會依下列順序重新導向：
  1. 觸發鑑別處理程序之要求的原始 URL 會持續保存在 HTTP 階段作業的 `WebAppStrategy.ORIGINAL_URL` 金鑰下。
  2. 成功重新導向，如 `passport.authenticate(name, {successRedirect: "...."})`.
  3. 應用程式根目錄 ("/")。

如需相關資訊，請參閱 [{{site.data.keyword.appid_short_notm}} Node.js GitHub 儲存庫 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。

## 步驟 4. 管理登入經驗
{: #manage-signin-appid}

<!--The following content is similar to https://test.cloud.ibm.com/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} 提供登入小組件，讓您提供使用者安全登入選項。

將應用程式配置為使用身分提供者時，登入小組件會將應用程式的訪客導向登入頁面。依預設，只有一個提供者設為**開啟**時，才會將訪客重新導向至該身分提供者的鑑別頁面。使用登入小組件，您可以顯示預設登入頁面，或者，使用雲端目錄，您可以重複使用現有的使用者介面。

身分提供者會為您的使用者提供鑑別資訊，如此您就可以授權給他們。使用 {{site.data.keyword.appid_short_notm}}，您可以使用社交身分提供者（例如 Facebook 及 Google+），或者您可以使用 Cloud Directory 來管理使用者登錄。

您可以隨時更新登入流程，而無需以任何方式變更您的原始碼！
{: tip}

服務會使用 `OAuth 2` 授權類型來對映授權處理程序。當您配置社交身分提供者（例如 Facebook）時，會使用 [Oauth2 授權流程](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 來呼叫登入小組件。當您顯示自己的使用者介面頁面時，會使用[資源擁有者密碼認證流程](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 來登入並取得存取及身分記號。

在您配置[社交身分提供者](/docs/services/appid?topic=appid-social#social)及 [Cloud Directory](/docs/services/appid?topic=appid-cloud-directory#cloud-directory) 的設定之後，就可以開始實作程式碼。

### 配置社交身分提供者
{: #social-identity-appid}

若要配置社交身分提供者，請完成下列步驟：

1. 開啟 {{site.data.keyword.appid_short_notm}} 儀表板的**身分提供者 > 管理**。
2. 將您要使用的身分提供者設為**開啟**。您可以使用任意組合的身分提供者，但如果您要帶入自訂的登入頁面，則只需要啟用雲端目錄。
3. 將[預設配置](/docs/services/appid?topic=appid-social#social)更新為您自己的認證。{{site.data.keyword.appid_short_notm}} 提供可用來試用服務的 IBM 認證。在您發佈應用程式之前，必須先更新配置。
4. [自訂預先配置的登入頁面](#login-widget)，以顯示您選擇的影像及顏色。

### 配置雲端目錄
{: #cloud-directory-appid}

使用 {{site.data.keyword.appid_short_notm}}，您可以管理自己的使用者登錄，稱為雲端目錄。雲端目錄可讓使用者使用其電子郵件及密碼，來註冊並登入行動及 Web 應用程式。

若要配置雲端目錄，請參閱[雲端目錄](/docs/services/appid?topic=appid-cloud-directory#cloud-directory)。

### 自訂預設登入頁面
{: #login-widget-appid}

您可以自訂預先配置的登入頁面，以顯示您選擇的標誌及顏色。

若要自訂頁面，請執行下列動作：

1. 開啟 {{site.data.keyword.appid_short_notm}} 服務儀表板。
2. 選取**登入自訂**區段。您可以修改登入小組件的外觀，以符合公司品牌。
3. 選取本端系統中的 PNG 或 JPG 檔案，以上傳公司的標誌。建議的影像大小是 320 x 320 像素。檔案大小上限是 100 KB。
4. 從顏色選取器中選取小組件的標頭顏色，或輸入另一個顏色的十六進位碼。
5. 檢查預覽窗格，然後在滿意自訂時按一下**儲存變更**。即會顯示一則確認訊息。
6. 在瀏覽器中，重新整理登入頁面，以驗證您的變更。

### 顯示預設頁面
{: #default-pages-appid}

如果您沒有自己的使用者介面頁面可供顯示，則 {{site.data.keyword.appid_short_notm}} 會提供您可以呼叫的預設登入頁面。

視您的身分提供者配置而定，您可以顯示的頁面有所不同。服務不會提供社交身分提供者的進階功能，因為我們絕不會存取使用者的帳戶資訊。使用者必須移至身分提供者，才能管理其資訊。例如，如果他們想要變更其 Facebook 密碼，則必須前往 [https://www.facebook.com](https://www.facebook.com){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")。

請參閱下表，以查看您可以針對每種類型的身分提供者顯示的頁面。

| 顯示頁面 | 社交身分提供者 | 雲端目錄 | 
|:-----|:-----|:-----|
| 登入 | <img src="images/confirm.png" width="32" alt="可用特性" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="可用特性" style="width:32px;" /> |
| 註冊 |  | <img src="images/confirm.png" width="32" alt="可用特性" style="width:32px;" /> |
| 忘記密碼 |  | <img src="images/confirm.png" width="32" alt="可用特性" style="width:32px;" /> |
| 變更密碼 |  | <img src="images/confirm.png" width="32" alt="可用特性" style="width:32px;" /> |
| 帳戶詳細資料 |  | <img src="images/confirm.png" width="32" alt="可用特性" style="width:32px;" /> |
{: row-headers}
{: class="comparison-table"}
{: caption="表格比較。顯示社交身分提供者和雲端目錄的頁面。" caption-side="bottom"}
{: summary="This table has row and column headers. The row headers identify the account pages that can be displayed. The column headers identify which identity service can display them. To understand where an account page can be displayed in the table, navigate to the row for the account page, and find the column for the identity service that you are interested in."}

若要顯示預設頁面，請執行下列動作：

1. 開啟 {{site.data.keyword.appid_short_notm}} 儀表板的**管理身分提供者**標籤，然後將雲端目錄設為**開啟**。
2. 配置[目錄及訊息設定](/docs/services/appid?topic=appid-cloud-directory#cloud-directory)。
3. 選擇您要顯示的登入頁面組合，並在應用程式中放置程式碼以呼叫那些頁面。

####  登入 

1. 在身分提供者設定中，將雲端目錄設為**開啟**，並指定回呼端點。
2. 將貼文路徑新增至可使用使用者名稱和密碼參數來呼叫的應用程式，並使用資源擁有者密碼登入。
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy` 可讓使用者利用使用者名稱和密碼來登入您的 Web 應用程式。登入成功之後，使用者的存取記號會儲存在 HTTP 階段作業中，並且可在階段作業期間使用。在 HTTP 階段作業損毀或過期之後，記號無效。
  {: tip}

####  註冊 

1. 在雲端目錄設定中，將**容許使用者註冊及重設其密碼**設為**開啟**。如果設為否，則處理程序會結束，而不會擷取存取及身分記號。
2. 傳遞 WebAppStrategy `show` 內容，並將其設為 `WebAppStrategy.SIGN_UP`。
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

####  忘記密碼 

1. 在雲端目錄設定中，將**容許使用者註冊及重設其密碼**及**忘記密碼電子郵件**設為**開啟**。如果設為否，則處理程序會結束，而不會擷取存取及身分記號。
2. 傳遞 WebAppStrategy `show` 內容，並將其設為 `WebAppStrategy.FORGOT_PASSWORD`。
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

####  變更密碼 

1. 在雲端目錄設定中，將**容許使用者註冊及重設其密碼**設為**開啟**。
2. 傳遞 WebAppStrategy `show` 內容，並將其設為 `WebAppStrategy.CHANGE_PASSWORD`。
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

####  帳戶詳細資料 

{: #default-account-details}
1. 在雲端目錄設定中，將**容許使用者註冊及重設其密碼**設為**開啟**
2. 傳遞 WebAppStrategy `show` 內容，並將其設為 `WebAppStrategy.CHANGE_DETAILS`。
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

想要向您的使用者提供自訂經驗嗎？請參閱 [{{site.data.keyword.appid_short_notm}} 文件](/docs/services/appid?topic=appid-login-widget#branding)，以查看如何顯示您自己的使用者介面頁面。
{: tip}

## 步驟 5. 測試應用程式
{: #test-appid}

所有項目都正確設定嗎？您可以測試看看！

1. 安裝相依關係，並啟動應用程式伺服器。
2. 使用 GUI，逐步進行登入應用程式的處理程序。如果您已配置雲端目錄，請確定所有頁面都以您想要的方式顯示。
3. 更新 {{site.data.keyword.appid_short_notm}} 儀表板中的身分提供者或登入小組件頁面。按一下**檢閱活動**，以查看已發生的鑑別事件。
4. 重複步驟 1 和 2，查看變更是否立即實作。不需要更新應用程式程式碼。

有困難嗎？請參閱[疑難排解 {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-troubleshooting#troubleshooting)。

## 後續步驟
{: #next-appid notoc}

做得好！您已將鑑別步驟新增至應用程式。嘗試下列其中一個選項，以保持動力：

* 若要瞭解有關 {{site.data.keyword.appid_short_notm}} 提供的所有特性的更多資訊並利用這些特性，請[查看文件](/docs/services/appid?topic=appid-getting-started)。
* 入門範本套件是使用 {{site.data.keyword.cloud}} 功能最快的方式之一。請檢視[行動開發人員儀表板 ](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示") 中的可用入門範本套件。下載程式碼。執行應用程式！
