---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Esecuzione dell'accesso a Node.js
{: #logging_nodejs}

I messaggi di log sono stringhe con informazioni contestuali sullo stato e sull'attività del microservizio nel momento in cui viene creata la voce di log. I log sono necessari per diagnosticare la modalità e la causa dei malfunzionamenti dei servizi e hanno un ruolo di supporto per [appmetrics](/docs/node/appmetrics.html#metrics) nel monitoraggio dell'integrità delle applicazioni.

Data la natura transitoria dei processi negli ambienti cloud, i log devono essere raccolti e inviati altrove, di norma a un'ubicazione centralizzata per l'analisi. Il modo più congruente per registrare nei log negli ambienti cloud consiste nell'inviare le voci di log a flussi di output e di errore standard e lasciare che l'infrastruttura gestisca il resto.

Puoi utilizzare [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno"), che è un diffuso framework di registrazione per Node.js e fornisce molti vantaggi nativi, che includono: 
* Registrazione in `stdout` o `stderr`
* Varie opzioni di accodamento
* Layout e modello dei messaggi di log configurabili
* Utilizzo dei livelli di log (`Log Levels`) per diverse categorie di log

## Aggiunta del supporto Log4js all'applicazione Node.js
{: #add_log4j}

1. Innanzitutto, installa `log4js` eseguendo il seguente comando [npm](https://nodejs.org/){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno") nella directory root della tua applicazione, che installa il pacchetto e lo aggiunge al tuo file `package.json`.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Per utilizzarlo nella tua applicazione, aggiungi le seguenti righe di codice alla tua applicazione.
  ```js
  var log4js = require('log4js');
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **Output stdout**:
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

3. Per scrivere eventi di log nel flusso di errori standard, puoi configurare un appender come il seguente esempio:
  ```js
  var log4js = require('log4js');

  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  Per ulteriori informazioni sulla personalizzazione dei messaggi di log con gli appender, i livelli di log (`Log Levels`) e i dettagli della configurazione, vedi la [documentazione di log4js-node](https://log4js-node.github.io/log4js-node/){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno") ufficiale.

## Monitoraggio con le applicazioni App Service
{: #monitoring}

Le applicazioni Node.js create utilizzando l'[App Service](https://cloud.ibm.com/developer/appservice/dashboard) {{site.data.keyword.cloud_notm}} sono fornite con Log4js per impostazione predefinita. Puoi aprire il file `server/server.js` per visualizzare il seguente codice Log4js:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Per impostazione predefinita, il livello di codice (`Log Level`) è impostato su `OFF` in modo da poter essere utilizzato in modo sicuro nelle librerie ma da poter essere sovrascritto dalla variabile di ambiente **LOG_LEVEL** dell'applicazione.
{: tip}

### Visualizzazione dell'output di log
{: #node-view-log-output}

Consulta il seguente output di log di esempio dall'esecuzione dell'applicazione in modo nativo o in un ambiente cloud:
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

Puoi visualizzare l'output di log utilizzando i seguenti metodi:
* Per gli ambienti locali, usa `stdout`.
* Per le distribuzioni [Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html), puoi accedere ai log eseguendo:
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* Per le distribuzioni [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno"), puoi accedere ai log eseguendo:
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## Passi successivi
{: #next_steps-logging notoc}

Ulteriori informazioni sulla visualizzazione dei log in ciascun ambiente di distribuzione:
* [Log Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")
* [Log Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html#logging_cf_apps)
* [Log e monitoraggio {{site.data.keyword.openwhisk}}](/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Utilizzando un aggregatore di log:
* [Analisi log {{site.data.keyword.cloud_notm}}](/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [Stack ELK {{site.data.keyword.cloud_notm}} privato](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")
