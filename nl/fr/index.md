---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-25"

keywords: node getting started, node cloud native, create node app, add node service, node programming guide, node guide

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Tutoriel d'initiation
{: #node-getting-started}

Le tutoriel suivant vous guide dans les procédures de génération, d'exécution en local et de déploiement d'une application Node.js à l'aide des outils {{site.data.keyword.cloud_notm}} fournis. Vous pouvez utiliser les outils [{{site.data.keyword.dev_cli_long}}](/docs/cli?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli) sur la ligne de commande ou le service Web [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") comme illustré dans la procédure suivante du tutoriel. L'utilisation de l'une ou l'autre de ces méthodes vous permet de générer en quelques minutes une application Node.js prête à l'emploi.

Veillez à utiliser l'édition du supplément à long terme (LTS) Node.js la plus récente.

## Création d'une application Node.js
{: #node-create-project}

1. Sur la page des [kits de démarrage](https://cloud.ibm.com/developer/appservice/starter-kits){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") de la console {{site.data.keyword.dev_console}}, sélectionnez un kit de démarrage écrit en `Node.js`. Vous pouvez également créer une application de démarrage vide en cliquant sur **Créer une application** et en sélectionnant `Node.js` comme langage.

    Vous devez être connecté à un compte {{site.data.keyword.cloud_notm}} pour créer une application. Si vous n'avez pas de compte, vous pouvez vous [inscrire pour un compte gratuit](https://cloud.ibm.com/registration){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe").
    {: tip}

2. Cliquez sur **Créer une application**.
3. Donnez un **nom** à votre application. Un nom d'application générique est fourni si vous souhaitez l'utiliser.
4. Cliquez sur **Créer**. Une fois votre projet créé, vous pouvez le déployer à l'aide d'une chaîne d'outils ou poursuivre pour générer et déployer le projet depuis une ligne de commande.
5. Pour créer une chaîne d'outils de déploiement dans le tableau de bord, cliquez sur **Déployer**. Configurez votre cible de déploiement en fonction des instructions s'appliquant à la méthode choisie :
  * **Déployer dans [IBM Kubernetes Service](/docs/apps/deploying?topic=creating-apps-containers-kube#containers)**. Cette option crée un cluster d'hôtes, appelé noeuds worker, afin de déployer et de gérer des conteneurs d'application à haute disponibilité. Vous pouvez créer un cluster ou effectuer un déploiement sur un cluster existant.
  * **Déployer dans Cloud Foundry**. Cette option déploie votre application cloud native sans qu'il soit nécessaire de gérer l'infrastructure sous-jacente. Si votre compte a accès à {{site.data.keyword.cfee_full_notm}}, vous pouvez sélectionner un déployeur de type **[Public Cloud](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf#about-cf)** ou **[Enterprise Environment](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee#cfee)**, que vous pouvez utiliser pour créer et gérer des environnements isolés pour l'hébergement de vos applications Cloud Foundry exclusivement pour votre entreprise.
  * **Déployer sur un [serveur virtuel](/docs/apps?topic=creating-apps-vsi-deploy#vsi-deploy)**. Cette option met à disposition une instance de serveur virtuel, charge une image qui inclut votre application, crée une chaîne d'outils DevOps et initie pour vous le premier cycle de déploiement.

6. Finalisez vos options puis cliquez sur **Créer** pour créer la chaîne d'outils.
7. Si vous choisissez de continuer avec l'interface de ligne de commande plutôt que la chaîne d'outils, téléchargez le projet sur votre machine locale, `unzip` puis `cd` dans le répertoire racine. Vous pouvez à présent installer les prérequis à l'aide de la méthode de commande de vos plateformes :

    MacOS :
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows (exécuter en tant qu'administrateur) :
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## Ajout d'un service
{: #node-add-service}

1. Revenez à votre projet dans {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}.
2. Cliquez sur **Ajouter un service**, sélectionnez la catégorie du service à ajouter, cliquez sur **Suivant** puis choisissez votre service. Par exemple, pour ajouter une base de données NoSQL à votre application, cliquez sur la catégorie **Data**, puis sélectionnez **Cloudant** pour bénéficier d'un développement gratuit avec le plan Lite. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} met à disposition le service pour vous en fonction du plan sélectionné.
Remarque : Si vous avez précédemment mis à disposition le service que vous prévoyez d'utiliser, choisissez la catégorie **existante**.
3. Une fois le service mis à disposition, cliquez sur **Télécharger le code** pour régénérer le projet avec le logiciel SDK qui se connecte à votre service.

<!--
<video of creating a project and adding a service>
-->

## Exécution d'applications en local
{: #node-run-local}

1. Utilisez la commande `ibmcloud dev build` pour générer votre application.
2. Utilisez la commande `ibmcloud dev run` pour exécuter l'application en local. Votre application est exécutée dans les conteneurs Docker sur votre système local. Vous pouvez tester votre application dans un navigateur en accédant à `http://localhost:3000`.
3. Un noeud final de diagnostic d'intégrité est disponible à l'adresse `http://localhost:3000/health`.
4. Vous pouvez accéder au tableau de bord [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/){: new_window} ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe") sur la page `http://localhost:3000/appmetrics-dash`.

<!--
<video>
-->

## Déploiement dans IBM Cloud
{: #deploy_cloud}

Utilisez la commande `ibmcloud dev deploy` pour un déploiement sur {{site.data.keyword.cloud_notm}} en tant qu'application Cloud Foundry. 

Pour un déploiement sur IBM Container Services dans {{site.data.keyword.cloud_notm}}, utilisez la commande suivante :
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## Etapes suivantes
{: #node-tutorial-nextsteps notoc}

Continuez à consultez les rubriques du guide de programmation de Node.js. Pour des déploiements plus avancés, vous pouvez apprendre à créer un cluster Kubernetes sur lequel déployer votre application Node.js.

### Configuration d'un cluster Kubernetes
Pour plus d'informations sur la configuration d'un cluster Kubernetes dans {{site.data.keyword.cloud_notm}}, consultez la [procédure du tutoriel](/docs/containers?topic=containers-clusters#clusters).

### Déploiement d'applications Node.js sur un cluster Kubernetes
Apprenez à [déployer des applications Node.js sur un cluster Kubernetes](/docs/containers?topic=containers-cs_apps_tutorial#cs_apps_tutorial).
