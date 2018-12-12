---

copyright:
  years: 2018
lastupdated: "2018-09-20"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configuration de l'environnement Node.js

En implémentant des principes natifs du cloud, une application Node.js peut passer d'un environnement à un autre, de test en production, sans changement de code ni exercice autre que des chemins de code non testés.

Le problème se pose lorsque des différences significatives existent dans la façon dont la configuration est présentée, en fonction de l'environnement de développement. CloudFoundry, par exemple, utilise des objets JSON convertis en chaînes, tandis que Kubernetes utilise des valeurs non hiérarchiques ou des objets JSON convertis en chaînes. Le développement en local, hormis pour Kubernetes, présente également des considérations variables. Les données d'identification peuvent être présentées différemment pour la version publique et la version privée, ce qui complique encore davantage le fait de conserver à l'identique des applications d'un environnement à un autre.

Que vous deviez ajouter un support {{site.data.keyword.cloud}} à des applications existantes ou créer des applications à l'aide de kits de démarrage, le but est de fournir la portabilité pour des applications Node.js quelle que soit la plateforme de développement.

## Ajout de configuration {{site.data.keyword.cloud_notm}} à des applications Node.js existantes
{: #addcloud-env}

Le module [`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env) agrège des variables d'environnement à partir de différents fournisseurs cloud, tels que CloudFoundry et Kubernetes, afin que l'application ne dépende pas de l'environnement.

### Installation du module `ibm-cloud-env`
1. Installez le module `ibm-cloud-env` à l'aide de la commande suivante :
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. Initialisez le module dans votre code en référençant `mappings.json` comme suit :
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  Si le chemin d'accès au fichier de mappages n'est pas spécifié dans `IBMCloudEnv.init()`, le module tente de charger les mappages depuis le chemin par défaut `/server/config/mappings.json`.
  {: tip}

  Exemple de fichier `mappings.json` :
  ```json
  {
    "service1-credentials": {
        "searchPatterns": [
            "cloudfoundry:my-service1-instance-name", 
            "env:my-service1-credentials", 
            "file:/localdev/my-service1-credentials.json" 
        ]
    },
    "service2-username": {
        "searchPatterns": [
            "cloudfoundry:$.service2[@.name=='my-service2-instance-name'].credentials.username",
            "env:my-service2-credentials:$.username",
            "file:/localdev/my-service1-credentials.json:$.username" 
        ]
    }
  }
  ```
  {: codeblock}

### Utilisation des valeurs d'une application Node.js
Récupérez les valeurs de votre application à l'aide des commandes suivantes.

1. Extraire la variable `service1credentials` :
  ```js
  // this will be a dictionary
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. Extraire la variable `service2username` :
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // this will be a string
  ```
  {: codeblock}

A présent, votre application peut être implémentée dans un environnement d'exécution en faisant abstraction des différences introduites par des fournisseurs de traitement cloud différents.

### filtrage des valeurs pour les balises et libellés
Vous pouvez filtrer les données d'identification générées par le module en fonction des balises et libellés de service, comme illustré dans l'exemple suivant :
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // returns a Json with credentials for specified service tag and label
```
{: codeblock}

## Utilisation du gestionnaire de configuration Node.js depuis les applications du kit de démarrage (Starter Kit)

Les applications Node.js créées avec des [kits de démarrage](https://console.bluemix.net/developer/appservice/starter-kits/) sont automatiquement fournies avec les données d'identification et configurations nécessaires pour l'exécution dans un grand nombre d'environnements de déploiement cloud (CF, K8s, VSI et Functions).

### Présentation des données d'identification de service

Vos informations de configuration d'application pour les services sont stockées dans le fichier `localdev-config.json` du répertoire `/server/config`. Le fichier se trouve dans le répertoire `.gitignore` afin d'empêcher que les informations sensibles ne soient stockées dans Git. Les informations de connexion de tout service configuré qui s'exécute en local, comme le nom d'utilisateur, le mot de passe et le nom d'hôte, sont stockées dans ce fichier.

L'application utilise le gestionnaire de configuration pour lire les informations de connexion et de configuration depuis l'environnement et ce fichier. Elle utilise un fichier `mappings.json` fait sur mesure, situé dans le répertoire `server/config`, pour communiquer l'emplacement des données d'identification pour chaque service.

Les applications exécutées en local peuvent se connecter aux services {{site.data.keyword.cloud_notm}} en utilisant des données d'identification non liées, lues depuis le fichier `mappings.json`. Si vous devez créer des données d'identification non liées, vous pouvez le faire depuis la console Web {{site.data.keyword.cloud_notm}} ou en utilisant la commande `cf create-service-key` de l'[interface CLI CloudFoundry](https://docs.cloudfoundry.org/cf-cli/).

Lorsque vous envoyez par commande push votre application à {{site.data.keyword.cloud_notm}}, ces valeurs ne sont plus utilisées. A la place, l'application se connecte automatiquement aux services liés à l'aide de variables d'environnement.

* **Cloud Foundry** : Les données d'identification du service sont récupérées à partir de la variable d'environnement `VCAP_SERVICES`.

* **Kubernetes** : Les données d'identification du service sont récupérées par service, à partir de variables d'environnement individuelles.

* **{{site.data.keyword.cloud_notm}} Container Service** : Les données d'identification du service sont récupérées à partir de VSI (instances de service virtuel) ou d'{{site.data.keyword.openwhisk}} (Openwhisk).


## Etapes suivantes
{: #next_steps notoc}

`ibm-cloud-config` prend en charge la recherche de valeurs à l'aide des trois types de modèle de recherche suivants : `cloudfoundry`, `env` et `file`. Si vous souhaitez consulter d'autres exemples de modèle de recherche, reportez-vous à la section [Usage](https://github.com/ibm-developer/ibm-cloud-env#usage).
