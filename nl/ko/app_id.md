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

# 사용자 인증 추가
{: #authentication}

애플리케이션 보안은 매우 복잡할 수 있습니다. 대부분의 개발자에게 이는 앱을 작성하는 데 있어 가장 어려운 부분 중 하나입니다. 어떻게 하면 확실하게 사용자의 정보를 보호할 수 있을까요? {{site.data.keyword.appid_full}}를 앱과 통합하면 보안 분야에 대한 경험이 많지 않더라도 리소스를 보호하고 인증을 추가할 수 있습니다.

사용자가 앱에 로그인하도록 요구하면 앱 환경 설정 또는 공개 소셜 프로파일과 같은 사용자 데이터를 저장할 수 있습니다. 그 후 이러한 데이터를 사용하여 각 사용자의 앱 내 경험을 사용자 정의할 수 있습니다. {{site.data.keyword.appid_short_notm}}가 로그인 프레임워크를 제공하지만, 사용자가 직접 클라우드 디렉토리와 함께 사용할 자체 브랜드 로그인 페이지를 제공할 수도 있습니다.

{{site.data.keyword.appid_short_notm}}의 모든 사용 방법에 대한 자세한 정보 및 아키텍처 정보는 [{{site.data.keyword.appid_short_notm}} 정보](/docs/services/appid?topic=appid-about#about)를 참조하십시오.

## 시작하기 전에
{: #prereqs-appid}

다음 전제조건이 준비되어 있어야 합니다.
1. [{{site.data.keyword.cloud}} 계정 ](https://cloud.ibm.com/registration){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")이 있어야 합니다.
2. [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cloud-cli-getting-started)를 설치하십시오.
3. [npm ](https://nodejs.org/en/){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘") 패키지 관리 지원을 설치하십시오.
4. [Express 프레임워크 ](http://expressjs.com/){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")를 사용하여 Node.js 서버를 구현하십시오. Express 프레임워크를 설치하려면 명령행을 사용하여 Node.js 앱이 있는 디렉토리를 열고 다음 명령을 실행하십시오.
  ```
  npm install --save express
  ```
  {: codeblock}

5. Passport를 설치하십시오. 명령행을 사용하여 Node.js 앱이 있는 디렉토리를 열고 다음 명령을 실행하십시오.
  ```
  npm install --save passport
  ```
  {: codeblock}

  다른 프레임워크는 LoopBack과 같은 `Express` 프레임워크를 사용합니다. {{site.data.keyword.appid_short_notm}} 서버 SDK는 이러한 모든 프레임워크와 함께 사용할 수 있습니다.
  {: note}

6. 나중에 SDK 초기화에 사용될 인증 정보 키 값을 찾으십시오.

    * _**tenantID**_ - 테넌트 ID는 앱을 초기화하는 데 사용되는 고유 ID입니다. 이 값은 {{site.data.keyword.appid_short_notm}} 서비스 대시보드의 **서비스 인증 정보** 탭에서 **인증 정보 보기**를 클릭하여 찾을 수 있습니다.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_ - 이 값들은 서비스 대시보드의 **서비스 인증 정보** 탭에서 **인증 정보 보기**를 클릭하여 찾을 수 있습니다.
    * _**CALLBACK_URL**_ - 사용자가 로그인한 후 보게 되는 페이지의 URL입니다.
    * _**redirectUri**_ - `redirectUri` 값은 세 가지 방식으로 제공될 수 있습니다.
      * 새 `WebAppStrategy({redirectUri: "...."})`
      * `redirectUri`라는 환경 변수로. 
      * `redirectUri` 값이 제공되지 않은 경우 App ID SDK는 {{site.data.keyword.cloud_notm}}에서 실행 중인 애플리케이션의 `application_uri`를 검색하고 기본 접미부 `/ibm/cloud/appid/callback`을 추가합니다.

## 1단계. {{site.data.keyword.appid_short_notm}}의 인스턴스 작성
{: #create-instance-appid}

서비스의 인스턴스를 프로비저닝하십시오.

1. [{{site.data.keyword.cloud_notm}} 카탈로그](https://cloud.ibm.com/catalog){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")에서 **웹 및 모바일** 카테고리를 선택하고 {{site.data.keyword.appid_short_notm}}를 클릭하십시오. 서비스 구성 페이지가 열립니다.
2. 서비스 인스턴스에 이름을 지정하거나 사전 설정된 이름을 사용하십시오.
3. 가격 플랜을 선택하고 **작성**을 클릭하십시오.

## 2단계. SDK 설치
{: #install-appid}

1. 명령행을 사용하여 Node.js 앱이 있는 디렉토리를 여십시오.
2. {{site.data.keyword.appid_short_notm}} 서비스를 설치하십시오.
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## 3단계. SDK 초기화
{: #initialize-appid}

1. 다음 `require` 정의를 `server.js` 파일에 추가하십시오.
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. express-session 미들웨어를 사용하도록 Express 앱을 설정하십시오. **참고**: 프로덕션 환경에 적합한 세션 스토리지를 포함하는 미들웨어를 구성해야 합니다. 자세한 정보는 [expressjs 문서 ](https://github.com/expressjs/session){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")를 참조하십시오.
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

3. `tenant ID` 및 인증 정보를 전달하여 SDK를 초기화하십시오.
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

    앱의 인증 정보 키 값을 찾는 데 대해 도움이 필요한 경우에는 [시작하기 전에](#prereqs-appid) 섹션의 *5단계*에서 이를 찾을 수 있는 위치에 대한 세부사항을 확인하십시오.
    {: tip}

4. Passport의 직렬화 및 역직렬화를 구성하십시오. 이 구성 단계는 HTTP 요청 간의 인증된 세션 지속성을 위해 필요합니다. 자세한 정보는 [Passport 문서 ](http://www.passportjs.org/docs/){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")를 참조하십시오.
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. `server.js` 파일에 다음 코드를 추가하여 서비스 경로 재지정을 실행하십시오.
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  서비스는 다음 순서로 경로 재지정됩니다.
  1. 인증 프로세스를 트리거한 요청의 원래 URL은 HTTP 세션의 `WebAppStrategy.ORIGINAL_URL` 키에 지속됩니다.
  2. `passport.authenticate(name, {successRedirect: "...."})`.
  3. 애플리케이션 루트 디렉토리("/").

자세한 정보는 [{{site.data.keyword.appid_short_notm}} Node.js GitHub 저장소 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")를 참조하십시오.

## 4단계. 로그인 경험 관리
{: #manage-signin-appid}

<!--The following content is similar to https://test.cloud.ibm.com/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}}에서는 사용자에게 안전한 로그인 옵션을 제공하기 위한 로그인 위젯을 제공합니다.

앱이 ID 제공자를 사용하도록 구성된 경우 앱의 방문자는 로그인 위젯에 의해 로그인 페이지로 경로 지정됩니다. 기본적으로 하나의 제공자만 **켜짐**으로 설정된 경우 방문자는 해당 ID 제공자의 인증 페이지로 경로 재지정됩니다. 로그인 위젯을 사용하면 기본 로그인 페이지를 표시할 수 있으며, 클라우드 디렉토리를 사용하면 기존 UI를 재사용할 수 있습니다.

ID 제공자는 사용자에게 권한을 부여할 수 있도록 사용자에 대한 인증 정보를 제공합니다. {{site.data.keyword.appid_short_notm}}를 사용하면 Facebook 및 Google+와 같은 소셜 ID 제공자를 사용할 수 있으며, 클라우드 디렉토리를 사용하여 사용자 레지스트리를 관리할 수 있습니다.

소스 코드를 전혀 변경하지 않고도 로그인 플로우를 언제든지 업데이트할 수 있습니다.
{: tip}

이 서비스는 권한 부여 프로세스를 맵핑하는 데 `OAuth 2` 권한 부여 유형을 사용합니다. Facebook과 같은 소셜 ID 제공자를 구성하는 경우에는 로그인 위젯을 호출하는 데 [Oauth2 권한 부여 플로우 ](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")가 사용됩니다. 자신의 고유 UI 페이지를 표시하는 경우에는 로그인하고 ID 토큰에 대한 액세스 권한을 얻는 데 [리소스 소유자 비밀번호 인증 정보 플로우 ](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")가 사용됩니다.

[소셜 ID 제공자](/docs/services/appid?topic=appid-social#social) 및 [클라우드 디렉토리](/docs/services/appid?topic=appid-cloud-directory#cloud-directory)에 대한 설정을 구성하고 나면 코드 구현을 시작할 수 있습니다.

### 소셜 ID 제공자 구성
{: #social-identity-appid}

소셜 ID 제공자를 구성하려면 다음 단계를 완료하십시오.

1. {{site.data.keyword.appid_short_notm}} 대시보드를 열고 **ID 제공자 > 관리**로 이동하십시오.
2. 사용할 ID 제공자를 **켜짐**으로 설정하십시오. 임의의 ID 제공자 조합을 사용할 수 있으나, 사용자 정의된 로그인 페이지를 제공하려는 경우에는 클라우드 디렉토리만 사용으로 설정하십시오.
3. 자신의 인증 정보로 [기본 구성](/docs/services/appid?topic=appid-social#social)을 업데이트하십시오. {{site.data.keyword.appid_short_notm}}에서는 서비스를 사용해 보는 데 사용할 수 있는 IBM 인증 정보를 제공합니다. 앱을 공개하기 전에 이 구성을 업데이트해야 합니다.
4. [사전 구성된 로그인 페이지를 사용자 정의](#login-widget)하여 원하는 이미지 및 색상을 표시하십시오.

### 클라우드 디렉토리 구성
{: #cloud-directory-appid}

{{site.data.keyword.appid_short_notm}}를 사용하면 클라우드 디렉토리라는 고유 사용자 레지스트리를 관리할 수 있습니다. 클라우드 디렉토리는 사용자가 자신의 이메일 및 비밀번호를 사용하여 모바일 및 웹 앱에 가입하고 로그인할 수 있게 해 줍니다.

클라우드 디렉토리를 구성하려면 [클라우드 디렉토리](/docs/services/appid?topic=appid-cloud-directory#cloud-directory)를 참조하십시오.

### 기본 로그인 페이지 사용자 정의
{: #login-widget-appid}

사전 구성된 로그인 페이지가 원하는 로고 및 색상을 표시하도록 사용자 정의할 수 있습니다.

페이지를 사용자 정의하려면 다음 작업을 수행하십시오.

1. {{site.data.keyword.appid_short_notm}} 서비스 대시보드를 여십시오.
2. **로그인 사용자 정의** 섹션을 선택하십시오. 자신의 회사 브랜드에 맞추어 로그인 위젯의 모양을 수정할 수 있습니다.
3. 로컬 시스템에서 PNG 또는 JPG 파일을 선택하여 회사의 로고를 업로드하십시오. 권장 이미지 크기는 320 x 320 픽셀입니다. 최대 파일 크기는 100Kb입니다.
4. 색상 선택도구에서 위젯의 헤더 색상을 선택하거나, 16진 코드를 입력하여 그 외의 색상을 선택하십시오.
5. 미리보기 분할창을 확인하고, 사용자 정의가 만족스러우면 **변경사항 저장**을 클릭하십시오. 확인 메시지가 표시됩니다.
6. 브라우저에서 로그인 페이지를 새로 고쳐 변경사항을 확인하십시오.

### 기본 페이지 표시
{: #default-pages-appid}

{{site.data.keyword.appid_short_notm}}는 표시할 고유 UI 페이지가 없는 경우 호출할 수 있는 기본 로그인 페이지를 제공합니다.

표시할 수 있는 페이지는 ID 제공자 구성에 따라 달라집니다. IBM은 사용자의 계정 정보에 액세스할 수 없으므로 이 서비스는 소셜 ID 제공자에 대한 고급 기능을 제공하지 않습니다. 사용자는 자신의 정보를 관리하려는 경우 해당 ID 제공자로 이동해야 합니다. 예를 들어, Facebook 비밀번호를 변경하려는 경우에는 [https://www.facebook.com](https://www.facebook.com){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")으로 이동해야 합니다.

각 ID 제공자 유형에 대해 표시할 수 있는 페이지를 알아보려면 다음 표를 참조하십시오.

| 페이지 표시 | 소셜 ID 제공자 | 클라우드 디렉토리 | 
|:-----|:-----|:-----|
| 로그인 | <img src="images/confirm.png" width="32" alt="기능 사용 가능" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="기능 사용 가능" style="width:32px;" /> |
| 가입 |  | <img src="images/confirm.png" width="32" alt="기능 사용 가능" style="width:32px;" /> |
| 비밀번호 찾기 |  | <img src="images/confirm.png" width="32" alt="기능 사용 가능" style="width:32px;" /> |
| 비밀번호 변경 |  | <img src="images/confirm.png" width="32" alt="기능 사용 가능" style="width:32px;" /> |
| 계정 세부사항 |  | <img src="images/confirm.png" width="32" alt="기능 사용 가능" style="width:32px;" /> |
{: row-headers}
{: class="comparison-table"}
{: caption="표 비교. 소셜 ID 제공자 및 클라우드 디렉터리의 페이지를 표시합니다." caption-side="bottom"}
{: summary="This table has row and column headers. The row headers identify the account pages that can be displayed. The column headers identify which identity service can display them. To understand where an account page can be displayed in the table, navigate to the row for the account page, and find the column for the identity service that you are interested in."}

기본 페이지를 표시하려면 다음 작업을 수행하십시오.

1. {{site.data.keyword.appid_short_notm}} 대시보드를 열어 **ID 제공자 관리** 탭으로 이동한 후 클라우드 디렉토리를 **켜짐**으로 설정하십시오.
2. [디렉토리 및 메시지 설정](/docs/services/appid?topic=appid-cloud-directory#cloud-directory)을 구성하십시오.
3. 표시할 사인온 페이지의 조합을 선택한 후 자신의 애플리케이션에서 이러한 페이지를 호출하기 위한 코드를 배치하십시오.

####  로그인

1. ID 제공자 설정에서 클라우드 디렉토리를 **켜짐**으로 설정하고 콜백 엔드포인트를 지정하십시오.
2. 사용자 이름 및 비밀번호 매개변수를 사용하여 호출할 수 있는 POST 라우트를 추가하고 리소스 소유자 비밀번호를 사용하여 로그인하십시오.
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy`는 사용자가 사용자 이름 및 비밀번호를 사용하여 웹 앱에 로그인할 수 있도록 합니다. 로그인 성공 후 사용자의 액세스 토큰은 HTTP 세션에 저장되며 세션 중에 사용할 수 있습니다. 해당 HTTP 세션이 영구 삭제되거나 만료되면 해당 토큰이 무효화됩니다.
  {: tip}

#### 가입

1. 클라우드 디렉토리 설정에서 **사용자가 가입하고 비밀번호를 재설정할 수 있도록 허용**을 **켜짐**으로 설정하십시오. 아니오로 설정된 경우에는 프로세스가 액세스 및 ID 토큰을 검색하지 않고 종료됩니다.
2. WebAppStrategy `show` 특성을 전달하고 이를 `WebAppStrategy.SIGN_UP`으로 설정하십시오.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

####  비밀번호 찾기

1. 클라우드 디렉토리 설정에서 **사용자가 가입하고 비밀번호를 재설정할 수 있도록 허용** 및 **비밀번호 찾기 이메일**을 **켜짐**으로 설정하십시오. 아니오로 설정된 경우에는 프로세스가 액세스 및 ID 토큰을 검색하지 않고 종료됩니다.
2. WebAppStrategy `show` 특성을 전달하고 이를 `WebAppStrategy.FORGOT_PASSWORD`로 설정하십시오.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

####  비밀번호 변경

1. 클라우드 디렉토리 설정에서 **사용자가 가입하고 비밀번호를 재설정할 수 있도록 허용**을 **켜짐**으로 설정하십시오.
2. WebAppStrategy `show` 특성을 전달하고 이를 `WebAppStrategy.CHANGE_PASSWORD`로 설정하십시오.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

####  계정 세부사항

{: #default-account-details}
1. 클라우드 디렉토리 설정에서 **사용자가 가입하고 비밀번호를 재설정할 수 있도록 허용**을 **켜짐**으로 설정하십시오.
2. WebAppStrategy `show` 특성을 전달하고 이를 `WebAppStrategy.CHANGE_DETAILS`로 설정하십시오.
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

사용자에게 사용자 정의된 경험을 제공하려 하십니까? 자신의 고유 UI 페이지를 표시하는 방법을 보려면 [{{site.data.keyword.appid_short_notm}} 문서](/docs/services/appid?topic=appid-login-widget#branding)를 참조하십시오.
{: tip}

## 5단계. 앱 테스트
{: #test-appid}

모든 항목이 올바르게 설정되었습니까? 이는 테스트를 통해 확인할 수 있습니다.

1. 종속 항목을 설치하고 애플리케이션 서버를 시작하십시오.
2. GUI를 사용하여 애플리케이션 로그인 프로세스를 진행해 보십시오. 클라우드 디렉토리를 구성한 경우에는 자신의 모든 페이지가 의도한 대로 표시되는지 확인하십시오.
3. {{site.data.keyword.appid_short_notm}} 대시보드에서 ID 제공자 또는 로그인 위젯 페이지를 업데이트하십시오. 발생한 인증 이벤트를 보려면 **활동 검토**를 클릭하십시오.
4. 1단계 및 2단계를 반복하여 변경사항이 즉시 구현되는지 확인하십시오. 앱 코드를 업데이트할 필요는 없습니다.

문제가 있습니까? [{{site.data.keyword.appid_short_notm}} 문제점 해결](/docs/services/appid?topic=appid-troubleshooting#troubleshooting)을 참조하십시오.

## 다음 단계
{: #next-appid notoc}

잘 하셨습니다! 앱에 인증 단계를 추가했습니다. 다음 옵션 중 하나를 사용하여 계속 진행하십시오.

* {{site.data.keyword.appid_short_notm}}에서 제공하는 모든 기능에 대해 자세히 알아보고 이를 이용하려면 [문서를 확인하십시오](/docs/services/appid?topic=appid-getting-started).
* 스타터 킷은 {{site.data.keyword.cloud}}의 기능을 사용하는 가장 빠른 방법 중 하나입니다. [모바일 개발자 대시보드 ](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")에서 사용 가능한 스타터 킷을 보십시오. 코드를 다운로드하십시오. 앱을 실행하십시오!
