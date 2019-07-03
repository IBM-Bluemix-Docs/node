---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-05"

keywords: healthcheck node, add healthcheck node, healthcheck endpoint nodes, readiness node, liveness node, endpoint node, probes node, health check node

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilisation d'un diagnostic d'intégrité dans les applications Node.js
{: #node-healthcheck}

Les diagnostics d'intégrité fournissent un mécanisme simple pour déterminer si une application côté serveur se comporte de manière appropriée. Les environnements de cloud, tels [Kubernetes](https://www.ibm.com/cloud/container-service){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") et [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe"), peuvent être configurés pour sonder périodiquement les noeuds finaux d'intégrité afin de déterminer si une instance de votre service est prête à accepter du trafic.

## Présentation du diagnostic d'intégrité
{: #node-healthcheck-overview}

Les diagnostics d'intégrité fournissent un mécanisme simple pour déterminer si une application côté serveur se comporte de manière appropriée. Ils sont généralement consommés via HTTP et utilisent des codes retour standard pour indiquer l'état UP ou DOWN. La valeur de retour d'un diagnostic d'intégrité est variable, mais une réponse JSON minimale telle que `{"status": "UP"}` est typique.

Kubernetes a une notion nuancée de l'intégrité des processus. Il définit deux sondes :

- La sonde de préparation (_**readiness**_) est utilisée pour indiquer si le processus peut traiter les demandes (est routable).

  Kubernetes n'achemine pas le travail vers un conteneur si la sonde de préparation a échoué. Une sonde de préparation peut échouer si un service n'a pas terminé son initialisation, ou s'il est occupé, surchargé ou incapable de traiter les demandes.

- La sonde de vivacité (_**liveness**_) est utilisée pour indiquer si le processus doit être redémarré.

  Kubernetes arrête et redémarre un conteneur lorsqu'une sonde `liveness` échoue. Utilisez des sondes de vivacité pour nettoyer les processus se trouvant dans un état irrécupérable, par exemple, si la mémoire est épuisée, ou si le conteneur ne s'est pas arrêté correctement après un plantage de processus interne.

A titre de comparaison, Cloud Foundry utilise un seul noeud final d'intégrité. Si ce diagnostic échoue, le processus est redémarré, mais s'il réussit, les demandes sont acheminées vers lui. Dans cet environnement, le noeud final réussit rarement lorsque le processus est en cours. Un délai initial est configuré pour reporter la vérification de l'intégrité jusqu'à la fin de l'initialisation du service afin d'éviter les cycles de redémarrage.

Le tableau suivant donne des indications sur les réponses fournies par les noeuds finaux en terme de préparation, de vivacité et d'intégrité.

| Etat    | Préparation                   | Vivacité                   | Intégrité                    |
|----------|-----------------------------|----------------------------|---------------------------|
|          | Non-OK causes no load       | Non-OK causes restart      | Non-OK causes restart     |
| Starting | 503 - Unavailable           | 200 - OK                   | Use delay to avoid test   |
| Up       | 200 - OK                    | 200 - OK                   | 200 - OK                  |
| Stopping | 503 - Unavailable           | 200 - OK                   | 503 - Unavailable         |
| Down     | 503 - Unavailable           | 503 - Unavailable          | 503 - Unavailable         |
| Errored  | 500 - Server Error          | 500 - Server Error         | 500 - Server Error        |
{: caption="Tableau 1. Codes d'état HTTP" caption-side="bottom"}

## Ajout d'un diagnostic d'intégrité à une application Node.js existante
{: #add-healthcheck-existing}

Ajoutez un diagnostic d'intégrité ou de vivacité minimal à une application existante en introduisant une nouvelle route, comme illustré dans l'exemple suivant :
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

Vérifiez l'état de l'application avec un navigateur en accédant au noeud final `/health`.

## Accès au diagnostic d'intégrité depuis des applications de kit de démarrage Node.js
{: #node-healthcheck-starterkit}

Par défaut, lorsque vous générez une application Node.js à l'aide d'un kit de démarrage, un noeud final de diagnostic d'intégrité basique (non autorisé) est disponible dans `/health` afin de vérifier le statut (UP/DOWN) de cette application.

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

## Recommandations pour les sondes de préparation et de vivacité
{: #node-readiness-probes}

Les sondes de préparation peuvent inclure la viabilité des connexions aux services situés en aval dans leur résultat si aucune rétromigration acceptable n'existe lorsque le service en aval n'est pas disponible. Il ne s'agit pas d'appeler directement le diagnostic d'intégrité qui est fourni par le service en aval, car l'infrastructure le vérifie pour vous. Vous devez plutôt envisager de vérifier l'état des références existantes de votre application aux services en aval : il peut s'agir d'une connexion JMS à WebSphere MQ, ou d'un consommateur ou producteur Kafka initialisé. Si vous vérifiez la viabilité des références internes aux services en aval, mettez en cache le résultat pour minimiser l'impact de la vérification de santé sur votre application.

Une sonde de vivacité, en revanche, est délibérée quant à ce qui est vérifié, car une défaillance entraîne l'arrêt immédiat du processus. Un noeud final http simple qui renvoie toujours `{"statut" : "UP"}` avec le code de statut `200` est un choix raisonnable.

### Ajout d'une prise en charge de la préparation et de la vivacité pour Kubernetes
{: #kube-readiness-add}

La bibliothèque [`cloud-health-connect`](https://github.com/CloudNativeJS/cloud-health-connect){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") de [CloudNativeJS](https://github.com/cloudnativejs){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") fournit une infrastructure permettant de définir des noeuds finaux de préparation et de vivacité séparés dans Node qui permettent une composition des sources pour l'état de chaque noeud final.

## Configuration des sondes de préparation et de vivacité dans Kubernetes
{: #kube-readiness-config}

Sondez la vivacité et l'état de préparation lors de votre déploiement Kubernetes. Les deux sondes utilisent les mêmes paramètres de configuration :

* L'agent kubelet attend la durée définie par `initialDelaySeconds` avant la première sonde.

* L'agent kubelet sonde le service toutes les `periodSeconds` secondes. La valeur par défaut est 1.

* La sonde expire au bout de `timeoutSeconds` secondes. La valeur minimale et la valeur par défaut sont de 1.

* La sonde réussit si elle est exécutée `successThreshold` fois après une panne. La valeur minimale et la valeur par défaut sont de 1. La valeur doit être 1 pour les sondes de vivacité.

* Lorsqu'un pod démarre et que la sonde échoue, Kubernetes essaie `failureThreshold` fois de redémarrer le pod puis renonce. La valeur minimale est 1 et la valeur par défaut est 3.
    - Pour une sonde de vivacité, "renoncer" signifie redémarrer le pod.
    - Pour une sonde de préparation, "renoncer" signifie marquer le pod comme `Unready`.

Pour éviter les cycles de redémarrage, réglez `livenessProbe.initialDelaySeconds` sur une durée supérieure à la durée d'initialisation de votre service. Vous pouvez ensuite utiliser une valeur plus courte pour `readinessProbe.initialDelaySeconds` pour acheminer les demandes vers le service dès qu'il est prêt.

Exemple de configuration :
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```
{: codeblock}

Pour plus d'informations, voir [Configure liveness and readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe").
