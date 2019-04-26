---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-28"

keywords: configure node env, node environment, node credentials, ibm-cloud-env node

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Node.js-Umgebung konfigurieren
{: #configure-nodejs}

Durch die Implementierung von cloudbasierten Prinzipien kann eine Node.js-Anwendung von einer Umgebung zu einer anderen wechseln, z. B. von der Testumgebung zur Produktionsumgebung, ohne dass der Code geändert wurde oder anderweitig nicht getestete Codepfade ausgeübt werden.

Das Problem tritt auf, wenn in Abhängigkeit von der Entwicklungsumgebung signifikante Unterschiede in der Art und Weise gegeben sind, wie die Konfiguration dargestellt wird. Beispiel: Cloud Foundry, das in Zeichenfolgen konvertierte JSON-Objekte im Gegensatz zu Kubernetes verwendet, das entweder unstrukturierte Werte oder in Zeichenfolgen konvertierte JSON-Objekte verwendet. Für die lokale Entwicklung, abgesehen von Kubernetes, sind zudem noch andere Aspekte zu berücksichtigen. Berechtigungsnachweise können für Public und Private-Instanzen unterschiedlich angezeigt werden, was es für Apps noch weiter erschwert, in Umgebungen unverändert zu bleiben.

Unabhängig davon, ob Sie {{site.data.keyword.cloud}}-Unterstützung zu vorhandenen Anwendungen hinzufügen oder Apps mit Starter-Kits erstellen müssen, besteht das Ziel darin, Portierbarkeit für Node.js-Apps auf einer beliebigen Entwicklungsplattform bereitzustellen.

## {{site.data.keyword.cloud_notm}}-Konfiguration zu vorhandenen Node.js-Anwendungen hinzufügen
{: #addcloud-env-nodejs}

Das Modul [`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") fasst Umgebungsvariablen von verschiedenen Cloud-Providern wie Cloud Foundry und Kubernetes zusammen, sodass die Anwendung von der Umgebung unabhängig ist. 

### Modul `ibm-cloud-env` installieren
{: #install-module-nodejs}

1. Installieren Sie das Modul `ibm-cloud-env` mit dem folgenden Befehl:
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. Initialisieren Sie das Modul in Ihrem Code, indem Sie die Datei `mappings.json` wie folgt referenzieren:
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  Wenn der Pfad der Zuordnungsdatei in `IBMCloudEnv.init()` nicht angegeben ist, versucht das Modul, Zuordnungen von einem Standardpfad aus `/server/config/mappings.json` zu laden.
  {: tip}

  Beispiel für die Datei `mappings.json`:
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
        "searchPatterns":[
            "cloudfoundry:$.service2[@.name=='my-service2-instance-name'].credentials.username",
            "env:my-service2-credentials:$.username",
            "file:/localdev/my-service1-credentials.json:$.username" 
        ]
    }
  }
  ```
  {: codeblock}

### Serviceberechtigungsnachweise abrufen
{: #nodejs-get-creds}

Rufen Sie die Werte in Ihrer Anwendung mithilfe der folgenden Befehle ab.

1. Rufen Sie die Variable `service1credentials` ab:
  ```js
  // Dies ist ein Verzeichnis
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. Rufen Sie die Variable `service2username` ab:
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // Dies ist eine Zeichenfolge
  ```
  {: codeblock}

Nun kann Ihre Anwendung in einer beliebigen Laufzeitumgebung implementiert werden, indem die Unterschiede, die von verschiedenen Cloud-Computing-Anbietern eingeführt werden, abstrahiert werden.

### Werte für Tags und Bezeichnungen filtern
{: #filter-values-nodejs}

Sie können Berechtigungsnachweise, die vom Modul generiert werden, basierend auf Service-Tags und Servicebezeichnungen filtern, wie im folgenden Beispiel gezeigt:
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // Gibt eine JSON mit Berechtigungsnachweisen für den angegebenen Service-Tag und die Bezeichnung zurück
```
{: codeblock}

## Node.js-Konfigurationsmanager über Starter-Kit-Apps verwenden
{: #nodejs-config-skit}

Mit [Starter-Kits](https://cloud.ibm.com/developer/appservice/starter-kits/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") erstellte Node.js-Apps werden automatisch mit Berechtigungsnachweisen und Konfigurationen ausgestattet, die für die Ausführung in vielen Cloudbereitstellungsumgebungen (CF, K8s, VSI und Functions) erforderlich sind. 

### Informationen zu Serviceberechtigungsnachweisen
{: #credentials-nodejs}

Ihre Anwendungskonfigurationsdaten für Services sind in der Datei `localdev-config.json` im Verzeichnis `/server/config` gespeichert. Die Datei befindet sich im Verzeichnis `.gitignore`, um zu verhindern, dass sensible Informationen in Git gespeichert werden. Die Verbindungsinformationen für jeden konfigurierten Service, der lokal ausgeführt wird, wie der Benutzername, das Kennwort und der Hostname, werden in dieser Datei gespeichert.

Die Anwendung verwendet den Konfigurationsmanager, um die Verbindungs- und Konfigurationsinformationen aus der Umgebung und dieser Datei zu lesen. Sie verwendet eine kundenspezifische Datei `mappings.json` im Verzeichnis `server/config`, um zu kommunizieren, wo sich die Berechtigungsnachweise für jeden Service befinden.

Lokal ausgeführte Anwendungen können mithilfe von nicht gebundenen Berechtigungsnachweisen, die aus der Datei `mappings.json` gelesen werden, eine Verbindung zu {{site.data.keyword.cloud_notm}}-Services herstellen. Wenn Sie nicht gebundene Berechtigungsnachweise erstellen müssen, können Sie dazu die {{site.data.keyword.cloud_notm}}-Webkonsole oder den Befehl `cf create-service-key` der [Cloud Foundry-CLI](https://docs.cloudfoundry.org/cf-cli/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") verwenden. 

Wenn Sie Ihre Anwendung mit einer Push-Operation an {{site.data.keyword.cloud_notm}} übertragen, werden diese Werte nicht mehr verwendet. Stattdessen stellt die Anwendung automatisch durch die Verwendung von Umgebungsvariablen eine Verbindung zu gebundenen Services her.

* **Cloud Foundry**: Serviceberechtigungsnachweise werden aus der Umgebungsvariablen `VCAP_SERVICES` abgerufen. Weitere Informationen zu Cloud Foundry Enrprise Edition enthält dieses [Lernprogramm zur Einführung](/docs/cloud-foundry?topic=cloud-foundry-getting-started#getting-started).

* **Kubernetes**: Serviceberechtigungsnachweise werden pro Service aus einzelnen Umgebungsvariablen abgerufen.

* ** {{site.data.keyword.cloud_notm}} Container-Service**: Serviceberechtigungsnachweise werden aus VSIs oder {{site.data.keyword.openwhisk}} (Openwhisk) übernommen.

## Nächste Schritte
{: #next_steps-config notoc}

`ibm-cloud-config` unterstützt die Suche nach Werten unter Verwendung von drei Suchmustertypen: `cloudfoundry`, `env` und `file`. Wenn Sie sich weitere unterstützte Suchmuster und Suchmusterbeispiele ansehen möchten, lesen Sie den Abschnitt zur [Syntax](https://github.com/ibm-developer/ibm-cloud-env#usage){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link"). 
