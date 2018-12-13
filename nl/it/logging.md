---

copyright:
  years: 2018
lastupdated: "2018-10-04"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Esecuzione dell'accesso a Node.js
{: #logging_nodejs}

I messaggi di log sono stringhe con informazioni contestuali sullo stato e sull'attività del microservizio nel momento in cui viene creata la voce di log. I log sono necessari per diagnosticare la modalità e la causa dei malfunzionamenti dei servizi e hanno un ruolo di supporto per [appmetrics](appmetrics.html) nel monitoraggio dell'integrità delle applicazioni.

Data la natura transitoria dei processi negli ambienti cloud, i log devono essere raccolti e inviati altrove, di norma a un'ubicazione centralizzata per l'analisi. Il modo più congruente per registrare nei log negli ambienti cloud consiste nell'inviare le voci di log a flussi di output e di errore standard e lasciare che l'infrastruttura gestisca il resto.

## Aggiunta del supporto Log4js all'applicazione Node.js
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) è un diffuso framework di registrazione per Node.js e fornisce molti vantaggi nativi, che includono: 
* Registrazione in `stdout` o `stderr`
* Varie opzioni di accodamento
* Layout e modello dei messaggi di log configurabili
* Utilizzo dei livelli di log per diverse categorie di log

1. Per utilizzare Log4js, esegui questo comando [npm](https://nodejs.org/) nella directory root della tua applicazione, che installa il pacchetto e lo aggiunge al tuo file `package.json`.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Per utilizzarlo nella tua applicazione, aggiungi queste righe di codice:
  ```javascript
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

Per ulteriori informazioni sulla personalizzazione dei messaggi di log con gli appender, i livelli di log e i dettagli della configurazione, consulta la [documentazione di log4js-node](https://log4js-node.github.io/log4js-node/) ufficiale.

## Monitoraggio dei log con l'App Service
{: #monitoring}

Le applicazioni Node.js create utilizzando l'[App Service](https://console.bluemix.net/developer/appservice/dashboard) {{site.data.keyword.cloud_notm}} sono fornite con Log4js per impostazione predefinita. L'esecuzione dell'applicazione in modo nativo oppure in un ambiente Cloud produce un output simile al seguente: `2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`. Puoi visualizzare l'output nel modo seguente:
* Utilizza `stdout` in caso di esecuzione in locale.
* Nei log per le distribuzioni [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) e [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/), a cui si accede da `ibmcloud app logs --recent <APP_NAME>` e `kubectl logs <deployment name>`.

Nel file `server/server.js`, puoi vedere il seguente codice:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Per impostazione predefinita, il livello di log è impostato su `OFF` in modo che può essere utilizzato in modo sicuro nelle librerie, ma può essere sovrascritto dalla variabile di ambiente **LOG_LEVEL** dell'applicazione.
{: tip}

## Passi successivi
{: #next_steps notoc}

Ulteriori informazioni sulla visualizzazione dei log in ciascuno dei nostri ambienti di distribuzione:
* [Log Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Log Cloud Foundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} Log e monitoraggio](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Utilizzando un aggregatore di log:
* [{{site.data.keyword.cloud_notm}} Log Analysis](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [Stack ELK privato {{site.data.keyword.cloud_notm}}](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
