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
{:note: .note}

# 添加用户认证
{: #authentication}

应用程序安全性复杂程度之深令人难以置信。对于大多数开发者而言，这是创建应用程序中最困难的其中一个部分。如何确信您保护了用户的信息呢？通过将 {{site.data.keyword.appid_full}} 集成到应用程序中，可以保护资源和添加认证，即便您没有太多安全经验也能做到。

通过要求用户登录到应用程序，可以存储用户数据，例如应用程序首选项或公共社交概要文件。然后，可以使用这些数据来定制每个用户在应用程序中的体验。{{site.data.keyword.appid_short_notm}} 提供了登录框架，但您也可以将自己的品牌登录页面与 Cloud Directory 配合使用。

有关可以使用 {{site.data.keyword.appid_short_notm}} 和体系结构信息的所有方式的更多信息，请参阅[关于 {{site.data.keyword.appid_short_notm}}](/docs/services/appid/about.html)。

## 开始之前
{: #prereqs-appid}

确保以下先决条件准备就绪：
1. 您必须具有 [{{site.data.keyword.cloud}} 帐户 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}。
2. 安装 [{{site.data.keyword.cloud_notm}} CLI](/docs/cli/index.html)。
3. 安装 [npm ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://nodejs.org/){: new_window} 软件包管理支持。
4. 使用 [Express 框架 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](http://expressjs.com/){: new_window} 实现 Node.js 服务器。要安装 Express 框架，请使用命令行打开包含 Node.js 应用程序的目录，然后运行以下命令：
  ```
  npm install --save express
  ```
  {: codeblock}

5. 安装 Passport。使用命令行打开包含 Node.js 应用程序的目录，然后运行以下命令：
  ```
  npm install --save passport
  ```
  {: codeblock}

  **注**：其他框架会使用 `Express` 框架，例如 LoopBack。可以将 {{site.data.keyword.appid_short_notm}} 服务器 SDK 与其中任意框架一起使用。

6. 找到稍后要用于 SDK 初始化的凭证密钥值：

    * _**tenantID**_ - 租户标识是用于初始化应用程序的唯一标识。在 {{site.data.keyword.appid_short_notm}} 服务仪表板中，通过单击**服务凭证**选项卡中的**查看凭证**，可以找到该值。
    * _**clientID**_、_**secret**_ 和 _**oauth-server-url**_ - 可以通过单击服务仪表板的**服务凭证**选项卡中的**查看凭证**来找到这些值。
    * _**CALLBACK_URL**_ - 用户登录后看到的页面的 URL。
    * _**redirectUri**_ - 可以通过三种方式提供 `redirectUri` 值：
      * 在新的 `WebAppStrategy({redirectUri: "...."})`
      * 作为名为 `redirectUri` 的环境变量提供。 
      * 如果未提供 `redirectUri` 值，那么 App ID SDK 将检索在 {{site.data.keyword.cloud_notm}} 上运行的应用程序的 `application_uri`，然后附加缺省后缀 `/ibm/cloud/appid/callback`。

## 步骤 1. 创建 {{site.data.keyword.appid_short_notm}} 的实例
{: #create-instance-appid}

**供应服务的实例**
1. 在 [{{site.data.keyword.cloud_notm}} 目录](https://cloud.ibm.com/catalog/)中，选择 **Web 和移动**类别，然后单击 {{site.data.keyword.appid_short_notm}}。这将打开服务配置页面。
2. 为服务实例提供名称或使用预设名称。
3. 选择价格套餐，然后单击**创建**。

## 步骤 2. 安装 SDK
{: #install-appid}

1. 使用命令行打开包含 Node.js 应用程序的目录。
2. 安装 {{site.data.keyword.appid_short_notm}} 服务。
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## 步骤 3. 初始化 SDK
{: #initialize-appid}

1. 将以下 `require` 定义添加到 `server.js` 文件。
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. 将 express 应用程序设置为使用 express-session 中间件。**注**：必须针对生产环境为中间件配置合适的会话存储量。有关更多信息，请参阅 [expressjs 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/expressjs/session){: new_window}。
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

3. 传递 `tenant ID` 和凭证以初始化 SDK。
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

    如果需要帮助查找应用程序的凭证密钥值，请查看[开始之前](/docs/node/app_id.html#prereqs-appid)部分中的*步骤 5*，以了解有关在何处查找这些值的详细信息。
    {: tip}

4. 为 Passport 配置序列化和反序列化。为了实现跨 HTTP 请求的认证会话持久性，此配置步骤是必需的。有关更多信息，请参阅 [passport 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](http://passportjs.org/docs){: new_window}。
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. 将以下代码添加到 `server.js` 文件以发出服务重定向：
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  **注**：服务将按以下顺序重定向：
  1. 触发认证过程的请求的原始 URL 将持久存储在 `WebAppStrategy.ORIGINAL_URL` 键下的 HTTP 会话中。
  2. 成功重定向，如 `passport.authenticate(name, {successRedirect: "...."})`.
  3. 应用程序根目录（“/”）。

有关更多信息，请参阅 [{{site.data.keyword.appid_short_notm}}Node.js GitHub 存储库 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window}。

## 步骤 4. 管理登录体验
{: #manage-signin-appid}

<!--The following content is similar to https://test.cloud.ibm.com/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} 提供了一个登录窗口小部件，供您为用户提供安全登录选项。

应用程序配置为使用身份提供者时，对该应用程序的访问者将通过登录窗口小部件定向到登录页面。缺省情况下，当只有一个提供者设置为**开启**时，会将访问者重定向到该身份提供者的认证页面。借助登录窗口小部件，可以显示缺省登录页面，或者通过 Cloud Directory，可以复用现有 UI。

身份提供者会为您的用户提供认证信息，以便您可以对其进行授权。通过 {{site.data.keyword.appid_short_notm}}，可以使用社交身份提供者（例如，Facebook 和 Google+），也可以使用 Cloud Directory 来管理用户注册表。

您可以随时更新登录流程，而无需以任何方式更改源代码！
{: tip}

此服务使用 `OAuth 2` 授权类型映射授权过程。配置社交身份提供者（例如 Facebook）时，将使用 [OAuth2 授权流程 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} 来调用登录窗口小部件。显示自己的 UI 页面时，将使用[资源所有者密码凭证流程 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} 来登录并获取访问和身份令牌。

配置[社交身份提供者](/docs/services/appid/identity-providers.html)和[云目录](/docs/services/appid/cloud-directory.html)的设置后，即可以开始实现代码。

### 配置社交身份提供者
{: #social-identity-appid}

要配置社交身份提供者，请完成以下步骤：

1. 打开 {{site.data.keyword.appid_short_notm}} 仪表板中的**身份提供者 > 管理**。
2. 将要使用的身份提供者设置为**开启**。可以使用身份提供者的任何组合，但如果要启动定制登录页面，请仅启用云目录。
3. 将[缺省配置](/docs/services/appid/identity-providers.html)更新为您自己的凭证。{{site.data.keyword.appid_short_notm}} 提供了可用于试用服务的 IBM 凭证。发布应用程序之前，必须更新该配置。
4. [定制预配置的登录页面](#login-widget)以显示您选择的图像和颜色。

### 配置云目录
{: #cloud-directory-appid}

通过 {{site.data.keyword.appid_short_notm}}，您可以管理自己的用户注册表（称为云目录）。通过云目录，用户可以使用自己的电子邮件和密码登录到移动和 Web 应用程序。

要配置云目录，请参阅[云目录](/docs/services/appid/cloud-directory.html)。

### 定制缺省登录页面
{: #login-widget-appid}

您可以定制预配置的登录页面，以显示您选择的徽标和颜色。

要定制页面，请执行以下操作：

1. 打开 {{site.data.keyword.appid_short_notm}} 服务仪表板。
2. 选择**登录定制**部分。您可以修改登录窗口小部件的外观，使其与您公司的品牌保持一致。
3. 通过从本地系统中选择 PNG 或 JPG 文件来上传您公司的徽标。建议的图像大小为 320 x 320 像素。最大文件大小为 100 Kb。
4. 从颜色选取器中选择窗口小部件的标题颜色，或者输入其他颜色的十六进制代码。
5. 检查预览窗格，对定制满意后，单击**保存更改**。此时将显示确认消息。
6. 在浏览器中，刷新登录页面以验证您的更改。

### 显示缺省页面
{: #default-pages-appid}

{{site.data.keyword.appid_short_notm}} 提供了缺省登录页面，如果您没有自己的 UI 页面要显示，那么可以调用缺省登录页面。

根据您的身份提供者配置，可以显示的页面会有所不同。此服务不提供社交身份提供者的高级功能，因为我们从不具有用户帐户信息的访问权。用户必须转至相应的身份提供者来管理自己的信息。例如，如果用户想要更改其 Facebook 密码，那么必须转至 [https://www.facebook.com](https://www.facebook.com)。

查看下表以了解针对每种类型的身份提供者可以显示哪些页面。

|显示页|社交身份提供者|云目录| 
|:-----|:-----|:-----|
|登录| <img src="images/confirm.png" width="32" alt="功能可用" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="功能可用" style="width:32px;" /> |
|注册|  | <img src="images/confirm.png" width="32" alt="功能可用" style="width:32px;" /> |
|忘记密码|  | <img src="images/confirm.png" width="32" alt="功能可用" style="width:32px;" /> |
|更改密码|  | <img src="images/confirm.png" width="32" alt="功能可用" style="width:32px;" /> |
|帐户详细信息|  | <img src="images/confirm.png" width="32" alt="功能可用" style="width:32px;" /> |

要显示缺省页面，请执行以下操作：

1. 打开 {{site.data.keyword.appid_short_notm}} 仪表板中的**管理身份提供者**选项卡，并将云目录设置为**开启**。
2. 配置[目录和消息设置](/docs/services/appid/cloud-directory.html)。
3. 选择要显示的登录页面的组合，并在应用程序中放入用于调用这些页面的代码。

**登录**
1. 在身份提供者设置中，将云目录设置为**开启**，然后指定回调端点。
2. 将发布路径添加到应用程序（该应用程序可以使用用户名和密码参数进行调用），并使用资源所有者密码登录。
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // 允许闪存消息
   }));
  ```
  {: codeblock}

  `WebAppStrategy` 允许用户使用用户名和密码登录到 Web 应用程序。成功登录后，用户的访问令牌会存储在 HTTP 会话中，并在整个会话过程中可用。在 HTTP 会话被销毁或到期后，该令牌即无效。
  {: tip}

**注册**
1. 在云目录设置中，将**允许用户注册并重置密码**设置为**开启**。如果设置为“否”，那么到该流程结束也不会检索访问令牌和身份令牌。
2. 传递 WebAppStrategy `show` 属性并将其设置为 `WebAppStrategy.SIGN_UP`。
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

**忘记密码**
1. 在云目录设置中，将**允许用户注册并重置密码**和**忘记密码电子邮件**设置为**开启**。如果设置为“否”，那么到该流程结束也不会检索访问令牌和身份令牌。
2. 传递 WebAppStrategy `show` 属性并将其设置为 `WebAppStrategy.FORGOT_PASSWORD`。
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

**更改密码**
1. 在云目录设置中，将**允许用户注册并重置密码**设置为**开启**。
2. 传递 WebAppStrategy `show` 属性并将其设置为 `WebAppStrategy.CHANGE_PASSWORD`。
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

**帐户详细信息**
{: #default-account-details}
1. 在云目录设置中，将**允许用户注册并重置密码**设置为**开启**。
2. 传递 WebAppStrategy `show` 属性并将其设置为 `WebAppStrategy.CHANGE_DETAILS`。
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

想要为用户提供定制体验？请查看 [{{site.data.keyword.appid_short_notm}} 文档](/docs/services/appid/login-widget.html#branding)，以了解如何显示您自己的 UI 页面。
{: tip}

## 步骤 5. 测试应用程序
{: #test-appid}

是否一切设置正确？您可以进行测试！

1. 安装依赖项并启动应用程序服务器。
2. 使用 GUI 完成登录到应用程序的过程。如果已配置云目录，请确保所有页面均按您所需的方式显示。
3. 更新 {{site.data.keyword.appid_short_notm}} 仪表板中的身份提供者或登录窗口小部件页面。单击**复查活动**以查看所发生的认证事件。
4. 重复步骤 1 和 2 以查看更新是否会立即实现。不需要对应用程序代码进行更新。

遇到困难？请查看[有关 {{site.data.keyword.appid_short_notm}} 的故障诊断](/docs/services/appid/ts_index.html)。

## 后续步骤
{: #next-appid notoc}

太棒了！您已将认证步骤添加到应用程序。请一鼓作气尝试下列其中一个选项：

* 要了解有关 {{site.data.keyword.appid_short_notm}} 提供的所有功能的更多信息并利用这些功能，请[查看文档](/docs/services/appid/index.html)！
* 入门模板工具包是使用 {{site.data.keyword.cloud}} 的功能的最快方法之一。请在[移动开发者仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} 中查看可用的入门模板工具包。下载代码。运行应用程序！
