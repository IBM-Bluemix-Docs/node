---

copyright:
  years: 2018
lastupdated: "2018-09-06"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Archiviazione di contenuto statico in Object Storage
{: #object}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://console.bluemix.net/docs/services/cloud-object-storage/libraries/node.html#using-node-js -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://console.bluemix.net/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage -->

{{site.data.keyword.cos_full_notm}} è un componente fondamentale dell'elaborazione cloud e fornisce delle potenti funzionalità agli sviluppatori Apple e alle loro applicazioni. A differenza dell'archiviazione di informazioni in una gerarchia di file, (come l'archiviazione blocchi o file), un'archiviazione oggetti consiste solo nei file e nei loro metadati. Questi file sono archiviati in raccolte note come bucket. Per definizione, questi oggetti sono immutabili, il che li rende perfetti per dati quali immagini, video e altri documenti statici. Per dati che cambiano spesso o di tipo relazionale, puoi utilizzare il servizio database [{{site.data.keyword.cloudant_short_notm}}](/docs/node/cloudant.html).

COS ({{site.data.keyword.cos_short}}) è un sistema di archiviazione che può essere utilizzato per archiviare dati non strutturati che è flessibile, economicamente vantaggioso e scalabile. I dati sono accessibili tramite gli SDK oppure utilizzando l'interfaccia utente IBM. Puoi utilizzare {{site.data.keyword.cos_short}} per accedere ai tuoi dati non strutturati tramite un portale self-service supportato da SDK e API Restful.

## Prima di iniziare
{: #before}

Assicurati di disporre dei seguenti prerequisiti pronti a essere utilizzati:
1. Devi avere un [account {{site.data.keyword.cloud_notm}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}.
2. Devi avere il [{{site.data.keyword.cos_short}} SDK for Node.js ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/ibm/ibm-cos-sdk-js){: new_window}.
3. Devi avere Node 4.x+.
4. Individua i valori chiave delle credenziali da utilizzare in seguito per l'inizializzazione SDK:

    * _**endpoint**_ - l'endpoint pubblico per il tuo Cloud Object Storage. L'endpoint è disponibile dal [dashboard {{site.data.keyword.cloud_notm}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps){: new_window}.
    * _**api-key**_ - la chiave API generata quando vengono create le credenziali del servizio. Per gli esempi di creazione ed eliminazione, è necessario l'accesso in scrittura.
    * _**resource-instance-id**_ - l'ID risorsa per il tuo Cloud Object Storage. L'ID risorsa è disponibile tramite il [{{site.data.keyword.cloud_notm}} CLI](../cli/index.html) or [dashboard {{site.data.keyword.cloud_notm}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps){: new_window}.

## Passo 1. Creazione di un'istanza di {{site.data.keyword.cos_short}}
{: #create-instance}

1. Nel [catalogo {{site.data.keyword.cloud_notm}}](https://console.bluemix.net/catalog/), seleziona la categoria **Storage** e fai clic su {{site.data.keyword.cos_short}}. Viene aperta la pagina di configurazione del servizio.
2. Dai un nome alla tua istanza del servizio oppure utilizza il nome preimpostato.
3. Seleziona il tuo piano dei prezzi e fai clic su **Crea**.Viene aperta la pagina della tua istanza di Object Storage.
4. Nel menu di navigazione, seleziona **Credenziali del servizio**.
5. Nella pagina Credenziali del servizio, fai clic su **Nuova credenziale**.
6. Nella pagina Aggiungi nuova credenziale, assicurati che il ruolo sia impostato su **Scrittore** e fai quindi clic su **Aggiungi** La nuova credenziale viene creata e visualizzata nella pagina Credenziali del servizio.

## Passo 2. Installazione dell'SDK
{: #install}

Installa il {{site.data.keyword.cos_short}} SDK for Node.js utilizzando il gestore pacchetti [npm](https://nodejs.org/) dalla riga di comando:
```
npm install ibm-cos-sdk
```
{: pre}

## Passo 3. Inizializzazione dell'SDK
{: #initialize}

Dopo che hai inizializzato l'SDK nella tua applicazione, puoi utilizzare {{site.data.keyword.cos_short}} per archiviare i dati. Inizializza la tua connessione fornendo le tue credenziali e fornendo una funzione di callback da eseguire quando tutto è pronto.

1. Carica la libreria client aggiungendo le seguenti definizioni `require` al tuo file `server.js`.
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. Inizializza la libreria client fornendo le tue credenziali.
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

  Se hai bisogno di aiuto per trovare i valori chiave delle credenziali per la tua applicazione, consulta il *passo 4* della sezione [Prima di iniziare](object_storage.html#before) per i dettagli relativi a dove trovarli.
  {: tip}

3. Aggiungi il seguente codice al tuo file `server.js`.
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

### Gestione dei dati con le operazioni di base
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### Creazione di un bucket
```js
function doCreateBucket() {
    console.log('Creating bucket');
    return cos.createBucket({
        Bucket: 'my-bucket',
        CreateBucketConfiguration: {
          LocationConstraint: 'us-standard'
        },
    }).promise();
}
```
{: codeblock}

#### Creazione/caricamento o sovrascrittura di un oggetto
```js
function doCreateObject() {
    console.log('Creating object');
    return cos.putObject({
        Bucket: 'my-bucket',
        Key: 'foo',
        Body: 'bar'
    }).promise();
}
```
{: codeblock}

#### Download di un oggetto
<!-- Verify this snippet with Nick when he returns from vacation -->
```js
function doGetObject() {
 console.log('Getting object');
 return cos.getObject({
   Bucket: 'my-bucket',
   Key: 'foo'
 }).createReadStream().pipe(fs.createWriteStream('./MyObject'));
}
```
{: codeblock}

#### Eliminazione di un oggetto
```js
function doDeleteObject() {
    console.log('Deleting object');
    return cos.deleteObject({
        Bucket: 'my-bucket',
        Key: 'foo'
    }).promise();
}
```
{: codeblock}

Consulta la [documentazione completa](/docs/services/cloud-object-storage/libraries/node.html#using-node-js) per i caricamenti a più parti, le funzioni di sicurezza e altre operazioni.

## Passo 4. Esecuzione di test della tua applicazione
{: #test}

Tutto è configurato correttamente? Verificalo eseguendo dei test.

1. Esegui la tua applicazione, assicurandoti di avviare l'inizializzazione e le rispettive operazioni, come ad esempio la creazione di un bucket e l'aggiunta di dati al bucket.
2. Ritorna all'istanza del servizio {{site.data.keyword.cos_short}} che hai creato in precedenza nel tuo browser web e apri il dashboard del servizio.
3. Seleziona il bucket utilizzato e visualizza i tuoi oggetti appena creati nel dashboard.

Hai riscontrato dei problemi? Consulta la [guida di riferimento API {{site.data.keyword.cos_short}}](/docs/services/cloud-object-storage/api-reference/about-api.html){:new_window}.

## Passi successivi
{: #next notoc}

Ottimo lavoro! Hai aggiunto un livello di persistenza protetta alla tua applicazione. Non fermarti ora e continua provando una delle seguenti opzioni:

* Visualizza il codice sorgente di [{{site.data.keyword.cos_short}} SDK for Node.js ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/ibm/ibm-cos-sdk-js){:new_window}.
* Consulta il [codice di esempio per le operazioni bucket e oggetto ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://github.com/ibm/ibm-cos-sdk-js#example-code){:new_window}.
* I kit starter sono uno dei modi più rapidi per utilizzare le funzionalità di {{site.data.keyword.cloud_notm}}. Visualizza i kit starter disponibili nel [dashboard degli sviluppatori di applicazioni mobili ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://console.bluemix.net/developer/mobile/dashboard){:new_window}. Scarica il codice. Esegui l'applicazione!
* Per saperne di più su tutte le nuove funzioni offerte da {{site.data.keyword.cos_short}} e per avvalertene, [consulta la documentazione](/docs/services/cloud-object-storage/about-cos.html)!
