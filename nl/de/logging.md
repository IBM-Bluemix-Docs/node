---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-04"

keywords: nodejs logging, view logs nodejs, add logging nodejs, log4j nodejs, stdout nodejs, nodejs log, output nodejs, nodejs logger

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Protokollierung in Node.js
{: #logging_nodejs}

Protokollnachrichten sind Zeichenfolgen mit Kontextinformationen zum Status und der Aktivität des Mikroservice zu dem Zeitpunkt, an dem der Protokolleintrag erfolgt. Protokolle sind erforderlich, um zu diagnostizieren, wie und warum Services fehlschlagen, und spielen eine unterstützende Rolle für [appmetrics](/docs/node?topic=nodejs-metrics) bei der Anwendungsstatusprüfung.

Angesichts der transienten Natur von Prozessen in Cloud-Umgebungen müssen Protokolle erfasst und an eine andere Stelle gesendet werden, in der Regel an eine zentrale Position für die Analyse. Die konsistenteste Möglichkeit, sich in Cloud-Umgebungen anzumelden, besteht darin, Protokolleinträge an die Standardausgabe- und Fehlerdatenströme zu senden; die Infrastruktur ist dann für die restliche Verarbeitung zuständig.

Sie können [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") verwenden, ein gängiges Protokollierungsframework für Node.js, das zahlreiche native Vorteile bietet, z. B.: 
* Protokollierung in `stdout` oder `stderr`
* Verschiedene Optionen für das Anhängen
* Konfigurierbare Layouts und Muster für Protokollnachrichten
* Verwendung von `Protokollebenen` für verschiedene Protokollkategorien

## Log4js-Unterstützung zu einer vorhandenen Node.js-App hinzufügen
{: #add_log4j}

1. Installieren Sie als ersten Schritt `log4js`, indem Sie den folgenden [npm](https://nodejs.org/){: new_window}-Befehl ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") im Stammverzeichnis der Anwendung ausführen. Hierdurch wird das Paket installiert und zur Datei `package.json` hinzugefügt.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Um Log4js in der Anwendung zu verwenden, fügen Sie die folgenden Codezeilen zur App hinzu:
  ```js
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

3. Damit Protokollereignisse in den Standardfehlerdatenstrom geschrieben werden, können Sie einen Appender konfigurieren, wie im folgenden Beispiel veranschaulicht:
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  Weitere Informationen zum Anpassen der Protokollnachrichten mit Appendern, `Protokollebenen` und Konfigurationsdetails finden Sie in der offiziellen Dokumentation zu [log4js-node](https://log4js-node.github.io/log4js-node/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link").

## Überwachung mit App-Service-Apps
{: #monitoring}

Mit dem {{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") erstellte Node.js-Apps werden standardmäßig mit Log4js bereitgestellt. Sie können die Datei `server/server.js` öffnen, um den folgenden Log4js-Code anzuzeigen:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Standardmäßig ist für die `Protokollebene` die Einstellung `OFF` festgelegt, damit sie sicher in Bibliotheken genutzt werden kann; sie kann jedoch von der Umgebungsvariablen **LOG_LEVEL** der Anwendung überschrieben werden.
{: tip}

### Protokollausgabe anzeigen
{: #node-view-log-output}

Die folgende Beispielprotokollausgabe stammt von einer nativen Ausführung der App oder der Ausführung in einer Cloudumgebung:
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

Zum Anzeigen der Protokollausgabe können die folgenden Methoden verwendet werden:
* Für lokale Umgebungen `stdout` verwenden.
* Für [Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html)-Bereitstellungen können Sie auf Protokolle zugreifen, indem Sie Folgendes ausführen:
  ```
  ibmcloud app logs --recent <APP-NAME>
  ```
  {: codeblock}

* Für [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/)-Bereitstellungen können Sie auf Protokolle zugreifen, indem Sie Folgendes ausführen: 
  ```
  kubectl logs <Bereitstellungsname>
  ```
  {: codeblock}

## Nächste Schritte
{: #next_steps-logging notoc}

Weitere Informationen zum Anzeigen von Protokollen in der jeweiligen Bereitstellungsumgebung:
* [Kubernetes-Protokolle ](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link")
* [Cloud Foundry-Protokolle](/docs/services/CloudLogAnalysis/cfapps?topic=cloudloganalysis-logging_cf_apps#logging_cf_apps)
* [{{site.data.keyword.openwhisk}}-Protokolle & -Überwachung](/docs/openwhisk?topic=cloud-functions-openwhisk_logs#openwhisk_logs)

Verwendung eines Protokollaggregators:
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [ELK-Stack von {{site.data.keyword.cloud_notm}} Private ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link")
