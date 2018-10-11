---

copyright:
  years: 2018
lastupdated: "2018-09-18"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilisation du diagnostic d'intégrité dans les applications Node.js
{: #healthcheck}

Les diagnostics d'intégrité fournissent un mécanisme simple pour déterminer si une application côté serveur se comporte de manière appropriée. De nombreux environnements de déploiement, tels que [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) et [Kubernetes](https://www.ibm.com/cloud/container-service), peuvent être configurés de manière à interroger périodiquement des noeuds finaux de santé afin de déterminer si une instance d'un service est prête à accepter du trafic.

## Présentation du diagnostic d'intégrité
{: #overview}

Les diagnostics d'intégrité sont généralement accessibles via `HTTP` et utilisent des codes retour standard pour indiquer un statut `UP` ou `DOWN`. Les exemples incluent le renvoi de la valeur `200` pour `UP` et la valeur `5xx` pour `DOWN`. Par exemple, un code retour `503` est utilisé quand l'application ne peut pas gérer les demandes et un code `500` quand le serveur rencontre une condition d'erreur. La valeur renvoyée par un diagnostic d'intégrité est variable, mais une réponse JSON minimale, telle que `{“status”: “UP”}` assure la cohérence.

Kubernetes définit des envois-test de [signal de présence](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) et de [disponibilité](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) :

* L'envoi-test de signal de présence permet à une application d'indiquer si le processus peut être lancé. Un sous-ensemble de considérations s'applique : une vérification de `liveness` peut échouer si, par exemple, un seuil de mémoire est atteint. Si votre appli s'exécute sur Kubernetes, pensez à ajouter un noeud final `liveness` afin de garantir le redémarrage de votre processus si nécessaire.

* L'envoi-test de l'état prêt est utilisé pour les décisions automatiques de routage. Le succès ou l'échec indique si l'application peut accepter un nouveau travail. Ce noeud final peut inclure des considérations relatives aux services en aval requis. Envisagez de mettre en cache le résultat des vérifications de service en aval afin de minimiser la charge globale sur le système (par exemple, le test de la connectivité à la base de données plus d'une fois par seconde).

Cloud Foundry utilise un seul noeud final de santé. Si la vérification échoue, Cloud Foundry redémarre le processus. Si elle réussi, Cloud Foundry considère que le processus peut gérer le nouveau travail. Ce diagnostic d'intégrité fait du noeud final unique une sorte de combinaison entre liveness et readiness.

## Ajout de diagnostic d'intégrité à une application Node.js existante
{: #add-healthcheck-existing}

Pour ajouter un noeud final de diagnostic d'intégrité à une application existante, commencez par introduire une nouvelle route comme illustré dans l'exemple suivant : 
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

L'ajout de critères significatifs ici garantit qu'une réponse satisfaisante du noeud final `/health` correspond bien au fait que le service est prêt à gérer les demandes.

## Accès au diagnostic d'intégrité depuis des applications de kit de démarrage Node.js
{: #healthcheck-starterkit}

Par défaut, lorsque vous générez une appli Node.js à l'aide d'un kit de démarrage, un noeud final de diagnostic d'intégrité basique (non autorisé) est disponible dans `/health` afin de vérifier le statut de l'appli (UP/DOWN).

Le code du noeud final de diagnostic d'intégrité est fourni par le fichier `/server/routers/health.js` suivant :
```js
var express = require('express');

module.exports = function(app) {
  var router = express.Router();

  router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });

  app.use("/health", router);
}
```
{: codeblock}

Vous pouvez personnaliser la vérification afin de vous assurer qu'elle aboutit uniquement quand l'application est prête à accepter du trafic.
{: tip}
