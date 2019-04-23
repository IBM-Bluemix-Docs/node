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

# Abilitazione delle applicazioni Node.js esistenti per la distribuzione cloud
{: #enable_existing}

Puoi generare i file necessari per abilitare l'esecuzione della tua applicazione Node.js su {{site.data.keyword.cloud}} utilizzando il [comando enable della CLI {{site.data.keyword.dev_cli_long}}](/docs/cli/idt?topic=cloud-cli-idt-cli#enable).

## Abilitazione della tua applicazione
{: #enable_app}

Immetti il seguente comando dalla directory root del tuo progetto Node:
```
ibmcloud dev enable
```
{: codeblock}

Ti viene richiesto di verificare il progetto Node.js, rispondi `y`. Il comando `enable` può inoltre creare i servizi e associarli alla tua applicazione. Per questo esempio di base, rispondi `n`.

Consulta il seguente output di esempio:
```
demo: $ ibmcloud dev enable
The enable feature is currently in Beta.
Please provide your experience and feedback at: https://ibm-cloud-tech.slack.com/messages/developer-tools/
Only server-side applications are supported by the enable feature
? Node application discovered. Do you want to proceed with this language choice? [y/n]> y

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

## Crea e distribuisci un'applicazione Node abilitata per il cloud
{: #build_deploy}

Successivamente, crea la tua applicazione con il comando [`build`](/docs/cli/idt?topic=cloud-cli-idt-cli#build):
```
ibmcloud dev build
```
{: codeblock}

Se la creazione viene completata correttamente, puoi distribuire la tua applicazione a {{site.data.keyword.cloud_notm}} con il seguente comando [`deploy`](/docs/cli/idt?topic=cloud-cli-idt-cli#deploy):
```
ibmcloud dev deploy
```
{: codeblock}

## Cosa fare se la tua applicazione abilitata non viene creata o distribuita
{: #build_failure}

Non tutte le applicazioni vengo abilitate correttamente dal comando `enable`. Viene eseguito il miglior tentativo possibile per determinare la configurazione necessaria per "abilitare per il cloud" la tua applicazione. Come minimo, l'applicazione originale deve essere in grado di eseguire `npm install` e `npm start` correttamente. Per un'applicazione che utilizza un comando di esecuzione diverso rispetto a `npm start`, puoi modificare la riga `CMD` nel `Dockerfile`.

Se la tua applicazione non viene creata e/o distribuita dopo l'esecuzione di `ibmcloud dev enable`, puoi modificare manualmente i file generati per completare l'abilitazione cloud.

### Modifica manuale dei file di abilitazione cloud generati
{: #manual_modify}

Una volta abilitata, la tua applicazione utilizza docker per l'esecuzione in locale. Se ancora non disponi di un Dockerfile, ne viene creato uno per te e viene esposta la porta `3000`.

Tuttavia, se la tua applicazione richiede un servizio come MongoDB o Redis che viene eseguito in un contenitore separato, ti consigliamo di utilizzare `docker-compose` per configurare contemporaneamente la tua applicazione e il tuo servizio richiesto.

Consulta il seguente esempio `docker-compose.yml` che puoi creare nella directory root:
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

Questo esporrà la tua applicazione sulla porta `3000` e il server MongoDB sarà disponibile per la connessione client tramite la porta `27017`.

Devi aggiornare la seguente riga in `cli-config.yml` in modo che punti al nuovo Dockerfile: 
```yaml
# Il nome del dockerfile per il contenitore di esecuzione
dockerfile-run : "Dockerfile"
```
{: codeblock}

Aggiorna con:
```yaml
# Il nome del dockerfile per il contenitore di esecuzione
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## Passi successivi
{: #next_steps-existing notoc}

Per ulteriori informazioni, consulta [CLI IBM Cloud Developer Tools](/docs/cli/idt?topic=cloud-cli-idt-cli#idt-cli).
