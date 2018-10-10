---

copyright:
  years: 2017-2018
lastupdated: "2018-10-01"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Archiviazione di documenti in {{site.data.keyword.cloud_notm}}
{: #cloudant}

{{site.data.keyword.cloudantfull}} è un DBaaS (Database as a Service) orientato ai documenti. Archivia i dati come documenti in formato JSON. {{site.data.keyword.cloudant_short_notm}} è stato messo a punto tenendo presenti scalabilità, alta disponibilità e durabilità ed è facile da configurare per l'utilizzo nelle applicazioni Node.js. Viene fornito con un'ampia varietà di opzioni di indicizzazione che includono MapReduce, {{site.data.keyword.cloudant_short_notm}} Query, full-text e geospaziale. La replica delle funzionalità rende più semplice mantenere i dati
sincronizzati tra i cluster di database, i PC desktop e i dispositivi mobili.
{:shortdesc}

Per ulteriori informazioni, vedi [Principi di base di {{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant/basics/index.html#cloudant-nosql-db-basics){:new_window}.

## Prima di iniziare
{: #before}

Assicurati di disporre dei seguenti prerequisiti pronti a essere utilizzati:
 * La libreria client [Nodejs-cloudant ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/cloudant/nodejs-cloudant){:new_window} 2.3.0+.
 * Devi avere un [account {{site.data.keyword.cloud}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}.
 * Per accedere a {{site.data.keyword.cloudant_short_notm}}, devi creare un servizio nel [dashboard {{site.data.keyword.cloud_notm}}![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps){: new_window} e avviare quindi il dashboard {{site.data.keyword.cloudant_short_notm}} dall'istanza del servizio.
 * I frammenti di codice in queste istruzioni utilizzano l'autenticazione IAM.
 
### Abilitazione di IAM con {{site.data.keyword.cloudant_short_notm}}
{: #enable_IAM}

Solo le nuove istanze del servizio {{site.data.keyword.cloudant_short_notm}} possono essere utilizzate con {{site.data.keyword.cloud_notm}} IAM.

Tutte le nuove istanze del servizio {{site.data.keyword.cloudant_short_notm}} sono abilitate a utilizzare {{site.data.keyword.cloud_notm}} IAM (Identity and Access Management) quando ne viene eseguito il provisioning. Quando esegui il provisioning di una nuova istanza dal catalogo {{site.data.keyword.cloud_notm}}, scegli il metodo di autenticazione **Utilizza solo IAM**. Questa modalità significa che solo le credenziali IAM sono fornite dal bind del servizio e dalla generazione delle credenziali. Puoi trovare ulteriori informazioni in [{{site.data.keyword.cloud_notm}} IAM (Identity and Access Management)](/docs/services/Cloudant/guides/iam.html).

## Passo 1. Creazione di un'istanza di {{site.data.keyword.cloudant_short_notm}}
{: #create-instance}

Quando crei un'istanza di {{site.data.keyword.cloudant_short_notm}}, crei anche il database.

1. Esegui l'accesso al tuo account {{site.data.keyword.cloud_notm}}.
2. Dal [dashboard {{site.data.keyword.cloud_notm}}![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps){: new_window}, fai clic su **Crea risorsa**. Viene aperto il catalogo {{site.data.keyword.cloud_notm}}.
3. Nel [catalogo {{site.data.keyword.cloud_notm}}](https://console.bluemix.net/catalog/), seleziona la categoria **Database** e fai quindi clic su {{site.data.keyword.cloudant_short_notm}}. Viene aperta la pagina di configurazione del servizio.
4. Completa le informazioni nei seguenti campi:
  * **Nome servizio** - Immetti un nome per la tua istanza del servizio oppure utilizza il nome preimpostato.
  * **Scegli una regione/ubicazione in cui distribuire** - seleziona una regione in cui distribuire il tuo servizio.
  * **Seleziona un gruppo di risorse** - seleziona un gruppo di risorse oppure accetta il valore predefinito.
  * **Metodi di autenticazione disponibili** - seleziona **Utilizza solo IAM** per il metodo di autenticazione.
5. Seleziona il tuo piano dei prezzi e fai quindi clic su **Crea**. Viene aperta la pagina per la tua istanza del servizio.
6. Per creare una credenziale del servizio, completa la seguente procedura:
  1. Dal menu di navigazione, seleziona **Credenziali del servizio**.
  2. Fai clic su **Nuova credenziale**. Viene aperta la pagina Aggiungi nuova credenziale.
  3. Nella pagina Aggiungi nuova credenziale, completa i campi e fai quindi clic su **Aggiungi**. La nuova credenziale del servizio viene aggiunta all'istanza del servizio.
  4. Se vuoi visualizzare i dettagli della credenziale del servizio, fai clic su **Visualizza credenziali** nella colonna **Azioni** della nuova credenziale.
7. Dal menu di navigazione, seleziona **Gestisci** e fai quindi clic su **Avvia dashboard Cloudant**.
8. Dal menu di navigazione, fai clic sull'icona **Database**.
9. Fai clic su **Crea database**, fornisci un nome di database e fai quindi clic su **Crea** Viene aperta la tua pagina del database.

Se vuoi visualizzare le informazioni correlate relative al provisioning di un'istanza del servizio {{site.data.keyword.cloud_notm}}, vedi l'[esercitazione Creating an IBM Cloudant instance on IBM Cloud ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/docs/services/Cloudant/tutorials/create_service.html#creating-a-cloudant-nosql-db-instance-on-ibm-cloud){: new_window}.

## Passo 2. Installazione dell'SDK
{: #install}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

Inizia con il tuo progetto Node.js e definisci questo lavoro come tua dipendenza. In altre parole, metti {{site.data.keyword.cloudant_short_notm}} nelle tue dipendenze package.json. Utilizza il gestore pacchetti [npm](https://nodejs.org/) dalla riga di comando per installare l'SDK:
```
npm install --save @cloudant/cloudant
```
{: codeblock}

Nota: il tuo file `package.json` ora mostra il pacchetto Cloudant.

## Passo 3. Inizializzazione dell'SDK
{: #initialize}

Dopo che hai inizializzato l'SDK nella tua applicazione, puoi utilizzare {{site.data.keyword.cloudant_short_notm}} per archiviare i dati. Per inizializzare la tua connessione, immetti le tue credenziali e fornisci una funzione di callback da eseguire quando tutto è pronto.

1. Carica la libreria client aggiungendo la seguente definizione `require` al tuo file `server.js`.
  ```js
  var Cloudant = require('@cloudant/cloudant');
  ```
  {: codeblock}

2. Inizializza la libreria client fornendo le tue credenziali. Utilizza il plug-in `iamauth` per creare un client database con una chiave API IAM. 
  ```js
  var cloudant = new Cloudant({ url: 'https://examples.cloudant.com', plugins: { iamauth: { iamApiKey: 'xxxxxxxxxx' } } });
  ```
  {: codeblock}

3. Elenca i database aggiungendo il seguente codice al tuo file `server.js`.
  ```javascript
  cloudant.db.list(function(err, body) {
  body.forEach(function(db) {
    console.log(db);
    });
  });
  ```
  {: codeblock}

`Cloudant` con la C maiuscola è il pacchetto che carichi utilizzando `require()`. `cloudant` in minuscolo è la connessione autenticata al tuo servizio {{site.data.keyword.cloudant_short_notm}}.
{: tip}

### Gestione dei dati con le operazioni di base
{: #basic_operations}
<!--Borrowed from https://github.com/cloudant/nodejs-cloudant/blob/master/example/crud.js-->

Queste operazioni di base illustrano le azioni per creare, leggere, aggiornare ed eliminare i tuoi documenti utilizzando il client inizializzato.

#### Creazione di un documento
```js
var createDocument = function(callback) {
  console.log("Creating document 'mydoc'");
  // specify the id of the document so you can update and delete it later
  db.insert({ _id: 'mydoc', a: 1, b: 'two' }, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

#### Lettura di un documento
```js
var readDocument = function(callback) {
  console.log("Reading document 'mydoc'");
  db.get('mydoc', function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep a copy of the doc so you know its revision token
    doc = data;
    callback(err, data);
  });
};
```
{: codeblock}

#### Aggiornamento di un documento
```js
var updateDocument = function(callback) {
  console.log("Updating document 'mydoc'");
  // make a change to the document, using the copy we kept from reading it back
  doc.c = true;
  db.insert(doc, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep the revision of the update so we can delete it
    doc._rev = data.rev;
    callback(err, data);
  });
};
```
{: codeblock}

#### Eliminazione di un documento
```js
var deleteDocument = function(callback) {
  console.log("Deleting document 'mydoc'");
  // supply the id and revision to be deleted
  db.destroy(doc._id, doc._rev, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

## Passo 4. Esecuzione di test della tua applicazione
{: #test}

Tutto è configurato correttamente? Verificalo eseguendo dei test.

1. Esegui la tua applicazione, assicurandoti di avviare l'inizializzazione e le rispettive operazioni, come ad esempio la creazione di un documento.
2. Dal [dashboard {{site.data.keyword.cloud_notm}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps){: new_window}, fai clic sull'istanza del servizio {{site.data.keyword.cloudant_short_notm}} che hai creato in precedenza. Quando l'istanza del servizio viene aperta, fai clic su **Avvia dashboard Cloudant**.
3. Nel dashboard {{site.data.keyword.cloudant_short_notm}}, seleziona il database dove hai creato i nuovi documenti.

Hai riscontrato dei problemi? Consulta la [guida di riferimento API {{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant/api/index.html#api-reference-overview){:new_window}.

## Passi successivi
{: #next notoc}

Ottimo lavoro! Hai aggiunto un livello di persistenza protetta alla tua applicazione. Non fermarti ora e continua provando una delle seguenti opzioni:

* Visualizza il codice sorgente di [{{site.data.keyword.cloudant_short_notm}} SDK for Node.js ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/cloudant/nodejs-cloudant){: new_window}.
* Consulta il [codice di esempio per le operazioni database e documento ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/cloudant/nodejs-cloudant/tree/master/example){: new_window}.
* I kit starter sono uno dei modi più rapidi per utilizzare le funzionalità di {{site.data.keyword.cloud}}. Visualizza i kit starter disponibili nel [dashboard degli sviluppatori di applicazioni mobili ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/developer/mobile/dashboard){: new_window}. Scarica il codice. Esegui l'applicazione!
* Per saperne di più su tutte le nuove funzioni offerte da {{site.data.keyword.cloudant_short_notm}} e per avvalertene, [consulta la documentazione](/docs/services/Cloudant/getting-started.html)!
