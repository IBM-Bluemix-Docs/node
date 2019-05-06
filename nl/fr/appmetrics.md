---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-30"

keywords: nodejs metrics, application metrics nodejs, node appmetrics, nodejs autoscaling, nodejs dash, appmetrics-dashs nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilisation de métriques d'application avec des applications Node.js
{: #metrics}

Apprenez à installer et interpréter les métriques d'application Node.js ainsi qu'à y accéder. Vous pouvez surveiller les applications Node.js via le tableau de bord [Node Application Metrics](https://developer.ibm.com/open/projects/node-application-metrics/){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") afin de visualiser les performances de votre application Node.js en affichant des métriques dans une interface Web.
{: shortdesc}

## Identification visuelle des problèmes
{: #identify-problems}

Les métriques d'application sont importantes pour la surveillance des performances de votre application. Une vue en direct de métriques comme l'UC, la mémoire, le temps d'attente et les métriques HTTP est essentielle pour vous assurer que votre application fonctionne efficacement dans le temps. Vous pouvez utiliser un service cloud comme Cloud Foundry [Auto-Scaling](/docs/services/Auto-Scaling?topic=Auto-Scaling-get-started), qui repose sur des métriques pour une mise à l'échelle dynamique des instances en fonction de la charge de travail en cours. En utilisant des métriques d'application, vous savez précisément quand augmenter ou réduire des instances, ou les nettoyer lorsqu'elles ne sont plus nécessaires, ce qui vous permet de limiter les coûts.

Les métriques d'application sont capturées sous forme de données de séries temporelles. L'agrégation et la visualisation des métriques capturées peut permettre d'identifier des problèmes de performance courants :

* Temps de réponse HTTP trop longs sur tout ou partie des routes
* Débit médiocre dans l'application
* Pics de demandes entraînant des ralentissements
* Utilisation d'UC plus élevée que prévu
* Utilisation élevée ou en augmentation de la mémoire (fuite de mémoire potentielle)

Le tableau de bord Application Metrics intégré ([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")) inclut également un diagramme pour les ‘autres demandes’, qui indique la durée des requêtes pour les bases de données prises en charge (MongoDB, MySQL, Postgres, LevelDB et Redis) et présente les événements Socket.IO et Riak.

Il est possible de générer un rapport sur les noeuds (Node Report) ou un instantané de segment de mémoire (Heap Snapshot) depuis le tableau de bord, afin de permettre une analyse plus approfondie.

## Ajout de métriques à des applications Node.js existantes
{: #add-appmetrics-existing}

Ajoutez des fonctions de surveillance à des applications Express existantes avec le constructeur [`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") afin de transmettre des options de configuration. Par exemple, l'une des options utilise un serveur existant plutôt que de faire appel à `appmetrics-dash` pour démarrer un serveur supplémentaire.

### Installation du tableau de bord
{: #install-appmetrics}

1. Utilisez, par exemple, l'application express “Hello World” suivante :
  ```js
  var express = require('express')
  var app = express()
  app.get('/', function (req, res) {
      res.send('Hello World!')
  })
  app.listen(3000, function () {
      console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

2. Installez le tableau de bord `appmetrics` à l'aide de la commande [npm](https://nodejs.org/){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") suivante :
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. Ajoutez la prise en charge d'`appmetrics-dash` à votre application existante en ajoutant le code suivant :
  ```js
  var dash = require('appmetrics-dash').attach()
  ```
  {: codeblock}

  Cette commande indique à `appmetrics-dash` d'utiliser le serveur déjà créé et ajoute un noeud final `appmetrics-dash`.

  Le code révisé ressemble à présent à l'exemple suivant :
  ```js
  var express = require('express')
  var app = express()
  var dash = require('appmetrics-dash').attach()
  app.get('/', function (req, res) {
    res.send('Hello World!')
  })
  app.listen(3000, function () {
    console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

## Utilisation de métriques des kits de démarrage
{: #appmetrics-starterkits}

Par défaut, les applications Node.js créées à partir de kits de démarrage sont automatiquement fournies avec `appmetrics-dash` et son tableau de bord, mais elles doivent être activées pour utilisation.

Le code appmetrics se trouve dans le fichier source applicatif généré, nommé `/app_name/server/server.js` :
```js
// Uncomment following to enable zipkin tracing, tailor to fit your network configuration:
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## Accès au tableau de bord
{: #access-dashboard}

Après avoir démarré votre application, accédez à `http://<hostname>:<port>/appmetrics-dash` dans un navigateur.

Utilisez l'adresse `localhost:3001/appmetrics-dash` pour des applications s'exécutant en local.
{: tip}

L'interface utilisateur du tableau de bord de surveillance Application Metrics for Node.js fournit une gamme de métriques, incluant les demandes HTTP et le temps d'attente de boucle d'événements comme illustré dans la vidéo suivante : [Monitoring Metrics for Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe").

## Compréhension des données
{: #understanding-data}

![Tableau de bord Appmetrics](images/appmetricsdash-1.png)

La plupart des données sont représentées sous forme de diagrammes linéaires. Les demandes entrantes HTTP, demandes sortantes HTTP et autres demandes indiquent la durée des événements en fonction du temps. Le débit HTTP montre le nombre de demandes par seconde. Les temps de réponse moyens indiquent les cinq demandes HTTP entrantes les plus utilisées qui ont duré le plus longtemps en moyenne. Les diagrammes d'UC et de mémoire montrent l'utilisation du système et des processus dans le temps. Le segment de mémoire indique la taille de segment de mémoire maximale ainsi que son utilisation dans le temps. Le temps d'attente de boucle d'événements montre des exemples de temps d'attente pris à différents moments dans la boucle d'événements Node.js, avec un point pour le temps d'attente le plus court, un pour le temps moyen et un autre pour le plus long (pour chaque exemple pris).

Si un diagramme comporte des points, des informations supplémentaires s'affichent lorsque vous le survolez. Par exemple, `HTTP Incoming Requests` affiche le temps de réponse et l'URL demandée.

Un maximum de 15 minutes de données est affiché pour l'ensemble des diagrammes.

Si une quantité importante de données est générée par l'application surveillée, le tableau de bord commence automatiquement à afficher les données. Lorsque vous regardez à nouveau le graphique des demandes HTTP entrantes (HTTP Incoming Requests), vous pouvez voir que chaque point indique toutes les demandes pour une période de deux secondes. L'infobulle suivante montre le nombre total de demandes avec la durée moyen et le temps le plus long. Ce dernier correspond à la valeur qui est tracée.

![Afficher l'infobulle](images/tooltip-1.png)




