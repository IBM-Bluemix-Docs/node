---

copyright:
  years: 2018
lastupdated: "2018-10-04"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Journalisation dans Node.js
{: #logging_nodejs}

Les messages de journal sont des chaînes avec des informations contextuelles sur l'état et l'activité du microservice au moment où l'entrée de journal est créée. Les journaux sont nécessaires pour diagnostiquer comment et pourquoi un service échoue ; ils jouent un rôle de support pour [appmetrics](appmetrics.html) dans la surveillance de la santé des applications.

Compte tenu de la nature transitoire des processus dans des environnements cloud, les journaux doivent être collectés en placés ailleurs, généralement à un emplacement centralisé destiné à l'analyse. La façon la plus cohérente de journaliser des environnements cloud consiste à envoyer les entrées de journal dans des flux d'erreur et de sortie standard, ce qui permet à l'infrastructure de gérer le reste.

## Ajout de support Log4js à l'application Node.js
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) est une infrastructure de journalisation répandue pour Node.js, qui présente de nombreux avantages natifs, notamment : 
* Journalisation dans `stdout` ou `stderr`
* Diverses options d'ajout
* Agencement et modèles de message de journal configurables
* Utilisation de niveaux de journalisation pour différentes catégories de journal

1. Pour utiliser Log4js, exécutez la commande [npm](https://nodejs.org/) suivante dans le répertoire racine de votre application, ce qui installe le module et l'ajoute à votre fichier `package.json`.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Pour l'utiliser dans votre application, ajoutez les lignes de code suivantes :
  ```javascript
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

Pour plus d'informations sur la personnalisation des messages de journal avec des appenders, des niveaux de journal et des détails de configuration, voir le site officiel de la [documentation log4js-node](https://log4js-node.github.io/log4js-node/).

## Surveillance des journaux avec App Service
{: #monitoring}

Par défaut, les applications Node.js créées à l'aide d'{{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard) sont fournies avec Log4js. L'exécution de l'application en mode natif ou dans un environnement cloud génère une sortie similaire à celle-ci : `2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`. Vous pouvez afficher la sortie comme suit :
* Utilisez `stdout` pour une exécution en local.
* Dans les journaux pour des déploiements [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) et [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/), qui sont accessible par `ibmcloud app logs --recent <APP_NAME>` et `kubectl <deployment name>`.

Dans le fichier `server/server.js`, vous pouvez voir le code suivant :
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Par défaut, le niveau de journalisation est défini sur `OFF` pour pouvoir être utilisé en toute sécurité dans les bibliothèques. Cependant, il peut être remplacé par la variable d'environnement **LOG_LEVEL** de l'application.
{: tip}

## Etapes suivantes
{: #next_steps notoc}

En savoir plus sur l'affichage des journaux dans chacun des environnements de déploiement :
* [Journaux Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Journaux de Cloud Foundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [Journaux & surveillance d'{{site.data.keyword.openwhisk}}](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Utilisation d'un regroupeur de journaux
* [Analyse de journal {{site.data.keyword.cloud_notm}}](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [Pile ELK privée {{site.data.keyword.cloud_notm}} ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
