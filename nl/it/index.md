---

copyright:
  years: 2018
lastupdated: "2018-10-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Esercitazione introduttiva

La seguente esercitazione ti guida attraverso i passi per creare, eseguire in locale e distribuire un'applicazione Node.js utilizzando gli strumenti {{site.data.keyword.cloud_notm}} forniti. Puoi utilizzare [{{site.data.keyword.dev_cli_long}}](https://console.bluemix.net/docs/cloudnative/dev_cli.html#add-cli) sulla riga di comando oppure l'[{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://console.bluemix.net/developer/appservice/dashboard) basato sul web, come mostrato nei seguenti passi dell'esercitazione. Utilizzando l'uno o l'altro di questi metodi, puoi generare un'applicazione Node.js pronta per la produzione in pochi minuti.

Assicurati che stai utilizzando la release di LTS Node.js più recente.

## Creazione di un'applicazione Node.js
{: #create_project}

1. Dalla pagina [Kit starter](https://console.bluemix.net/developer/appservice/starter-kits) nella {{site.data.keyword.dev_console}}, seleziona un kit starter scritto in `Node.js`. Puoi anche creare un'applicazione starter vuota facendo clic su **Crea applicazione** e selezionando `Node.js` come linguaggio.

    Per creare un progetto, devi essere collegato a un account {{site.data.keyword.cloud_notm}}. Se non hai un account, puoi [eseguire la registrazione per un account gratuito](https://console.bluemix.net/registration).
    {: tip}

2. Fai clic su **Crea applicazione**.
3. Usa **Nome** per denominare la tua applicazione. Viene fornito un nome applicazione generico, se vuoi utilizzarlo.
4. Immetti un **nome host univoco**. Il nome host viene utilizzato per accedere alla tua applicazione, ad esempio: `expressjs-project.mybluemix.net`.
5. Fare clic su **Crea**. Dopo che il tuo progetto è stato creato, puoi distribuirlo utilizzando una toolchain oppure puoi continuare a creare e distribuire il tuo progetto dalla riga di comando.
6. Se scegli di creare una toolchain, fai clic su **Distribuisci al cloud** e seleziona uno dei seguenti metodi di distribuzione.
    * **Applicazione Cloud Foundry** - non hai bisogno di gestire l'infrastruttura sottostante.
    * **Cluster Kubernetes** - devi eseguire il provisioning di una serie di nodi di lavoro. Ad esempio, puoi utilizzare le VM per distribuire e gestire contenitori dell'applicazione ad elevata disponibilità. Puoi creare un cluster o eseguire la distribuzione a un cluster esistente.

7. Finalizza le tue opzioni e fai quindi clic su **Crea** per creare la toolchain.

8. Se scegli di continuare con la CLI invece della toolchain, scarica il progetto sulla tua macchina locale, esegui la decompressione (`unzip`) e passa (`cd`) alla directory root. Ora puoi installare i prerequisiti utilizzando il metodo dei comandi delle piattaforme:

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows (esegui come Amministratore):
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## Aggiunta di un servizio
{: #add_service}

1. Ritorna al progetto nella {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}.
2. Fai clic su **Aggiungi servizio**, seleziona la categoria del servizio che vuoi aggiungere, fai clic su **Avanti** e scegli quindi il tuo servizio. Ad esempio, per aggiungere un database NoSQL alla tua applicazione, fai clic sulla categoria **Data** e seleziona **Cloudant**, che offre un piano lite per lo sviluppo gratuito. La {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} esegue il provisioning del servizio per te in base al piano selezionato.
Nota: se hai in precedenza eseguito il provisioning del servizio che intendi utilizzare, scegli la categoria **Existing**.
3. Dopo che è stato eseguito il provisioning del servizio, fai clic su **Download Code** per rigenerare il progetto con l'SDK che si connette al tuo servizio.

<!--
<video of creating a project and adding a service>
-->

## Esecuzione delle applicazioni in locale
{: #run_local}

1. Utilizza il comando `ibmcloud dev build` per creare la tua applicazione.
2. Utilizza il comando `ibmcloud dev run` per eseguire l'applicazione in locale. La tua applicazione viene eseguita nel contenitore Docker sul tuo sistema locale. Puoi testare la tua applicazione in un browser accedendo a `http://localhost:3000`.
3. Un endpoint di controllo dell'integrità è disponibile all'indirizzo `http://localhost:3000/health`.
4. Puoi accedere al dashboard [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/) all'indirizzo `http://localhost:3000/appmetrics-dash`.

<!--
<video>
-->

## Distribuzione al cloud
{: #deploy_cloud}

Utilizza il comando `ibmcloud dev deploy` per eseguire la distribuzione a {{site.data.keyword.cloud_notm}} come un'applicazione Cloud Foundry. 

Per eseguire la distribuzione a IBM Container Services in {{site.data.keyword.cloud_notm}}, utilizza questo comando:
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## Passi successivi
{: #nextsteps}

Continua consultando gli argomenti nella guida alla programmazione di Node.js oppure, per distribuzioni più avanzate, puoi imparare a creare un cluster Kubernetes e distribuire a esso la tua applicazione Node.js.

### Configura un cluster Kubernetes
Per ulteriori informazioni sulla configurazione di un cluster Kubernetes in {{site.data.keyword.cloud_notm}}, consulta i [passi dell'esercitazione](https://console.bluemix.net/docs/containers/cs_clusters.html#clusters).

### Distribuisci le applicazioni Node.js al cluster Kubernetes
Impara come [distribuire applicazioni Node.js a un cluster Kubernetes](../containers/cs_tutorials_apps.html).
