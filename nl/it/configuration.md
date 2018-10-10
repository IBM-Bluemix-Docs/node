---

copyright:
  years: 2018
lastupdated: "2018-09-20"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configurazione dell'ambiente Node.js

Implementando i principi nativi del cloud, un'applicazione Node.js può passare da un ambiente a un altro, dalla fase di test alla produzione, senza modificare il codice o attuare dei percorsi di codice non testati in precedenza.

Il problema sorge quando esistono delle differenze significative nel modo in cui viene presentata la configurazione, a seconda dell'ambiente di sviluppo. Poniamo ad esempio CloudFoundry, che utilizza oggetti JSON in stringhe rispetto a Kubernetes, che utilizza valori non strutturati oppure oggetti JSON in stringhe. Lo sviluppo locale, fatta eccezione per Kubernetes, ha anche considerazioni diverse. Le credenziali possono essere presentate diversamente in ambiente pubblico e privato, il che rende ulteriormente difficile per le applicazioni il restare immutate da un ambiente all'altro.

Sia che tu debba aggiungere il supporto {{site.data.keyword.cloud}} alle applicazioni esistenti o creare applicazioni con i kit starter, l'obiettivo è quello di fornire la portabilità per le applicazioni Node.js su qualsiasi piattaforma di sviluppo.

## Aggiunta della configurazione {{site.data.keyword.cloud_notm}} alle applicazioni Node.js esistenti
{: #addcloud-env}

Il modulo [`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env) aggrega le variabili di ambiente da diversi provider cloud, come CloudFoundry e Kubernetes, in modo che l'applicazione sia indipendente dall'ambiente.

### Installazione del modulo `ibm-cloud-env`
1. Installa il modulo `ibm-cloud-env` con il seguente comando:
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. Inizializza il modulo nel tuo codice facendo riferimento a `mappings.json` nel seguente modo:
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  Se il percorso del file delle associazioni non è specificato in `IBMCloudEnv.init()`, il modulo prova a caricare le associazioni da un percorso predefinito di `/server/config/mappings.json`.
  {: tip}

  File `mappings.json` di esempio:
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

### Utilizzo dei valori in un'applicazione Node.js
Richiama i valori nella tua applicazione utilizzando i seguenti comandi.

1. Richiama la variabile `service1credentials`:
  ```js
  // this will be a dictionary
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. Richiama la variabile `service2username`:
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // this will be a string
  ```
  {: codeblock}

Ora la tua applicazione può essere implementata in qualsiasi ambiente di runtime astraendo le differenze introdotte dai diversi provider di elaborazione Cloud.

### Filtro dei valori per tag ed etichette
Puoi filtrare le credenziali generate dal modulo in base alle tag di servizio e alle etichette di servizio, come mostrato nel seguente esempio:
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // returns a Json with credentials for specified service tag and label
```
{: codeblock}

## Utilizzo del gestore configurazione Node.js dalle applicazioni kit starter

Le applicazioni Node.js create con i [kit starter](https://console.bluemix.net/developer/appservice/starter-kits/)  vengono automaticamente fornite con le credenziali e le configurazioni necessarie per l'esecuzione in molti ambienti di distribuzione Cloud (CF, K8s, VSI e Functions).

### Informazioni sulle credenziali del servizio

Le tue informazioni sulla configurazione delle applicazioni per i servizi vengono memorizzate nel file `localdev-config.json` nella directory `/server/config`. Il file si trova nella directory `.gitignore` per evitare che informazioni sensibili vengano memorizzate in Git. Le informazioni sulla connessione per qualsiasi servizio configurato che viene eseguito localmente, come nome utente, password e nome host, sono memorizzate in questo file.

L'applicazione utilizza il gestore configurazione per leggere le informazioni sulla connessione e sulla configurazione dall'ambiente e da questo file. Utilizza un `mappings.json`, personalizzato che si trova nella directory `server/config` per comunicare dove è possibile trovare le credenziali per ciascun servizio.

Le applicazioni in esecuzione in locale possono connettersi ai servizi {{site.data.keyword.cloud_notm}} utilizzando le credenziali senza binding lette dal file `mappings.json`. Se hai bisogno di creare delle credenziali senza binding, puoi farlo dalla console web {{site.data.keyword.cloud_notm}} oppure utilizzando il comando `cf create-service-key` della [CLI CloudFoundry](https://docs.cloudfoundry.org/cf-cli/).

Quando esegui il push della tua applicazione a {{site.data.keyword.cloud_notm}}, questi valori non vengono più utilizzati. L'applicazione stabilisce invece automaticamente una connessione ai servizi di cui è stato eseguito il bind utilizzando le variabili di ambiente.

* **Cloud Foundry**: le credenziali del servizio vengono prese dalla variabile di ambiente `VCAP_SERVICES`.

* **Kubernetes**: le credenziali del servizio vengono prese dalle singole variabili di ambiente per ogni servizio.

* **Servizio contenitore {{site.data.keyword.cloud_notm}}**: le credenziali del servizio sono prese dalle VSI o da {{site.data.keyword.openwhisk}} (Openwhisk).


## Passi successivi
{: #next_steps notoc}

`ibm-cloud-config` supporta la ricerca di valori utilizzando tre tipo di modello di ricerca: `cloudfoundry`, `env` e `file`. Se vuoi verificare altri modelli di ricerca supportati e degli esempi di modello di ricerca, consulta la sezione [Usage](https://github.com/ibm-developer/ibm-cloud-env#usage).
