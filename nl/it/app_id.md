---

copyright:
  years: 2018
lastupdated: "2018-09-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Aggiunta dell'autenticazione utente
{: #authentication}

La sicurezza delle applicazioni può essere incredibilmente complicata. Per la maggior parte degli sviluppatori, è una delle parti più difficili della creazione di un'applicazione. Come puoi essere sicuro che stai proteggendo le informazioni del tuo utente? Integrando {{site.data.keyword.appid_full}} nelle tue applicazioni, puoi proteggere le risorse e aggiungere l'autenticazione, anche quando non hai molta esperienza nel campo della sicurezza.

Richiedendo agli utenti di eseguire l'accesso alla tua applicazione, puoi memorizzare i dati utente quali le preferenze dell'applicazione o i loro profili social pubblici. Puoi quindi utilizzare tali dati per personalizzare l'esperienza di ogni utente all'interno dell'applicazione. {{site.data.keyword.appid_short_notm}} fornisce un framework di accesso per te ma puoi anche portare le tue pagine di accesso personalizzate da utilizzare con Cloud Directory.

Per ulteriori informazioni su tutti i modi in cui puoi utilizzare {{site.data.keyword.appid_short_notm}} e le informazioni sull'architettura, consulta [Informazioni su {{site.data.keyword.appid_short_notm}}](/docs/services/appid/about.html).

## Prima di iniziare
{: #before}

Assicurati di disporre dei seguenti prerequisiti pronti a essere utilizzati:
1. Devi avere un [account {{site.data.keyword.cloud_notm}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}.
2. Installa la [CLI {{site.data.keyword.cloud_notm}}](../cli/index.html).
3. Installa il [npm ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")supporto di gestione del pacchetto ](https://nodejs.org/){: new_window}.
4. Implementa il tuo server Node.js con il [framework Express ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](http://expressjs.com/){: new_window}. Per installare il framework Express, utilizza la riga di comando per aprire la directory con la tua applicazione Node.js ed esegui questo comando:
  ```
  npm install --save express
  ```
  {: codeblock}

5. Installa Passport. Utilizza la riga di comando per aprire la directory con la tua applicazione Node.js ed esegui questo comando:
  ```
  npm install --save passport
  ```
  {: codeblock}

  **Nota**: altri framework utilizzano i framework `Express`, come LoopBack. Puoi utilizzare l'SDK server {{site.data.keyword.appid_short_notm}} con uno qualsiasi di questi framework.

6. Individua i valori chiave delle credenziali da utilizzare in seguito per l'inizializzazione SDK:

    * _**tenantID**_ - l'ID tenant è un identificativo univoco utilizzato per inizializzare la tua applicazione. Puoi trovare il valore nel dashboard del servizio {{site.data.keyword.appid_short_notm}} facendo clic su **Visualizza credenziali** nella scheda **Credenziali del servizio**.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_ - puoi trovare questi valori facendo clic su **Visualizza credenziali** nella scheda **Credenziali del servizio** del tuo dashboard del servizio.
    * _**CALLBACK_URL**_ - l'URL per la pagina che gli utenti visualizzano dopo che hanno eseguito l'accesso.
    * _**redirectUri**_ - il valore `redirectUri` può essere fornito in tre modi:
      * Manualmente nel nuovo `WebAppStrategy({redirectUri: "...."})`
      * Come una variabile di ambiente denominata `redirectUri`. 
      * Se non viene fornito il valore `redirectUri`, l'SDK ID applicazione richiama l'`application_uri` dell'applicazione che è in esecuzione su {{site.data.keyword.cloud_notm}} e accoda il suffisso predefinito `/ibm/cloud/appid/callback`.

## Passo 1. Creazione di un'istanza di {{site.data.keyword.appid_short_notm}}
{: #create-instance}

**Esegui il provisioning di un'istanza del servizio**
1. Nel [catalogo {{site.data.keyword.cloud_notm}}](https://console.bluemix.net/catalog/), seleziona la categoria **Web e mobile** e fai clic su {{site.data.keyword.appid_short_notm}}. Viene aperta la pagina di configurazione del servizio.
2. Dai un nome alla tua istanza del servizio oppure utilizza il nome preimpostato.
3. Seleziona il tuo piano dei prezzi e fai clic su **Crea**.

## Passo 2. Installazione dell'SDK
{: #install}

1. Utilizzando la riga di comando, apri la directory con la tua applicazione Node.js.
2. Installa il servizio {{site.data.keyword.appid_short_notm}}.
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## Passo 3. Inizializzazione dell'SDK
{: #initialize}

1. Aggiungi le seguenti definizioni `require` al tuo file `server.js`.
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. Configura la tua applicazione Express per utilizzare il middleware express-session. **Nota**: devi configurare il middleware con l'archiviazione di sessione appropriata per gli ambienti di produzione. Per ulteriori informazioni, consulta la [documentazione di expressjs![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/expressjs/session){: new_window}.
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

3. Passa l'ID tenant (`tenant ID`) e le credenziali per inizializzare l'SDK.
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

    Se hai bisogno di aiuto per trovare i valori chiave delle credenziali per la tua applicazione, consulta il *passo 5* della sezione [Prima di iniziare](app_id.html#before) per i dettagli relativi a dove trovarli.
    {: tip}

4. Configura passport con la serializzazione e la deserializzazione. Questo passo di configurazione è richiesto per la persistenza della sessione autenticata attraverso le richieste HTTP. Per ulteriori informazioni, consulta la [documentazione di passport ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](http://passportjs.org/docs){: new_window}.
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });

  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. Aggiungi il seguente codice al tuo file `server.js` per immettere i reindirizzamenti di servizio:
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  });
  ```
  {: codeblock}

  **Nota**: il servizio esegue il reindirizzamento nel seguente ordine:
  1. l'URL originale della richiesta che ha attivato il processo di autenticazione viene conservato nella sessione HTTP nella chiave `WebAppStrategy.ORIGINAL_URL`.
  2. Un reindirizzamento eseguito correttamente, come specificato in `passport.authenticate(name, {successRedirect: "...."})`.
  3. La directory root dell'applicazione ("/").

Per ulteriori informazioni, vedi il [repository GitHub di {{site.data.keyword.appid_short_notm}} Node.js ![icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window}.

## Passo 4. Gestione dell'esperienza di accesso
{: #manage-sign-in}

<!--The following content is similar to https://console.stage1.bluemix.net/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} fornisce un widget di accesso per dare ai tuoi utenti delle opzioni di accesso protetto.

Quando la tua applicazione è configurata per utilizzare un provider di identità, i visitatori della tua applicazione vengono indirizzati a una pagina di accesso dal widget di accesso. Per impostazione predefinita, quando solo un provider è impostato su **On**, i visitatori sono reindirizzati alla pagina di autenticazione di tale provider di identità. Con il widget di accesso, puoi visualizzare una pagina di accesso predefinita oppure, con la directory cloud, puoi riutilizzare le tue IU esistenti.

Un provider di identità fornisce le informazioni di autenticazione per i tuoi utenti in modo da consentirti di autorizzarli. Con {{site.data.keyword.appid_short_notm}}, puoi utilizzare i provider di identità social, quali Facebook e Google+, oppure puoi gestire un registro utenti con Cloud Directory.

Puoi aggiornare il tuo flusso di accesso in qualsiasi momento, senza modificare in alcun modo il tuo codice sorgente!
{: tip}

Il servizio utilizza i tipi di concessione `OAuth 2` per associare il processo di autorizzazione. Quando configuri i provider di identità social come Facebook, per richiamare il widget di accesso viene utilizzato il [flusso di concessione dell'autorizzazione Oauth2 ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window}. Quando visualizzi le tue pagine dell'IU, per ottenere l'accesso ai token e per identificarli viene utilizzato il [flusso di credenziali della password del proprietario delle risorse![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window}.

Dopo che hai configurato le impostazioni per i [provider di identità social](/docs/services/appid/identity-providers.html) e [Cloud Directory](/docs/services/appid/cloud-directory.html), puoi iniziare a implementare il codice.

### Configurazione dei provider di identità social
{: #social-identity}

Per configurare i provider di identità social, completa la seguente procedura:

1. Apri il dashboard {{site.data.keyword.appid_short_notm}} a **Identity Providers > Manage**.
2. Imposta i provider di identità che vuoi utilizzare su **On**. Puoi utilizzare qualsiasi combinazione di provider di identità ma, se vuoi portare delle pagine di accesso personalizzate, abilita solo Cloud Directory.
3. Aggiorna la [configurazione predefinita](/docs/services/appid/identity-providers.html) alle tue credenziali. {{site.data.keyword.appid_short_notm}} fornisce le credenziali IBM che puoi utilizzare per provare il servizio. Prima di pubblicare la tua applicazione, devi aggiornare la configurazione.
4. [Personalizza la pagina di accesso preconfigurata](#login-widget) per visualizzare l'immagine e i colori da te scelti.

### Configurazione di Cloud Directory
{: #cloud-directory}

Con {{site.data.keyword.appid_short_notm}}, puoi gestire il tuo registro utenti denominato Cloud Directory. Cloud Directory abilita gli utenti a registrarsi e ad accedere alle tue applicazioni mobili e web utilizzando la loro email e una password.

Per configurare Cloud Directory, vedi [Cloud Directory](/docs/services/appid/cloud-directory.html).

### Personalizzazione della pagina di accesso predefinita
{: #login-widget}

Puoi personalizzare la pagina di accesso preconfigurata per visualizzare il logo e i colori da te scelti.

Per personalizzare la pagina:

1. Apri il dashboard del servizio {{site.data.keyword.appid_short_notm}}.
2. Seleziona la sezione **Login Customization**. Puoi modificare l'aspetto del widget di accesso per allinearlo con il tuo marchio aziendale.
3. Carica il tuo logo aziendale selezionando un file PNG o JPG dal tuo sistema locale. La dimensione dell'immagine consigliata è 320 x 320 pixel. La dimensione massima del file è 100 Kb.
4. Seleziona un colore di intestazione per il widget dal selezionatore del colore o immetti il codice esadecimale per un altro colore.
5. Controlla il pannello dell'anteprima e fai clic su **Save Changes** quando sei soddisfatto delle tue personalizzazioni. Viene visualizzato un messaggio di conferma.
6. Nel tuo browser, aggiorna la tua pagina di accesso per verificare le tue modifiche.

### Visualizzazione delle pagine predefinite
{: #default-pages}

{{site.data.keyword.appid_short_notm}} fornisce una pagina di accesso predefinita che puoi richiamare se non hai delle tue pagine dell'IU da visualizzare.

A seconda della configurazione del tuo provider di identità. le pagine che puoi visualizzare differiscono. Il servizio non fornisce funzioni avanzate per i provider di identità social perché non abbiamo mai accesso alle informazioni sull'account di un utente. Gli utenti devono andare al provider di identità per gestire le loro informazioni. Ad esempio, se vogliono modificare la loro password di Facebook, devono andare a www.facebook.com.

Consulta la seguente tabella per vedere quali pagine puoi visualizzare per ciascun tipo di provider di identità.

| Visualizza pagina | Provider di identità social | Cloud Directory | 
|:-----|:-----|:-----|
| Sign in | <img src="images/confirm.png" width="32" alt="Funzione disponibile" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="Funzione disponibile" style="width:32px;" /> |
| Sign up |  | <img src="images/confirm.png" width="32" alt="Funzione disponibile" style="width:32px;" /> |
| Forgot password |  | <img src="images/confirm.png" width="32" alt="Funzione disponibile" style="width:32px;" /> |
| Change password |  | <img src="images/confirm.png" width="32" alt="Funzione disponibile" style="width:32px;" /> |
| Account details |  | <img src="images/confirm.png" width="32" alt="Funzione disponibile" style="width:32px;" /> |

Per visualizzare le pagine predefinite:

1. Apri il dashboard {{site.data.keyword.appid_short_notm}} alla scheda **Managing identity providers** e imposta Cloud Directory su **On**.
2. Configura le tue [impostazioni di directory e messaggi](/docs/services/appid/cloud-directory.html).
3. Scegli la combinazione di pagine di accesso che vuoi visualizzare e inserisci il codice per richiamare tali pagine nella tua applicazione.

**Sign in**
1. Imposta Cloud Directory su **On** nelle impostazioni del tuo provider di identità e specifica un endpoint di callback.
2. Aggiungi un instradamento post alla tua applicazione che può essere richiamata con i parametri password e nome utente e accedi utilizzando la password del proprietario della risorsa.
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy` consente agli utenti di accedere alle tue applicazioni web con un nome utente e una password. Dopo un accesso eseguito correttamente, un token di accesso dell'utente viene memorizzato nella sessione HTTP ed è disponibile durante la sessione. Dopo che la sessione HTTP è stata interrotta o è scaduta, il token non è valido.
  {: tip}

**Sign up**
1. Imposta **Allow users to sign up and reset their password** su **On** nelle impostazioni di Cloud Directory. Se è impostata su no, il processo termina senza richiamare i token di accesso e di identità.
2. Passa la proprietà WebAppStrategy `show` e impostala su `WebAppStrategy.SIGN_UP`.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

**Forgot password**
1. Imposta **Allow users to sign up and reset their password** e **Forgot password email** su **ON** nelle impostazioni di Cloud Directory. Se è impostata su no, il processo termina senza richiamare i token di accesso e di identità.
2. Passa la proprietà WebAppStrategy `show` e impostala su `WebAppStrategy.FORGOT_PASSWORD`.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

**Change password**
1. Imposta **Allow users to sign up and reset their password** su **On** nelle impostazioni di Cloud Directory.
2. Passa la proprietà WebAppStrategy `show` e impostala su `WebAppStrategy.CHANGE_PASSWORD`.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

**Account details**
{: #default-account-details}
1. Imposta **Allow users to sign up and reset their password** su **On** nelle impostazioni di Cloud Directory
2. Passa la proprietà WebAppStrategy `show` e impostala su `WebAppStrategy.CHANGE_DETAILS`.
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

Vuoi fornire ai tuoi utenti un'esperienza personalizzata? Consulta la [documentazione di {{site.data.keyword.appid_short_notm}}](/docs/services/appid/login-widget.html#branding) per vedere come puoi visualizzare le tue pagine dell'IU.
{: tip}

## Passo 5. Esecuzione di test della tua applicazione
{: #test}

Tutto è configurato correttamente? Puoi verificarlo eseguendo dei test.

1. Installa le dipendenze e avvia il server delle applicazioni.
2. Utilizzando la GUI, segui il processo di accesso alla tua applicazione. Se hai configurato Cloud Directory, assicurati che tutte le tue pagine si presentino come previsto.
3. Aggiorna i provider di identità o la pagina del widget di accesso nel tuo dashboard {{site.data.keyword.appid_short_notm}}. Fai clic su **Review Activity** per visualizzare gli eventi di autenticazione che si sono verificati.
4. Ripeti i passi 1 e 2 per appurare che le modifiche vengano implementate immediatamente. Non è richiesto alcun aggiornamento al tuo codice dell'applicazione.

Hai riscontrato dei problemi? Consulta [Risoluzione dei problemi di {{site.data.keyword.appid_short_notm}}](/docs/services/appid/ts_index.html).

## Passi successivi
{: #next notoc}

Ottimo lavoro! Hai aggiunto un passo di autenticazione alla tua applicazione. Non fermarti ora e continua provando una delle seguenti opzioni:

* Per saperne di più su tutte le nuove funzioni offerte da {{site.data.keyword.appid_short_notm}} e per avvalertene, [consulta la documentazione](/docs/services/appid/index.html)!
* I kit starter sono uno dei modi più rapidi per utilizzare le funzionalità di {{site.data.keyword.cloud}}. Visualizza i kit starter disponibili nel [dashboard degli sviluppatori di applicazioni mobili ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/developer/mobile/dashboard){: new_window}. Scarica il codice. Esegui l'applicazione!
