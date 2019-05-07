---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-30"

keywords: nodejs metrics, application metrics nodejs, node appmetrics, nodejs autoscaling, nodejs dash, appmetrics-dashs nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilizzo delle metriche dell'applicazione con le applicazioni Node.js
{: #metrics}

Impara come installare, accedere e comprendere le metriche dell'applicazione Node.js. Puoi monitorare le applicazioni Node.js con il dashboard [Node Application Metrics](https://developer.ibm.com/open/projects/node-application-metrics/){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno") per visualizzare le prestazioni della tua applicazione Node.js visualizzando le metriche in un front end basato sul web.
{: shortdesc}

## Identificazione dei problemi visivamente
{: #identify-problems}

Le metriche dell'applicazione sono importanti per il monitoraggio delle prestazioni della tua applicazione. Disporre di una vista in diretta di metriche come CPU, memoria, latenza e metriche HTTP è essenziale per garantire un'efficace esecuzione della tua applicazione nel tempo. Puoi utilizzare un servizio cloud come il [ridimensionamento automatico](/docs/services/Auto-Scaling?topic=Auto-Scaling-get-started) di Cloud Foundry che si basa sulle metriche per modificare dinamicamente le dimensioni delle istanze in modo che corrispondano all'attuale carico di lavoro. Utilizzando le metriche dell'applicazione, sei informato precisamente quando aumentare o ridurre le istanze oppure quando eliminare quelle che non sono più necessarie per mantenere bassi i costi.

Le metriche dell'applicazione vengono acquisite come dati delle serie temporali. L'aggregazione e la visualizzazione delle metriche acquisite può essere di ausilio nell'identificazione di problemi delle prestazioni comuni quali:

* Tempi di risposta HTTP lenti su qualche instradamento o su tutti gli instradamenti
* Una scarsa velocità effettiva nell'applicazione
* Dei picchi di richiesta che causano un rallentamento
* Un utilizzo della CPU più elevato del previsto
* Un utilizzo della memoria elevato o crescente (potenziale perdita di memoria)

Il dashboard Application Metrics integrato ([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")) include anche un grafico per 'Other Requests', che mostra la durata delle richieste database per i database supportati (MongoDB, MySQL, Postgres, LevelDB e Redis), Socket.IO ed eventi Riak.

Dal dashboard è possibile generare un report dei nodi (Node Report) o un'istantanea dell'heap (Heap Snapshot) per abilitare un'analisi più approfondita.

## Aggiunta di metriche alle applicazioni Node.js esistenti
{: #add-appmetrics-existing}

Aggiungi le funzioni di monitoraggio alle applicazioni Express esistenti con il constructor [ `appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno") per trasmettere svariate opzioni di configurazione. Ad esempio, una delle opzioni utilizza un server esistente invece di fare in modo che `appmetrics-dash` avvii un server supplementare.

### Installazione del dashboard
{: #install-appmetrics}

1. Ad esempio, utilizza la seguente semplice applicazione Express “Hello World”:
  ```js
  var express = require('express')
  var app = express()
  app.get('/', function (req, res) {
      res.send('Hello World!')
  })
  app.listen(3000, function () {
      console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

2. Installa il dashboard `appmetrics` con il seguente comando [npm](https://nodejs.org/){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno").
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. Aggiungi il supporto `appmetrics-dash` alla tua applicazione esistente aggiungendo il seguente codice:
  ```js
  var dash = require('appmetrics-dash').attach()
  ```
  {: codeblock}

  Questo comando indica a `appmetrics-dash` di utilizzare il server che è già creato e di aggiungere un endpoint `appmetrics-dash`.

  Il codice aggiornato si presenta ora come il seguente esempio:
  ```js
  var express = require('express')
  var app = express()
  var dash = require('appmetrics-dash').attach()
  app.get('/', function (req, res) {
    res.send('Hello World!')
  })
  app.listen(3000, function () {
    console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

## Utilizza le metriche dai kit starter
{: #appmetrics-starterkits}

Le applicazioni Node.js create dai kit starter sono automaticamente fornite con `appmetrics-dash` e il relativo dashboard per impostazione predefinita ma è necessario che ne venga eseguita l'abilitazione per l'utilizzo.

Il codice appmetrics è disponile nel file di origine dell'applicazione generato, denominato `/app_name/server/server.js`:
```js
// Uncomment following to enable zipkin tracing, tailor to fit your network configuration:
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## Accesso al dashboard
{: #access-dashboard}

Dopo che hai avviato la tua applicazione, vai a `http://<hostname>:<port>/appmetrics-dash` in un browser.

Utilizza il `localhost:3001/appmetrics-dash` predefinito per le applicazioni che sono in esecuzione in locale.
{: tip}

L'IU del dashboard di monitoraggio Application Metrics for Node.js fornisce una gamma di metriche, comprese le richieste HTTP e la latenza di loop degli eventi, come illustrato nel seguente video [Monitoring Metrics for Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno").

## Descrizione dei dati
{: #understanding-data}

![Dashboard Appmetrics](images/appmetricsdash-1.png)

La maggior parte dei dati viene tracciata come grafici a linee. HTTP Incoming Requests, HTTP Outgoing Requests e Other Requests mostrano la durata degli eventi rispetto al tempo. HTTP Throughput mostra le richieste al secondo. Average Response Times mostra le cinque richieste HTTP in entrata più usate che hanno impiegato, in media, più tempo. I grafici CPU e Memory mostrano l'utilizzo di sistema e processi nel tempo. Heap mostra la dimensione heap massima e la dimensione heap utilizzata nel tempo. Event Loop Latency mostra dei campioni di latenza presi a intervalli dal loop di eventi Node.js, con un punto per la latenza più breve, uno per la media e uno per quella più lunga per ogni campione preso.

Se un grafico ha dei punti, se ci passi sopra il puntatore del mouse ti verranno fornite ulteriori informazioni. Ad esempio, `HTTP Incoming Requests` mostra il tempo di riposta e l'url richiesto.

In tutti i grafici viene mostrato un massimo di 15 minuti di dati.

Se l'applicazione di cui è in corso il monitoraggio sta producendo un'ampia quantità di dati, il dashboard inizia automaticamente a visualizzare i dati. Quando visualizzi nuovamente il grafico HTTP Incoming Requests, puoi vedere che ciascun punto mostra tutte le richieste per un periodo di 2 secondi. La seguente descrizione a comparsa mostra il numero totale di richieste insieme al tempo medio impiegato e al tempo più lungo. Il tempo più lungo è il valore che viene tracciato.

![Mostra descrizione a comparsa](images/tooltip-1.png)




