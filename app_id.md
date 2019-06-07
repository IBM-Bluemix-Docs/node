---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-07"

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

# Adding user authentication
{: #authentication}

Application security can be incredibly complicated. For most developers, it's one of the hardest parts of creating an app. How can you be sure that you are protecting your user's information? By integrating {{site.data.keyword.appid_full}} into your apps, you can secure resources and add authentication, even when you don't have much security experience.

By requiring users to sign in to your app, you can store user data such as app preferences, or public social profiles. You can then use that data to customize each user's experience within the app. {{site.data.keyword.appid_short_notm}} provides a log-in framework for you, but you can also bring your own branded sign-in pages to use with Cloud Directory.

For more information about all of the ways that you can use {{site.data.keyword.appid_short_notm}} and architecture information, see [About {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-about#about).

## Before you begin
{: #prereqs-appid}

Be sure that you have the following prerequisites ready to go:
1. You must have an [{{site.data.keyword.cloud}} account](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon").
2. Install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cloud-cli-getting-started).
3. Install [npm](https://nodejs.org/en/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") package management support.
4. Implement your Node.js server with the [Express framework](http://expressjs.com/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon"). To install the Express framework, use the command line to open the directory with your Node.js app, and run the following command:
  ```
  npm install --save express
  ```
  {: codeblock}

5. Install Passport. Use the command line to open the directory with your Node.js app, and run the following command:
  ```
  npm install --save passport
  ```
  {: codeblock}

  Other frameworks use `Express` frameworks, such as LoopBack. You can use the {{site.data.keyword.appid_short_notm}} server SDK with any of these frameworks.
  {: note}

6. Locate the credential key values to be used later for SDK initialization:

    * _**tenantID**_ - The tenant ID is a unique identifier that is used to initialize your app. You can find the value in the {{site.data.keyword.appid_short_notm}} service dashboard by clicking **View Credentials** in the **Service Credentials** tab.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_ - You can find these values by clicking **View Credentials** in the **Service Credentials** tab of your service dashboard.
    * _**CALLBACK_URL**_ - The URL for the page that users see after they log in.
    * _**redirectUri**_ - The `redirectUri` value can be supplied in three ways:
      * Manually in new `WebAppStrategy({redirectUri: "...."})`
      * As an environment variable named `redirectUri`. 
      * If the `redirectUri` value isn't provided, the App ID SDK retrieves the `application_uri` of the application that is running on {{site.data.keyword.cloud_notm}}, and appends the default suffix`/ibm/cloud/appid/callback`.

## Step 1. Creating an instance of {{site.data.keyword.appid_short_notm}}
{: #create-instance-appid}

Provision an instance of the service:

1. In the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon"), select the **Web and Mobile** category, and click {{site.data.keyword.appid_short_notm}}. The service configuration page opens.
2. Give your service instance a name, or use the preset name.
3. Select your pricing plan and click **Create**.

## Step 2. Installing the SDK
{: #install-appid}

1. Using the command line, open the directory with your Node.js app.
2. Install the {{site.data.keyword.appid_short_notm}} service.
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## Step 3. Initializing the SDK
{: #initialize-appid}

1. Add the following `require` definitions to your `server.js` file.
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. Set up your express app to use express-session middleware. **Note**: You must configure the middleware with the proper session storage for production environments. For more information, see the [expressjs docs](https://github.com/expressjs/session){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon").
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

3. Pass the `tenant ID` and credentials to initialize the SDK.
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

    If you need help with finding the credential key values for your app, check *step 5* of the [Before you begin](#prereqs-appid) section for details on where to find them. 
    {: tip}

4. Configure passport with serialization and deserialization. This configuration step is required for authenticated session persistence across HTTP requests. For more information, see the [passport docs](http://www.passportjs.org/docs/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon").
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. Add the following code to your `server.js` file to issue the service redirects:
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  The service redirects in the following order:
  1. The original URL of the request that triggered the authentication process is persisted in the HTTP session under the `WebAppStrategy.ORIGINAL_URL` key.
  2. A successful redirect, as specified in `passport.authenticate(name, {successRedirect: "...."})`.
  3. The application root directory ("/").

For more information, see the [{{site.data.keyword.appid_short_notm}} Node.js GitHub repository ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon").

## Step 4. Managing the sign-in experience
{: #manage-signin-appid}

<!--The following content is similar to https://test.cloud.ibm.com/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} provides a login widget for you to give your users secure sign-in options.

When your app is configured to use an identity provider, visitors to your app are directed to a sign-in page by the login widget. By default, when only one provider is set to **On**, visitors are redirected to that identity provider's authentication page. With the login widget, you can display a default sign-in page or with cloud directory, you can reuse your existing UIs.

An identity provider provides the authentication information for your users so that you can authorize them. With {{site.data.keyword.appid_short_notm}}, you can use social identity providers such as Facebook and Google+ or you can manage a user registry with Cloud Directory.

You can update your sign-in flow at any time, without changing your source code in any way!
{: tip}

The service uses `OAuth 2` grant types to map the authorization process. When you configure social identity providers such as Facebook, the [Oauth2 Authorization Grant flow](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") is used to call the login widget. When you display your own UI pages, the [Resource Owner Password Credentials flow](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") is used to log in and gain access and identity tokens.

After you configure the settings for [social identity providers](/docs/services/appid?topic=appid-social#social) and [Cloud Directory](/docs/services/appid?topic=appid-cloud-directory#cloud-directory), you can start implementing the code.

### Configuring social identity providers
{: #social-identity-appid}

To configure social identity providers, complete the following steps:

1. Open the {{site.data.keyword.appid_short_notm}} dashboard to **Identity Providers > Manage**.
2. Set the identity providers that you want to use to **On**. You can use any combination of identity providers, but if you'd like to bring customized sign-on pages, enable cloud directory only.
3. Update the [default configuration](/docs/services/appid?topic=appid-social#social) to your own credentials. {{site.data.keyword.appid_short_notm}} provides IBM credentials that you can use to try out the service. Before you publish your app, you must update the configuration.
4. [Customize the preconfigured sign-in page](#login-widget) to display the image and colors of your choice.

### Configuring cloud directory
{: #cloud-directory-appid}

With {{site.data.keyword.appid_short_notm}}, you can manage your own user registry called cloud directory. Cloud directory enables users to sign up and sign in to your mobile and web apps by using their email and a password.

To configure cloud directory, see [cloud directory](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).

### Customizing the default sign-in page
{: #login-widget-appid}

You can customize the preconfigured sign-in page to display the logo and colors of your choice.

To customize the page:

1. Open the {{site.data.keyword.appid_short_notm}} service dashboard.
2. Select the **Login Customization** section. You can modify the appearance of the login widget to align with your company's brand.
3. Upload your company's logo by selecting a PNG or JPG file from your local system. The recommended image size is 320 x 320 pixels. The maximum file size is 100 Kb.
4. Select a header color for the widget from the color picker, or enter the hex code for another color.
5. Inspect the preview pane, and click **Save Changes** when you are happy with your customizations. A confirmation message is displayed.
6. In your browser, refresh your login page to verify your changes.

### Displaying the default pages
{: #default-pages-appid}

{{site.data.keyword.appid_short_notm}} provides a default login page that you can call if you don't have your own UI pages to display.

Depending on your identity provider configuration, the pages that you can display differ. The service doesn't provide advanced functions for social identity providers because we never have access to a user's account information. Users must go to the identity provider to manage their information. For example, if they want to change their Facebook password, they must go to [https://www.facebook.com](https://www.facebook.com){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon").

Check out the following table to see which pages you can display for each type of identity provider.

| Display page | Social identity provider | Cloud directory | 
|:-----|:-----|:-----|
| Sign in | <img src="images/confirm.png" width="32" alt="Feature available" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="Feature available" style="width:32px;" /> |
| Sign up |  | <img src="images/confirm.png" width="32" alt="Feature available" style="width:32px;" /> |
| Forgot password |  | <img src="images/confirm.png" width="32" alt="Feature available" style="width:32px;" /> |
| Change password |  | <img src="images/confirm.png" width="32" alt="Feature available" style="width:32px;" /> |
| Account details |  | <img src="images/confirm.png" width="32" alt="Feature available" style="width:32px;" /> |
{: row-headers}
{: class="comparison-table"}
{: caption="Table comparison. Displaying pages for social identity providers and cloud directory." caption-side="bottom"}
{: summary="This table has row and column headers. The row headers identify the account pages that can be displayed. The column headers identify which identity service can display them. To understand where an account page can be displayed in the table, navigate to the row for the account page, and find the column for the identity service that you are interested in."}

To display the default pages:

1. Open the {{site.data.keyword.appid_short_notm}} dashboard to the **Managing identity providers** tab and set cloud directory to **On**.
2. Configure your [directory and message settings](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).
3. Choose the combinations of sign-on pages that you'd like to display, and place the code to call those pages in your application.

#### Sign in

1. Set cloud directory to **On** in your identity provider settings and specify a callback endpoint.
2. Add a post route to your app that can be called with the user name and password parameters and log in by using the resource owner password.
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy` allows users to sign in to your web apps with a user name and password. After a successful login, a user's access token is stored in the HTTP session and is available during the session. After the HTTP session is destroyed or expired, the token is invalid.
  {: tip}

#### Sign up

1. Set **Allow users to sign up and reset their password** to **On** in the cloud directory settings. If set to no, the process ends without retrieving access and identity tokens.
2. Pass the WebAppStrategy `show` property and set it to `WebAppStrategy.SIGN_UP`.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

#### Forgot password

1. Set **Allow users to sign up and reset their password** and **Forgot password email** to **ON** in the cloud directory settings. If set to no, the process ends without retrieving access and identity tokens.
2. Pass the WebAppStrategy `show` property and set it to `WebAppStrategy.FORGOT_PASSWORD`.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

#### Change password

1. Set **Allow users to sign up and reset their password** to **On** in the cloud directory settings.
2. Pass the WebAppStrategy `show` property and set it to `WebAppStrategy.CHANGE_PASSWORD`.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

#### Account details

{: #default-account-details}
1. Set **Allow users to sign up and reset their password** to **On** in the cloud directory settings
2. Pass the WebAppStrategy `show` property and set it to `WebAppStrategy.CHANGE_DETAILS`.
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

Want to provide your users with a custom experience? Check out the [{{site.data.keyword.appid_short_notm}} docs](/docs/services/appid?topic=appid-login-widget#branding) to see how you can display your own UI pages.
{: tip}

## Step 5. Testing your app
{: #test-appid}

Is everything set up correctly? You can test it out!

1. Install the dependencies and start your application server.
2. Using the GUI, walk through the process of signing into your application. If you configured cloud directory, be sure that all of your pages are displaying how you intend.
3. Update the identity providers or the login widget page in the {{site.data.keyword.appid_short_notm}} dashboard. Click **Review Activity** to see the authentication events that occurred.
4. Repeat steps 1 and 2 to see that the changes are immediately implemented. No updates to your app code are required.

Having trouble? Check out [troubleshooting {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-troubleshooting#troubleshooting).

## Next steps
{: #next-appid notoc}

Great job! You added an authentication step to your app. Keep the momentum by trying one of the following options:

* To learn more about, and take advantage of all of the features that {{site.data.keyword.appid_short_notm}} offers, [check out the docs](/docs/services/appid?topic=appid-getting-started).
* Starter kits are one of the fastest ways to use the capabilities of {{site.data.keyword.cloud}}. View the available starter kits in the [Mobile developer dashboard](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon"). Download the code. Run the app!
