---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-28"

keywords: ibmcloud dev enable, nodejs cloud deployment, cloud enable nodejs, deploy nodejs, build nodejs cloud, nodejs debug

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Activation des applications Node.js existantes pour le déploiement en cloud
{: #enable_existing}

Vous pouvez générer les fichiers permettant à votre application Node.js de s'exécuter sur {{site.data.keyword.cloud}} en utilisant la commande enable de l'interface de ligne de commande [{{site.data.keyword.dev_cli_long}}](/docs/cli/idt?topic=cloud-cli-idt-cli#enable).

## Activation de votre application
{: #enable_app}

Entrez la commande suivante à partir du répertoire principal de votre projet Node :
```
ibmcloud dev enable
```
{: codeblock}

Lorsque vous êtes invité à vérifier le projet Node.js, répondez `y`. La commande `enable` peut également créer des services et les lier à votre application. Dans cet exemple de base, répondez `n`.

Exemple de sortie :
```
demo: $ ibmcloud dev enable
The enable feature is currently in Beta.
Please provide your experience and feedback at: https://ibm-cloud-tech.slack.com/messages/developer-tools/
Only server-side applications are supported by the enable feature
? Node application discovered. Do you want to proceed with this language choice? [o/n]> o

Using the resource group default (default) of your account
? Do you want to create a service and connect it to this application? [y/n]> n
                                    
The following files were added to your application:

.cfignore
.dockerignore
Dockerfile-tools
Dockerfile
Jenkinsfile
README.md.merge
cli-config.yml
run-debug
manifest.yml
run-dev
.bluemix/deploy.json
.bluemix/pipeline.yml
.bluemix/toolchain.yml
.bluemix/scripts/container_build.sh
.bluemix/scripts/kube_deploy.sh
chart/nodeapp/Chart.yaml
chart/nodeapp/bindings.yaml
chart/nodeapp/values.yaml
chart/nodeapp/templates/hpa.yaml
chart/nodeapp/templates/service.yaml
chart/nodeapp/templates/deployment.yaml
chart/nodeapp/templates/basedeployment.yaml
chart/nodeapp/templates/istio.yaml
.gitignore.merge
LICENSE


The following file conflicts need to be merged manually:

README.md.merge
.gitignore.merge

Files can be easily compared with 'git diff' or a similar tool.
The application, nodeapp, has been successfully saved into the current directory.
demo: $
```
{: screen}

## Génération et déploiement d'une application Node activée pour le cloud
{: #build_deploy}

Ensuite, générez votre application avec la commande [`build`](/docs/cli/idt?topic=cloud-cli-idt-cli#build) :
```
ibmcloud dev build
```
{: codeblock}

Si la génération réussit, vous pouvez déployer votre application dans {{site.data.keyword.cloud_notm}} avec la commande [`deploy`](/docs/cli/idt?topic=cloud-cli-idt-cli#deploy) suivante :
```
ibmcloud dev deploy
```
{: codeblock}

## Que faire si votre application activée n'est pas générée ou déployée ?
{: #build_failure}

Toutes les applications ne s'activent pas correctement à l'aide de la commande `enable`. Vous devrez tenter de déterminer quelle est la configuration nécessaire pour "activer votre application pour le cloud". Au minimum, l'application d'origine doit être capable d'exécuter `npm install` et `npm start` correctement. Si une application utilise une commande d'exécution autre que `npm start`, vous pouvez modifier la ligne `CMD` dans `Dockerfile`.

Si votre application n'est pas générée et/ou déployée après l'exécution de `ibmcloud dev enable`, vous pouvez modifier les fichiers générés pour activer l'application pour le cloud.

### Modification manuelle des fichiers d'activation du cloud générés
{: #manual_modify}

Une fois activée, votre application utilise Docker pour s'exécuter localement. S'il n'existe pas déjà, un fichier Docker est créé pour vous et le port `3000` est exposé.

Cependant, si votre application nécessite un service tel que MongoDB ou Redis s'exécutant dans un conteneur séparé, nous vous recommandons d'utiliser `docker-compose` pour faire fonctionner simultanément votre application et le service requis.

Exemple d'un fichier `docker-compose.yml` pouvant être créé dans le répertoire racine :
```yaml
version: '2'
services:
  web:
    build: .
    image: "app-express-run"
    container_name: "app-express-run"
    command: npm run start
    ports:
      - "3000:3000"
    links:
      - mongo
    environment:
      MONGO_URL: "mongo"
      MONGO_DB_NAME: "comments"
      NODE_ENV: "development"
      PORT: 3000
  mongo:
    image: mongo
    ports:
      - "27017:27017" 
```
{: codeblock}

Ceci exposera votre application sur le port `3000` et le serveur MongoDB sera disponible pour la connexion client via le port `27017`.

Mettez à jour la ligne suivante dans `cli-config.yml` de manière à pointer vers le nouveau fichier Docker : 
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "Dockerfile"
```
{: codeblock}

Remplacez par :
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## Etapes suivantes
{: #next_steps-existing notoc}

Pour plus d'informations, voir [Interface de ligne de commande IBM Cloud Developer Tools](/docs/cli/idt?topic=cloud-cli-idt-cli#idt-cli).
