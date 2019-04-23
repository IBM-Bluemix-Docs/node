---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-04"

keywords: nodejs logging, view logs nodejs, add logging nodejs, log4j nodejs, stdout nodejs, nodejs log, output nodejs, nodejs logger

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Journalisation dans Node.js
{: #logging_nodejs}

Les messages de journal sont des chaînes avec des informations contextuelles sur l'état et l'activité du microservice au moment où l'entrée de journal est créée. Les journaux sont nécessaires pour diagnostiquer comment et pourquoi un service échoue ; ils jouent un rôle de support pour [appmetrics](/docs/node?topic=nodejs-metrics) dans la surveillance de la santé des applications.

Compte tenu de la nature transitoire des processus dans les environnements de cloud, les journaux doivent être collectés et envoyés ailleurs, généralement dans un emplacement centralisé pour analyse. La façon la plus cohérente de journaliser des environnements cloud consiste à envoyer les entrées de journal dans des flux d'erreur et de sortie standard, ce qui permet à l'infrastructure de gérer le reste.

Vous pouvez utiliser [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe"), qui est une infrastructure de journalisation répandue pour Node.js, afin de bénéficier de nombreux avantages, notamment : 
* Journalisation dans `stdout` ou `stderr`
* Diverses options d'ajout
* Agencement et modèles de message de journal configurables
* Utilisation de `niveaux de journalisation` pour différentes catégories de journal

## Ajout de support Log4js à une application Node.js existante
{: #add_log4j}

1. Installez tout d'abord `log4js` à l'aide de la commande [npm](https://nodejs.org/){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") suivante dans le répertoire racine de votre application, ce qui installe le module et l'ajoute à votre fichier `package.json`.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Pour l'utiliser dans votre application, ajoutez les lignes de code suivantes dans votre application :
  ```js
  var log4js = require('log4js');

  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **Sortie stdout** :
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

3. Pour consigner les événements du journal dans le flux d'erreurs standard, configurez un programme d'ajout comme suit :
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  Pour plus d'informations sur la personnalisation des messages de journal avec des programmes d'ajout (appenders), les `niveaux de journal` et les détails de configuration, voir le site officiel de la [documentation log4js-node](https://log4js-node.github.io/log4js-node/){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe").

## Surveillance à l'aide des applications App Service
{: #monitoring}

Par défaut, les applications Node.js créées à l'aide d'{{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") sont fournies avec Log4js. Vous pouvez ouvrir le fichier `server/server.js` pour vérifier le code Log4js suivant :
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Par défaut, le `niveau de journalisation` est défini sur `OFF` pour pouvoir être utilisé en toute sécurité dans les bibliothèques. Cependant, il peut être remplacé par la variable d'environnement **LOG_LEVEL** de l'application.
{: tip}

### Affichage de la sortie de journal
{: #node-view-log-output}

Consultez la sortie de journal ci-dessous liée à l'exécution de l'application en mode natif ou dans un environnement cloud :
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

Vous pouvez afficher la sortie de journal de l'une des manières suivantes :
* Pour les environnements locaux, utilisez `stdout`.
* Pour les déploiements [Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html), accédez aux journaux en exécutant :
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* Pour les déploiements [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/), accédez aux journaux en exécutant :
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## Etapes suivantes
{: #next_steps-logging notoc}

Découvrez davantage d'informations en consultant les journaux dans chaque environnement de déploiement :
* [Journaux de Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")
* [Journaux de Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps?topic=cloudloganalysis-logging_cf_apps#logging_cf_apps)
* [Journaux et surveillance d'{{site.data.keyword.openwhisk}}](/docs/openwhisk?topic=cloud-functions-openwhisk_logs#openwhisk_logs)

Utilisation d'un regroupeur de journaux
* [Analyse de journal {{site.data.keyword.cloud_notm}}](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [Pile ELK privée {{site.data.keyword.cloud_notm}}](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")
