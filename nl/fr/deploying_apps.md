---

copyright:
  years: 2018
lastupdated: "2018-08-15"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Déploiement et intégration d'applications Node.js
{: #deploy_apps}

Vous pouvez déployer vos applications Node.js avec une chaîne d'outils ou en utilisant l'interface de ligne de commande. Une chaîne d'outils est un ensemble d'intégrations d'outils. L'interface de ligne de commande constitue un moyen simple de déployer vos applications et vos instances de service.

Pour plus d'informations, voir [Déploiement d'applications](../apps/dep-app-tool.html).

## Développement d'applications Node.js sans serveur
{: #serverless}

Pour faciliter le déploiement sans serveur, vous pouvez utiliser l'offre IBM Functions as a Service (FaaS), {{site.data.keyword.openwhisk}}. {{site.data.keyword.openwhisk_short}} permet d'exécuter la logique d'application en réponse à des événements ou à des appels directs du Web ou d'applis mobiles sur HTTP sans la mise à disposition ou la gestion de serveurs.

{{site.data.keyword.openwhisk_short}} effectue des tâches d'administration système, comme la mise à l'échelle automatique, la gestion de la disponibilité et la maintenance, ce qui permet aux développeurs de rester concentrés sur l'écriture de la logique d'application.

Pour plus d'informations, voir [Développement d'applis sans serveur](../apps/deploying/functions.html).

## Intégration de services de back-end avec un logiciel SDK généré
{: #backend_gensdk}

Le plug-in générateur du logiciel SDK {{site.data.keyword.IBM_notm}} intègre aisément des services de back end à votre appli à l'aide d'un logiciel SDK généré. Lorsqu'une modification d'API REST se produit, vous pouvez régénérer le logiciel SDK, puis remplacer l'ancien par une mise à niveau SDK transparente. Vous pouvez aussi intégrer l'interface de ligne de commande dans un pipeline DevOps et vous assurer que le SDK est toujours cohérent avec la spécification d'API chaque fois que l'application est générée.

Pour plus d'informations, voir [Intégration de services de back-end avec un logiciel SDK généré](../apps/deploying/api_management.html).

## Déploiement dans un cluster Kubernetes
{: #deploy_kube}

Vous pouvez apprendre à utiliser {{site.data.keyword.cloud_notm}} Kubernetes Service pour déployer une appli Node.js conteneurisée qui utilise Watson Tone Analyzer. Dans le scénario fourni, une firme PR fictive utilise le service {{site.data.keyword.cloud_notm}} pour analyser ses communiqués de presse et recevoir des commentaires en retour sur la tonalité de ses messages. Pour plus d'informations, voir le tutoriel [Déploiement d'applis dans les clusters](../containers/cs_tutorials_apps.html).

## Déploiement sur un serveur virtuel
{: #virtual_deploy}

Un service virtuel offre un plus haut degré de transparence, de prévisibilité et d'automatisation pour tous les types de charge de travail. Terraform est utilisé pour générer, changer et gérer les versions de votre infrastructure en toute sécurité et de manière efficace.

Pour plus d'informations, voir [Déploiement sur un serveur virtuel](../apps/vsi-deploy.html).
