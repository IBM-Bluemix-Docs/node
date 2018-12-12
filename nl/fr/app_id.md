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

# Ajout d'une authentification d'utilisateur
{: #authentication}

La sécurité des applications peut s'avérer être un sujet incroyablement compliqué. Pour la plupart des développeurs, il s'agit de l'une des composantes les plus difficiles du processus de création d'application. Comment pouvez-vous être sûr que vous protégez les informations de vos utilisateurs ? En intégrant {{site.data.keyword.appid_full}} à vos applications, vous pouvez sécuriser les ressources et ajouter un processus d'authentification, même si vous ne possédez pas une grande expérience en matière de sécurité.

En demandant aux utilisateurs de se connecter à votre application, vous pouvez stocker des données utilisateurs telles que des préférences d'application ou des profils sociaux publics. Vous pouvez ensuite utiliser ces données pour personnaliser l'expérience de chaque utilisateur au sein de l'application. {{site.data.keyword.appid_short_notm}} vous fournit une infrastructure de connexion, mais vous pouvez également utiliser vos propres pages de connexion de marque avec Cloud Directory.

Pour plus d'informations sur toutes les façons dont vous pouvez utiliser {{site.data.keyword.appid_short_notm}}, ainsi que des informations sur l'architecture, voir [A propos d'{{site.data.keyword.appid_short_notm}}](/docs/services/appid/about.html).

## Avant de commencer
{: #before}

Assurez-vous que les prérequis suivants sont satisfaits :
1. Vous devez disposer d'un compte [{{site.data.keyword.cloud}} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}.
2. Vous avez installé l'[interface CLI {{site.data.keyword.cloud_notm}}](../cli/index.html).
3. Vous avez installé le support de gestion de package [npm ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://nodejs.org/){: new_window}.
4. Vous avez implémenté votre serveur Node.js avec l'[infrastructure Express ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](http://expressjs.com/){: new_window}. Afin d'installer l'infrastructure Express, utilisez la ligne de commande pour ouvrir le répertoire contenant votre application Node.js et exécutez la commande suivante :
  ```
  npm install --save express
  ```
  {: codeblock}

5. Installez Passport. Utilisez la ligne de commande pour ouvrir le répertoire contenant votre application Node.js et exécutez la commande suivante :
  ```
  npm install --save passport
  ```
  {: codeblock}

  **Remarque** : d'autres infrastructures, notamment LoopBack, utilisent des infrastructures `Express`. Vous pouvez utiliser le logiciel SDK serveur d'{{site.data.keyword.appid_short_notm}} avec toutes ces infrastructures.

6. Localisez les valeurs de clé de données d'identification à utiliser ultérieurement pour l'initialisation du kit SDK :

    * _**tenantID**_ - L'ID titulaire est un identificateur unique utilisé pour initialiser votre application. Vous pouvez trouver sa valeur dans le tableau de bord du service {{site.data.keyword.appid_short_notm}} en cliquant sur **Afficher les données d'identification** dans l'onglet **Données d'identification pour le service**.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_ - Vous pouvez trouver ces valeurs en cliquant sur **Afficher les données d'identification** dans l'onglet **Données d'identification pour le service** du tableau de bord de votre service.
    * _**CALLBACK_URL**_ - URL de la page que les utilisateurs voient une fois connectés.
    * _**redirectUri**_ - La valeur `redirectUri` peut être fournie de trois façons :
      * Manuellement, dans un nouveau `WebAppStrategy({redirectUri: "...."})`
      * En tant que variable d'environnement nommée `redirectUri`. 
      * Si la valeur `redirectUri` n'est pas fournie, le kit SDK d'App ID extrait l'URI `application_uri` de l'application qui s'exécute sur {{site.data.keyword.cloud_notm}} et ajoute le suffixe par défaut `/ibm/cloud/appid/callback`.

## Etape 1. Création d'une instance de {{site.data.keyword.appid_short_notm}}
{: #create-instance}

**Mise à disposition d'une instance du service**
1. Dans le [catalogue {{site.data.keyword.cloud_notm}}](https://console.bluemix.net/catalog/), sélectionnez la catégorie **Web et mobile** et cliquez sur {{site.data.keyword.appid_short_notm}}. La page de configuration de service s'ouvre.
2. Donnez un nom à votre instance de service ou utilisez le nom prédéfini.
3. Sélectionnez votre plan de tarification, puis cliquez sur **Créer**.

## Etape 2. Installation du logiciel SDK
{: #install}

1. Depuis la ligne de commande, ouvrez le répertoire dans lequel se trouve votre application Node.js.
2. Installez le service {{site.data.keyword.appid_short_notm}}.
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## Etape 3. Initialisation du logiciel SDK
{: #initialize}

1. Ajoutez les définitions `require` suivantes à votre fichier `server.js` :
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. Configurez votre application Express pour qu'elle utilise le middleware express-session. **Remarque** : vous devez configurer le middleware avec le stockage de session approprié pour les environnements de production. Pour plus d'informations, voir la [documentation expressjs ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://github.com/expressjs/session){: new_window}.
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

3. Transmettez l'`ID titulaire` et les données d'identification pour initialiser le logiciel SDK.
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

    Si vous avez besoin d'aide pour trouver les valeurs de clé de données d'identification pour votre application, consultez l'*étape 5* de la section [Avant de commencer](app_id.html#before) pour plus de détails. 
    {: tip}

4. Configurez passport avec la sérialisation et la désérialisation. Cette étape de configuration est requise pour la persistance de session authentifiée dans les demandes HTTP. Pour plus d'informations, voir la [documentation de Passport ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](http://passportjs.org/docs){: new_window}.
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. Ajoutez le code suivant dans votre fichier `server.js` pour émettre les redirections de service :
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  **Remarque** : le service procède à la redirection dans l'ordre suivant :
  1. L'URL d'origine de la demande qui a déclenché le processus d'authentification est conservée dans la session HTTP sous la clé `WebAppStrategy.ORIGINAL_URL`.
  2. Redirection réussie, comme spécifié dans `passport.authenticate(name, {successRedirect: "...."})`.
  3. Le répertoire racine de l'application ("/").

Pour plus d'informations, voir [{{site.data.keyword.appid_short_notm}} Node.js GitHub repository ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window}.

## Etape 4. Gestion de l'expérience de connexion
{: #manage-sign-in}

<!--The following content is similar to https://console.stage1.bluemix.net/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} fournit un widget de connexion pour que vous puissiez fournir à vos utilisateurs des options de connexion sécurisées.

Lorsque votre application est configurée pour utiliser un fournisseur d'identité, les visiteurs de votre application sont dirigés par le widget vers une page de connexion. Par défaut, quand un seul fournisseur est défini sur **On**, les visiteurs sont redirigés vers la page d'authentification de ce fournisseur d'identité. Le widget de connexion permet d'afficher une page de connexion par défaut ; avec Cloud Directory, vous pouvez réutiliser vos interfaces utilisateurs existantes.

Un fournisseur d'identité fournit les informations d'authentification pour vos utilisateurs afin que vous puissiez les autoriser. {{site.data.keyword.appid_short_notm}} permet d'utiliser des fournisseurs d'identité de réseau social tels que Facebook ou Google+, ou bien vous pouvez gérer un registre d'utilisateurs avec Cloud Directory.

Vous pouvez à tout moment mettre à jour votre flux de connexion sans changer le code source de quelque façon que ce soit.
{: tip}

Le service utilise des types d'autorisation d'accès `OAuth 2` pour mapper le processus d'autorisation. Lorsque vous configurez des fournisseurs d'identité de réseau social tels que Facebook, le [flux d'octroi d'autorisation Oauth2 ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} est utilisé pour appeler le widget de connexion. Lorsque vous affichez vos propres pages d'interface utilisateur, le [flux Resource Owner Password Credentials ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} est utilisé pour se connecter et obtenir des jetons d'accès et d'identité.

Une fois configurés les paramètres pour les [fournisseurs d'identité de réseau social](/docs/services/appid/identity-providers.html) et [Cloud Directory](/docs/services/appid/cloud-directory.html), vous pouvez démarrer l'implémentation du code.

### Configuration des fournisseurs d'identité de réseaux sociaux
{: #social-identity}

Pour configurer des fournisseurs d'identité de réseau social, procédez comme suit :

1. Ouvrez le tableau de bord {{site.data.keyword.appid_short_notm}} sur **Fournisseurs d'identité > Gérer**.
2. Définissez les fournisseurs d'identité que vous voulez utiliser sur **Actif**. Vous pouvez utiliser la combinaison de fournisseurs d'identité de votre choix, mais si vous souhaitez utiliser des pages de connexion personnalisées, activez uniquement le répertoire cloud.
3. Mettez à jour la [configuration par défaut](/docs/services/appid/identity-providers.html) avec vos propres données d'identification. {{site.data.keyword.appid_short_notm}} fournit des données d'identification IBM que vous pouvez utiliser pour essayer le service. Avant de publier votre application, vous devez mettre à jour la configuration.
4. [Personnalisez la page de connexion préconfigurée](#login-widget) pour afficher l'image et les couleurs de votre choix.

### Configuration du répertoire cloud
{: #cloud-directory}

Avec {{site.data.keyword.appid_short_notm}}, vous pouvez gérer votre propre registre d'utilisateurs appelé répertoire cloud. Le répertoire cloud permet aux utilisateurs de s'inscrire et de se connecter à vos applications mobiles et Web à l'aide de leur e-mail et d'un mot de passe.

Pour configurer le répertoire cloud, voir [Configuration du répertoire cloud](/docs/services/appid/cloud-directory.html).

### Personnalisation de la page de connexion par défaut
{: #login-widget}

Vous pouvez personnaliser la page de connexion préconfigurée afin d'afficher le logo et les couleurs de votre choix.

Pour personnaliser la page :

1. Ouvrez le tableau de bord du service {{site.data.keyword.appid_short_notm}}.
2. Sélectionnez la section **Personnalisation de la connexion**. Vous pouvez modifier l'apparence du widget de connexion pour l'adapter à la marque de votre entreprise.
3. Téléchargez le logo de votre entreprise en sélectionnant un fichier PNG ou JPG sur votre système local. La taille recommandée pour l'image est de 320 x 320 pixels. La taille maximale du fichier est 100 Ko.
4. Sélectionnez une couleur d'en-tête pour le widget dans la palette de couleurs ou entrez le code hexadécimal d'une autre couleur.
5. Examinez le panneau de prévisualisation, puis cliquez sur **Sauvegarder les modifications** lorsque vous êtes satisfait de vos personnalisations. Un message de confirmation s'affiche.
6. Dans votre navigateur, actualisez votre page de connexion afin de vérifier vos modifications.

### Affichage des pages par défaut
{: #default-pages}

{{site.data.keyword.appid_short_notm}} fournit une page de connexion par défaut que vous pouvez appeler si vous n'avez pas de pages d'interface utilisateur à afficher.

En fonction de votre configuration de fournisseur d'identité, les pages que vous pouvez afficher varient. Le service ne fournit pas de fonctions avancées pour les fournisseurs d'identité de réseau social car nous n'avons jamais accès aux informations de compte d'un utilisateur. Les utilisateurs doivent accéder au fournisseur d'identité pour gérer leurs informations. Par exemple, s'ils veulent changer leur mot de passe pour Facebook, ils doivent accéder à [https://www.facebook.com](https://www.facebook.com).

Consultez le tableau ci-dessous pour voir les pages que vous pouvez afficher pour chaque type de fournisseur d'identité.

| Page d'affichage | Fournisseur d'identité de réseau social | Répertoire cloud | 
|:-----|:-----|:-----|
| Connexion | <img src="images/confirm.png" width="32" alt="Fonction disponible" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="Fonction disponible" style="width:32px;" /> |
| Inscription |  | <img src="images/confirm.png" width="32" alt="Fonction disponible" style="width:32px;" /> |
| Mot de passe oublié |  | <img src="images/confirm.png" width="32" alt="Fonction disponible" style="width:32px;" /> |
| Changement du mot de passe |  | <img src="images/confirm.png" width="32" alt="Fonction disponible" style="width:32px;" /> |
| Détails du compte |  | <img src="images/confirm.png" width="32" alt="Fonction disponible" style="width:32px;" /> |

Pour afficher les pages par défaut :

1. Accédez dans le tableau de bord {{site.data.keyword.appid_short_notm}} à l'onglet de **gestion des fournisseurs d'identité** et définissez le répertoire cloud sur **Actif**.
2. Configurez votre répertoire [ et les paramètres de message](/docs/services/appid/cloud-directory.html).
3. Choisissez les combinaisons de pages de connexion que vous souhaitez afficher, et placez le code pour appeler ces pages dans votre application.

**Connexion**
1. Activez le répertoire cloud dans les paramètres de votre fournisseur d'identité et spécifiez un noeud final de rappel.
2. Ajoutez une route post à votre application qui pourra être appelée avec les paramètres de nom d'utilisateur et de mot de passe pour la connexion à l'aide du mot de passe du propriétaire de la ressource.
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy` permet aux utilisateurs de se connecter à vos applications Web à l'aide d'un nom d'utilisateur et d'un mot de passe. Une fois la connexion établie, le jeton d'accès d'un utilisateur est stocké dans la session HTTP et reste disponible pendant la session. Lorsque la session HTTP a été détruite ou est arrivée à expiration, le jeton n'est plus valide.
  {: tip}

**Inscription**
1. Activez **Autoriser les utilisateurs à s'inscrire et à réinitialiser leur mot de passe** dans les paramètres de répertoire cloud. Si cette option est définie sur no, le processus se termine sans extraction des jetons d'accès et d'identité.
2. Transmettez la propriété WebAppStrategy `show` et définissez-la sur `WebAppStrategy.SIGN_UP`.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

**Mot de passe oublié**
1. Activez **Autoriser les utilisateurs à s'inscrire et à réinitialiser leur mot de passe** et **E-mail d'oubli de mot de passe** dans les paramètres de répertoire cloud. Si cette option est définie sur no, le processus se termine sans extraction des jetons d'accès et d'identité.
2. Transmettez la propriété WebAppStrategy `show` et définissez-la sur `WebAppStrategy.FORGOT_PASSWORD`.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

**Changement du mot de passe**
1. Activez **Autoriser les utilisateurs à s'inscrire et à réinitialiser leur mot de passe** dans les paramètres de répertoire cloud.
2. Transmettez la propriété WebAppStrategy `show` et définissez-la sur `WebAppStrategy.CHANGE_PASSWORD`.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

**Détails du compte**
{: #default-account-details}
1. Activez **Autoriser les utilisateurs à s'inscrire et à réinitialiser leur mot de passe** dans les paramètres de répertoire cloud.
2. Transmettez la propriété WebAppStrategy `show` et définissez-la sur `WebAppStrategy.CHANGE_DETAILS`.
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

Vous souhaitez offrir à vos utilisateurs une expérience personnalisée ? Consultez la [documentation {{site.data.keyword.appid_short_notm}}](/docs/services/appid/login-widget.html#branding) pour voir comment afficher vos propres pages d'interface utilisateur.
{: tip}

## Etape 5. Test de votre application
{: #test}

Tout est correctement configuré ? Il est temps de tester !

1. Installez les dépendances et démarrer votre serveur d'applications.
2. Depuis l'interface graphique utilisateur, suivez le processus de connexion à votre application. Si vous avez configuré le répertoire cloud, veillez à ce que toutes vos pages s'affichent comme vous l'aviez prévu.
3. Mettez à jour les fournisseurs d'identité pour la page du widget de connexion dans le tableau de bord {{site.data.keyword.appid_short_notm}}. Cliquez sur **Vérification de l'activité** pour visualiser les événements d'authentification qui se sont produits.
4. Répétez les étapes 1 et 2 pour vérifier que les modifications sont immédiatement appliquées. Aucune mise à jour du code de votre application n'est nécessaire.

Vous rencontrez des problèmes ? Consultez la section [troubleshooting {{site.data.keyword.appid_short_notm}}](/docs/services/appid/ts_index.html).

## Etapes suivantes
{: #next notoc}

Félicitations ! Vous avez ajouté une étape d'authentification à votre application. Poursuivez sur votre lancée en essayant l'une des options suivantes :

* Pour en savoir plus et tirer pleinement parti des fonctions offertes par {{site.data.keyword.appid_short_notm}}, [consultez la documentation](/docs/services/appid/index.html) !
* Les kits de démarrage (Starter Kits) constituent l'une des façons les plus rapides d'utiliser les fonctions d'{{site.data.keyword.cloud}}. Affichez les kits de démarrage disponibles dans le tableau de bord [Mobile developer dashboard ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://console.bluemix.net/developer/mobile/dashboard){: new_window}. Téléchargez le code. Exécutez l'application !
