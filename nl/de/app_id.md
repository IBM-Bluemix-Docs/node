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

# Benutzerauthentifizierung hinzufügen
{: #authentication}

Die Anwendungssicherheit kann extrem kompliziert sein. Für die meisten Entwickler ist dies einer der schwierigsten Teile bei der Erstellung einer App. Wie können Sie sicher sein, dass Sie die Informationen des Benutzers schützen? Durch die Integration von {{site.data.keyword.appid_full}} in Ihre Apps können Sie Ressourcen schützen und Authentifizierung hinzufügen, und zwar selbst dann, wenn Sie nicht über viel Erfahrung im Bereich Sicherheit verfügen.

Wenn Sie Benutzer dazu verpflichten, sich an Ihrer App anzumelden, können Sie Benutzerdaten wie App-Vorgaben oder öffentliche Social Media-Profile speichern. Anschließend können Sie diese Daten verwenden, um die Erfahrungen der einzelnen Benutzer in der App anzupassen. {{site.data.keyword.appid_short_notm}} stellt ein Anmeldeframework für Sie bereit, Sie können jedoch auch Ihre eigenen geschützten Anmeldeseiten für die Verwendung mit Cloud Directory verwenden.

Weitere Informationen zur möglichen Verwendung von {{site.data.keyword.appid_short_notm}} und Architekturinformationen finden Sie in [Informationen zu {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-about#about).

## Vorbereitende Schritte
{: #prereqs-appid}

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:
1. Sie müssen über ein [{{site.data.keyword.cloud}}-Konto ](https://cloud.ibm.com/registration){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") verfügen.
2. Installieren Sie die [{{site.data.keyword.cloud_notm}}-CLI](/docs/cli?topic=cloud-cli-getting-started).
3. Installieren Sie die Unterstützung für das Paketmanagement mit [npm ](https://nodejs.org/en/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link").
4. Implementieren Sie Ihren Node.js-Server mit dem [Express-Framework](http://expressjs.com/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link"). Verwenden Sie zum Installieren des Express-Frameworks die Befehlszeile, um das Verzeichnis mit der Node.js-App zu öffnen, und führen Sie den folgenden Befehl aus:
  ```
  npm install --save express
  ```
  {: codeblock}

5. Installieren Sie Passport. Verwenden Sie die Befehlszeile, um das Verzeichnis mit Ihrer Node.js-App zu öffnen, und führen Sie den folgenden Befehl aus:
  ```
  npm install --save passport
  ```
  {: codeblock}

  Andere Frameworks verwenden `Express`-Frameworks, wie LoopBack. Sie können das {{site.data.keyword.appid_short_notm}}-Server-SDK mit allen dieser Frameworks verwenden.
  {: note}

6. Suchen Sie nach den Schlüsselwerten des Berechtigungsnachweises, die später für die SDK-Initialisierung verwendet werden sollen:

    * _**tenantID**_: Die Tenant-ID ist eine eindeutige Kennung, die zur Initialisierung Ihrer App verwendet wird. Sie können den entsprechenden Wert im {{site.data.keyword.appid_short_notm}}-Service-Dashboard aufrufen, indem Sie auf der Registerkarte **Serviceberechtigungsnachweise** auf **Berechtigungsnachweise anzeigen** klicken.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_: Sie können diese Werte aufrufen, indem Sie im Service-Dashboard auf der Registerkarte **Serviceberechtigungsnachweise** auf **Berechtigungsnachweise anzeigen** klicken.
    * _**CALLBACK_URL**_: Die URL für die Seite, die Benutzern nach der Anmeldung angezeigt wird.
    * _**redirectUri**_: Der Wert für `redirectUri` kann auf drei Arten angegeben werden:
      * Manuell in neuer `WebAppStrategy({redirectUri: "...."})`
      * Als Umgebungsvariable mit dem Namen `redirectUri`. 
      * Wenn der Wert für `redirectUri` nicht angegeben wird, ruft das App ID-SDK den Anwendungs-URI (`application_uri`) der Anwendung ab, die in {{site.data.keyword.cloud_notm}} ausgeführt wird, und hängt das Standardsuffix `/ibm/cloud/appid/callback` an.

## Schritt 1. Instanz von {{site.data.keyword.appid_short_notm}} erstellen
{: #create-instance-appid}

Stellen Sie eine Instanz des Service bereit:

1. Wählen Sie im [{{site.data.keyword.cloud_notm}}-Katalog](https://cloud.ibm.com/catalog){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") die Kategorie **Web und Mobil** aus und klicken Sie auf {{site.data.keyword.appid_short_notm}}. Die Seite mit der Servicekonfiguration wird geöffnet.
2. Geben Sie der Serviceinstanz entweder einen Namen oder verwenden Sie den voreingestellten Namen.
3. Wählen Sie Ihren Preistarif aus und klicken Sie auf **Erstellen**.

## Schritt 2. SDK installieren
{: #install-appid}

1. Verwenden Sie die Befehlszeile, um das Verzeichnis mit Ihrer Node.js-App zu öffnen.
2. Installieren Sie den {{site.data.keyword.appid_short_notm}}-Service.
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## Schritt 3. SDK initialisieren
{: #initialize-appid}

1. Fügen Sie die folgenden `require`-Definitionen zur Datei `server.js` hinzu.
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. Richten Sie Ihre Express-App für die Verwendung von express-session-Middleware ein. **Hinweis**: Sie müssen die Middleware mit dem richtigen Sitzungsspeicher für Produktionsumgebungen konfigurieren. Weitere Informationen finden Sie in der [Dokumentation zu expressjs ](https://github.com/expressjs/session){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link").
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

3. Übergeben Sie den Wert für `tenant-id` und die Berechtigungsnachweise, um das SDK zu initialisieren.
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

    Wenn Sie bei der Suche nach den den Schlüsselwerten für die Berechtigungsnachweise für Ihre App Hilfe benötigen, finden Sie in *Schritt 5* im Abschnitt [Vorbereitende Schritte](#prereqs-appid) Informationen dazu, wo Sie sie finden.
    {: tip}

4. Konfigurieren Sie Passport mit Serialisierung und Deserialisierung. Dieser Konfigurationsschritt ist für eine authentifizierte Sitzungspersistenz über HTTP-Anforderungen hinweg erforderlich. Weitere Informationen finden Sie in der [Dokumentation zu Passport ](http://www.passportjs.org/docs/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link").
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. Fügen Sie den folgenden Code zur Datei `server.js` hinzu, um die Umleitungen für den Service auszugeben:
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  Der Service wird in der folgenden Reihenfolge umgeleitet:
  1. Die ursprüngliche URL der Anforderung, die den Authentifizierungsprozess ausgelöst hat, wird in der HTTP-Sitzung unter dem Schlüssel `WebAppStrategy.ORIGINAL_URL` als persistent definiert.
  2. Eine erfolgreiche Umleitung, wie in `passport.authenticate(name, {successRedirect: "...."})` angegeben.
  3. Das Stammverzeichnis der Anwendung ("/").

Weitere Informationen finden Sie im [GitHub-Repository zu Node.js für {{site.data.keyword.appid_short_notm}} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link").

## Schritt 4. Anmeldungserfahrung verwalten
{: #manage-signin-appid}

<!--The following content is similar to https://test.cloud.ibm.com/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} stellt ein Anmelde-Widget für Sie bereit, mit dem Sie Benutzern sichere Anmeldeoptionen bieten können.

Wenn Ihre App für die Verwendung eines Identitätsproviders konfiguriert ist, werden die Besucher Ihrer App von dem Anmelde-Widget an eine Anmeldeseite weitergeleitet. Wenn nur ein Provider **aktiviert** ist, werden Besucher standardmöäßig an die Authentifizierungsseite dieses Identitätsproviders weitergeleitet. Mit dem Anmelde-Widget können Sie eine Standardseite für die Anmeldung anzeigen; mit Cloud Directory können Sie Ihre vorhandenen Benutzerschnittstellen wiederverwenden.

Ein Identitätsprovider stellt die Authentifizierungsinformationen für Ihre Benutzer bereit, so dass Sie sie autorisieren können. Mit {{site.data.keyword.appid_short_notm}} können Sie Social Media-Identitätsprovider wie Facebook und Google + verwenden oder Sie können eine Benutzerregistry mit Cloud Directory verwalten.

Sie können Ihren Anmeldeablauf jederzeit aktualisieren, ohne Ihren Quellcode in irgendeiner Weise ändern zu müssen!
{: tip}

Der Service verwendet `OAuth 2`-Bewilligungstypen, um den Berechtigungsprozess zuzuordnen. Wenn Sie Social Media-Identitätsprovider wie Facebook konfigurieren, wird der [Bewilligungsablauf für die OAuth 2-Autorisierung ](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") zum Aufrufen des Anmelde-Widgets verwendet. Wenn Sie Ihre eigenen Benutzerschnittstellenseiten anzeigen, wird der [Ablauf für Kennwortberechtigungsnachweise für Ressourceneigner ](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") für die Anmeldung und den Zugriff auf Identitätstoken verwendet.

Nachdem Sie die Einstellungen für [Social Media-Identitätsprovider](/docs/services/appid?topic=appid-social#social) und [Cloud Directory](/docs/services/appid?topic=appid-cloud-directory#cloud-directory) konfiguriert haben, können Sie mit der Implementierung des Codes beginnen.

### Social Media-Identitätsprovider konfigurieren
{: #social-identity-appid}

Führen Sie die folgenden Schritte aus, um Social Media-Identitätsprovider zu konfigurieren:

1. Klicken Sie im {{site.data.keyword.appid_short_notm}}-Dashboard auf **Identitätsprovider > Verwalten**.
2. **Aktivieren** Sie die Identitätsprovider, die Sie verwenden möchten. Sie können eine beliebige Kombination von Identitätsprovidern verwenden. Wenn Sie jedoch angepasste Anmeldeseiten verwenden möchten, aktivieren Sie nur Cloud Directory.
3. Aktualisieren Sie die [Standardkonfiguration](/docs/services/appid?topic=appid-social#social) mit Ihren eigenen Berechtigungsnachweisen. {{site.data.keyword.appid_short_notm}} stellt IBM Berechtigungsnachweise bereit, die Sie zum Testen des Service verwenden können. Bevor Sie Ihre App veröffentlichen, müssen Sie die Konfiguration aktualisieren.
4. [Passen Sie die vorkonfigurierte Anmeldeseite an](#login-widget), um das Bild und die Farben Ihrer Wahl anzuzeigen.

### Cloud Directory konfigurieren
{: #cloud-directory-appid}

Mit {{site.data.keyword.appid_short_notm}} können Sie Ihre eigene Benutzerregistry namens Cloud Directory verwalten. Über Cloud Directory können sich Benutzer registrieren und sich mithilfe ihrer E-Mail-Adresse und einem Kennwort bei Ihren mobilen Apps und Web-Apps anmelden.

Informationen zum Konfigurieren von Cloud Directory finden Sie in [Cloud Directory](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).

### Standardanmeldeseite anpassen
{: #login-widget-appid}

Sie können die vorkonfigurierte Anmeldeseite so anpassen, dass sie das Logo und die Farben Ihrer Wahl enthält.

Führen Sie die folgenden Schritte aus, um die Seite anzupassen:

1. Öffnen Sie das {{site.data.keyword.appid_short_notm}}-Service-Dashboard.
2. Wählen Sie den Abschnitt für die **Anmeldeanpassung** aus. Sie können die Darstellung des Anmelde-Widgets so ändern, dass es im Einklang mit der Unternehmensmarke angezeigt wird.
3. Laden Sie das Unternehmenslogo hoch, indem Sie eine PNG- oder JPG-Datei auf Ihrem lokalen System auswählen. Die empfohlene Bildgröße beträgt 320 x 320 Pixel. Die maximale Dateigröße beträgt 100 KB.
4. Wählen Sie eine Farbe für die Kopfzeile für das Widget aus der Farbauswahl aus oder geben Sie den hexadezimalen Code für eine andere Farbe an.
5. Überprüfen Sie das Vorschaufenster und klicken Sie auf **Änderungen speichern**, wenn Sie mit Ihren Anpassungen zufrieden sind. Es wird eine entsprechende Bestätigungsnachricht angezeigt.
6. Aktualisieren Sie in Ihrem Browser die Anmeldeseite, um die vorgenommenen Änderungen zu überprüfen.

### Standardseiten anzeigen
{: #default-pages-appid}

{{site.data.keyword.appid_short_notm}} stellt eine Standardanmeldeseite bereit, die Sie aufrufen können, wenn Sie nicht über eigene Benutzerschnittstellenseiten zum Anzeigen verfügen.

Abhängig von der Konfiguration Ihres Identitätsproviders sind die Seiten, die Sie anzeigen können, unterschiedlich. Der Service stellt keine erweiterten Funktionen für Social Media-Identitätsprovider bereit, da IBM niemals Zugriff auf die Kontoinformationen eines Benutzers hat. Die Benutzer müssen zum Identitätsprovider wechseln, um ihre Informationen zu verwalten. Wenn sie beispielsweise ihr Facebook-Kennwort ändern möchten, müssen sie zu [https://www.facebook.com](https://www.facebook.com){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") wechseln.

Sehen Sie sich die folgende Tabelle an, um zu sehen, welche Seiten Sie für die verschiedenen Typen von Identitätsprovider anzeigen können.

| Anzeigeseite | Social Media-Identitätsprovider | Cloud Directory | 
|:-----|:-----|:-----|
| Anmelden | <img src="images/confirm.png" width="32" alt="Feature verfügbar" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="Feature verfügbar" style="width:32px;" /> |
| Registrieren |  | <img src="images/confirm.png" width="32" alt="Feature verfügbar" style="width:32px;" /> |
| Kennwort vergessen |  | <img src="images/confirm.png" width="32" alt="Feature verfügbar" style="width:32px;" /> |
| Kennwort ändern |  | <img src="images/confirm.png" width="32" alt="Feature verfügbar" style="width:32px;" /> |
| Kontodetails |  | <img src="images/confirm.png" width="32" alt="Feature verfügbar" style="width:32px;" /> |
{: row-headers}
{: class="comparison-table"}
{: caption="Tabellenvergleich. Seiten für Social Media-Identitätsprovider und Cloud Directory anzeigen. " caption-side="bottom"}
{: summary="This table has row and column headers. The row headers identify the account pages that can be displayed. The column headers identify which identity service can display them. To understand where an account page can be displayed in the table, navigate to the row for the account page, and find the column for the identity service that you are interested in."}

Gehen Sie folgendermaßen vor, um die Standardseiten anzuzeigen:

1. Öffnen Sie im {{site.data.keyword.appid_short_notm}}-Dashboard die Registerkarte **Identitätsprovider verwalten** und **aktivieren** Sie Cloud Directory.
2. Konfigurieren Sie Ihre [Verzeichnis- und Nachrichteneinstellungen](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).
3. Wählen Sie die Kombinationen von Anmeldeseiten aus, die angezeigt werden sollen, und legen Sie den Code zum Aufrufen dieser Seite in Ihrer Anmeldung fest.

#### Anmelden

1. **Aktivieren** Sie Cloud Directory in den Einstellungen Ihres Identitätsproviders und geben Sie einen Callback-Endpunkt an.
2. Fügen Sie eine Post-Route zu Ihrer App hinzu, die mit den Parametern für den Benutzernamen und das Kennwort aufgerufen werden kann, und melden Sie sich unter Verwendung des Ressourceneignerkennworts an.
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy` ermöglicht es Benutzern, sich mit einem Benutzernamen und einem Kennwort in Ihren Web-Apps anzumelden. Nach einer erfolgreichen Anmeldung wird das Zugriffstoken eines Benutzers in der HTTP-Sitzung gespeichert und steht während der Sitzung zur Verfügung. Wenn die HTTP-Sitzung gelöscht wird oder abgelaufen ist, ist das Token ungültig.
  {: tip}

#### Registrieren

1. **Aktivieren** Sie in den Cloud Directory-Einstellungen die Option, die es **Benutzern ermöglicht, sich zu registrieren und ihre Kennwörter zurückzusetzen**. Ist diese Option inaktiviert, wird der Prozess beendet, ohne dass Zugriffs- und Identitätstokens abgerufen werden.
2. Übergeben Sie die WebAppStrategy-Eigenschaft `show` und legen Sie sie auf `WebAppStrategy.SIGN_UP` fest.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

#### Kennwort vergessen

1. **Aktivieren** Sie in den Cloud Directory-Einstellungen die Option, die es **Benutzern ermöglicht, sich zu registrieren und ihre Kennwörter zurückzusetzen**, und die Option für das Senden einer **E-Mail für vergessenes Kennwort**. Ist diese Option inaktiviert, wird der Prozess beendet, ohne dass Zugriffs- und Identitätstokens abgerufen werden.
2. Übergeben Sie die WebAppStrategy-Eigenschaft `show` und legen Sie sie auf `WebAppStrategy.FORGOT_PASSWORD` fest.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

#### Kennwort ändern

1. **Aktivieren** Sie in den Cloud Directory-Einstellungen die Option, die es **Benutzern ermöglicht, sich zu registrieren und ihre Kennwörter zurückzusetzen**.
2. Übergeben Sie die WebAppStrategy-Eigenschaft `show` und legen Sie sie auf `WebAppStrategy.CHANGE_PASSWORD` fest.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

#### Kontodetails

{: #default-account-details}
1. **Aktivieren** Sie in den Cloud Directory-Einstellungen die Option, die es **Benutzern ermöglicht, sich zu registrieren und ihre Kennwörter zurückzusetzen**.
2. Übergeben Sie die WebAppStrategy-Eigenschaft `show` und legen Sie sie auf `WebAppStrategy.CHANGE_DETAILS` fest.
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

Möchten Sie Ihren Benutzern eine angepasste Erfahrung bereitstellen? Sehen Sie sich die [Dokumentation zu {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-login-widget#branding) genauer an, um zu erfahren, wie Sie Ihre eigenen Benutzerschnittstellenseiten anzeigen können.
{: tip}

## Schritt 5. App testen
{: #test-appid}

Ist alles richtig eingerichtet? Sie können es jetzt testen!

1. Installieren Sie die Abhängigkeiten und starten Sie Ihren Anwendungsserver.
2. Befolgen Sie über die GUI den Prozess für die Anmeldung bei der Anwendung. Wenn Sie Cloud Directory konfiguriert haben, stellen Sie sicher, dass alle Ihre Seiten wie von Ihnen gewünscht angezeigt werden.
3. Aktualisieren Sie die Identitätsprovider oder die Seite für das Anmelde-Widet im {{site.data.keyword.appid_short_notm}}-Dashboard. Klicken Sie auf **Aktivität überprüfen**, um die Authentifizierungsereignisse anzuzeigen, die aufgetreten sind.
4. Wiederholen Sie die Schritte 1 und 2, um zu sehen, ob die Änderungen sofort implementiert werden. Es sind keine Aktualisierungen an Ihrem App-Code erforderlich.

Haben Sie Schwierigkeiten? Werfen Sie einen Blick in den Abschnitt zur [Fehlerbehebung von {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-troubleshooting#troubleshooting).

## Nächste Schritte
{: #next-appid notoc}

Gut gemacht! Sie haben einen Authentifizierungsschritt zu Ihrer App hinzugefügt. Machen Sie am besten gleich weiter, indem Sie eine der folgenden Optionen ausprobieren:

* Wenn Sie mehr zu den Funktionen von {{site.data.keyword.appid_short_notm}} und deren Verwendung erfahren möchten, [lesen Sie die Dokumentation](/docs/services/appid?topic=appid-getting-started). 
* Mit Starter-Kits können Sie die Funktionalität von {{site.data.keyword.cloud}} sehr schnell nutzen. Zeigen Sie die verfügbaren Starter-Kits im [Mobile-Enwicklerdashboard ](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") an. Laden Sie den Code herunter. Führen Sie die App aus!
