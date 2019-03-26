---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Vorhandene Node.js-Anwendungen für Cloudbereitstellung aktivieren
{: #enable_existing}

Sie können die Dateien, die dazu erforderlich sind, dass die Node.js-Anwendung unter {{site.data.keyword.cloud}} ausgeführt wird, mithilfe des [{{site.data.keyword.dev_cli_long}}-CLI-Befehls 'enable'](/docs/cli/idt/commands.html#enable) generieren.

## Anwendung aktivieren
{: #enable_app}

Geben Sie den folgenden Befehl im Stammverzeichnis Ihres Node-Projekts ein:
```
ibmcloud dev enable
```
{: codeblock}

Sie werden dazu aufgefordert, das Node.js-Projekt zu überprüfen; antworten Sie mit `y` für 'Ja'. Mit dem Befehl `enable` können auch Services erstellt und an Ihre Anwendung gebunden werden. Antworten Sie im Falle dieses Basisbeispiels mit `n` für 'Nein'.

Sehen Sie sich die folgende Beispielausgabe an:
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

## Cloudfähige Node-Anwendung erstellen und bereitstellen
{: #build_deploy}

Als Nächstes erstellen Sie Ihre Anwendung mithilfe des Befehls [`build`](/docs/cli/idt/commands.html#build):
```
ibmcloud dev build
```
{: codeblock}

Wenn die Erstellung erfolgreich ausgeführt wurde, können Sie Ihre Anwendung in {{site.data.keyword.cloud_notm}} bereitstellen, und zwar mit dem folgenden Befehl des Typs [`deploy`](/docs/cli/idt/commands.html#deploy):
```
ibmcloud dev deploy
```
{: codeblock}

## Maßnahmen beim Fehlschlagen der Erstellung oder Bereitstellung für die aktivierte Anwendung
{: #build_failure}

Es werden nicht alle Anwendungen erfolgreich durch den Befehl `enable` aktiviert. Es werden alle Möglichkeiten ausgeschöpft, um die Konfiguration zu bestimmen, die erforderlich ist, um Ihre Anwendung cloudfähig zu machen. Die Ursprungsanwendung muss mindestens die Befehle `npm install` und `npm start` erfolgreich ausführen können. Bei einer Anwendung, die mit einem anderen Ausführungsbefehl als `npm start` arbeitet, können Sie die `CMD`-Zeile in der `Dockerfile` ändern.

Wird Ihre Anwendung nach der Ausführung von `ibmcloud dev enable` nicht erstellt und/oder bereitgestellt, können Sie die generierten Dateien manuell ändern, um die Cloudaktivierung abzuschließen.

### Generierte Dateien zur Cloudaktivierung manuell ändern
{: #manual_modify}

Sobald sie aktiviert ist, nutzt Ihre Anwendung für die lokale Ausführung Docker. Wenn Sie noch nicht über eine Dockerfile verfügen, wird eine solche für Sie erstellt und der Port `3000` wird zugänglich gemacht.

Wenn für Ihre Anwendung jedoch ein Service wie MongoDB oder Redis erforderlich ist, der in einem separaten Container ausgeführt wird, empfehlen wir die Verwendung des Befehls `docker-compose`, damit sowohl Ihre Anwendung als auch der erforderliche Service gleichzeitig automatisch installiert werden.

Im Folgenden sehen Sie ein Beispiel für `docker-compose.yml`, das Sie im Stammverzeichnis erstellen können:
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

Dadurch wird Ihre Anwendung bei Port `3000` zugänglich gemacht; der MongoDB-Server ist für die Clientverbindung über Port `27017` verfügbar.

Sie müssen die folgende Zeile in `cli-config.yml` aktualisieren, damit auf die neue Dockerfile verwiesen wird: 
```yaml
# Name der Dockerfile für den Ausführungscontainer
dockerfile-run : "Dockerfile"
```
{: codeblock}

Wie folgt aktualisieren:
```yaml
# Name der Dockerfile für den Ausführungscontainer
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## Nächste Schritte
{: #next_steps-existing notoc}

Weitere Informationen finden Sie in [IBM Cloud Developer Tools CLI](/docs/cli/idt/commands.html#idt-cli).
