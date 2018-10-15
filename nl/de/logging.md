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

# Protokollierung in Node.js
{: #logging_nodejs}

Protokollnachrichten sind Zeichenfolgen mit Kontextinformationen zum Status und der Aktivität des Mikroservice zu dem Zeitpunkt, an dem der Protokolleintrag erfolgt. Protokolle sind erforderlich, um zu diagnostizieren, wie und warum Services fehlschlagen, und spielen eine unterstützende Rolle für [appmetrics](appmetrics.html) bei der Anwendungsstatusprüfung.

Angesichts der transienten Natur von Prozessen in Cloud-Umgebungen müssen Protokolle erfasst und an eine andere Stelle gesendet werden, in der Regel an eine zentrale Position für die Analyse. Die konsistenteste Möglichkeit, sich in Cloud-Umgebungen anzumelden, besteht darin, Protokolleinträge an die Standardausgabe- und Fehlerdatenströme zu senden; die Infrastruktur ist dann für die restliche Verarbeitung zuständig.

## Log4js-Unterstützung zur Node.js-App hinzufügen
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) ist ein beliebtes Protokollierungsframework für Node.js und bietet viele native Vorteile: 
* Protokollierung in `stdout` oder `stderr`
* Verschiedene Optionen für das Anhängen
* Konfigurierbare Layouts und Muster für Protokollnachrichten
* Verwendung von Protokollebenen für verschiedene Protokollkategorien

1. Um Log4js zu verwenden, führen Sie den folgenden [npm](https://nodejs.org/)-Befehl im Stammverzeichnis der Anwendung aus; dadurch wird das Paket installiert und der Datei `package.json` hinzugefügt.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Um Log4js in der Anwendung zu verwenden, fügen Sie die folgenden Codezeilen hinzu:
  ```javascript
  var log4js = require('log4js');
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **stdout-Ausgabe**:
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

Weitere Informationen zum Anpassen der Protokollnachrichten mit Appendern, Protokollebenen und Konfigurationsdetails finden Sie in der offiziellen Dokumentation zu [log4js-node](https://log4js-node.github.io/log4js-node/).

## Protokolle mit App-Service überwachen
{: #monitoring}

Node.js-Apps, die mit dem {{site.data.keyword.cloud_notm}} [App-Service](https://console.bluemix.net/developer/appservice/dashboard) erstellt werden, werden standardmäßig mit Log4js bereitgestellt. Wenn Sie die App nativ oder in einer Cloud-Umgebung ausführen, wird in etwa die folgende Ausgabe erzeugt: `2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`. Sie können die Ausgabe wie folgt anzeigen:
* Verwenden Sie `stdout` bei einer lokalen Ausführung.
* In den Protokollen für [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)- und [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/)-Bereitstellungen, auf die der Zugriff über `ibmcloud app logs --recent <APP_NAME>` und `kubectl logs <deployment name>` erfolgt.

In der Datei `server/server.js` wird der folgende Code angezeigt:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Standardmäßig ist die Protokollebene auf `INFO` gesetzt und kann von der Umgebungsvariablen **LOG_LEVEL** der Anwendung überschrieben werden.
{: tip}

## Nächste Schritte
{: #next_steps notoc}

Weitere Informationen zum Anzeigen der Protokolle in jeder der Bereitstellungsumgebungen:
* [Kubernetes-Protokolle](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cloud Foundry-Protokolle](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}}-Protokolle & -Überwachung](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Verwendung eines Protokollaggregators:
* [{{site.data.keyword.cloud_notm}} Log Analysis](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [ELK-Stack von {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
