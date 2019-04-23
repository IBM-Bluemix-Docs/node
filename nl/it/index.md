---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-15"

keywords: node getting started, node cloud native, create node app, add node service, node programming guide, node guide

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Esercitazione introduttiva
{: #node-getting-started}

La seguente esercitazione ti guida attraverso i passi per creare, eseguire in locale e distribuire un'applicazione Node.js utilizzando gli strumenti {{site.data.keyword.cloud_notm}} forniti. Puoi utilizzare [{{site.data.keyword.dev_cli_long}}](/docs/cli?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli) sulla riga di comando oppure l'[{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno") basato sul web, come mostrato nei seguenti passi dell'esercitazione. Utilizzando l'uno o l'altro di questi metodi, puoi generare un'applicazione Node.js pronta per la produzione in pochi minuti.

Assicurati che stai utilizzando la release di LTS Node.js più recente.

## Creazione di un'applicazione Node.js
{: #node-create-project}

1. Dalla pagina [Kit starter](https://cloud.ibm.com/developer/appservice/starter-kits){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno") nella {{site.data.keyword.dev_console}}, seleziona un kit starter scritto in `Node.js`. Puoi anche creare un'applicazione starter vuota facendo clic su **Crea applicazione** e selezionando `Node.js` come linguaggio.

    Per creare un'applicazione, devi essere collegato a un account {{site.data.keyword.cloud_notm}}. Se non hai un account, puoi [eseguire la registrazione per un account gratuito](https://cloud.ibm.com/registration){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno").
    {: tip}

2. Fai clic su **Crea applicazione**.
3. Usa **Nome** per denominare la tua applicazione. Viene fornito un nome applicazione generico, se vuoi utilizzarlo.
4. Immetti un **nome host univoco**. Il nome host viene utilizzato per accedere alla tua applicazione, ad esempio: `expressjs-project.mybluemix.net`.
5. Fare clic su **Crea**. Dopo che il tuo progetto è stato creato, puoi distribuirlo utilizzando una toolchain oppure puoi continuare a creare e distribuire il tuo progetto dalla riga di comando.
6. Per creare una toolchain di distribuzione nel dashboard, fai clic su **Distribuisci**. Configura la tua destinazione di distribuzione in base alle istruzioni per il metodo che scegli:
  * **Distribuisci a [IBM Kubernetes Service](/docs/apps/deploying?topic=creating-apps-containers-kube#containers)**. Questa opzione crea un cluster di host, denominati nodi di lavoro, per distribuire e gestire contenitori applicazione ad elevata disponibilità. Puoi creare un cluster o eseguire la distribuzione a un cluster esistente.
  * **Distribuisci a Cloud Foundry**. Questa opzione distribuisce la tua applicazione nativa del cloud senza che tu debba gestire l'infrastruttura sottostante. Se il tuo account ha accesso a {{site.data.keyword.cfee_full_notm}}, puoi selezionare un tipo di deployer **[Cloud pubblico](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf#about-cf)** o **[Ambiente aziendale](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee#cfee)**, che puoi utilizzare per creare e gestire ambienti isolati per ospitare applicazioni Cloud Foundry esclusivamente per la tua azienda.
  * **Distribuisci a un [Virtual Server](/docs/apps?topic=creating-apps-vsi-deploy#vsi-deploy)**. Questa opzione esegue il provisioning di un'istanza del server virtuale, carica un'immagine che include la tua applicazione, crea una toolchain DevOps e avvia il primo ciclo di distribuzione per tuo conto.

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
{: #node-add-service}

1. Ritorna al progetto nella {{site.data.keyword.dev_console}} {{site.data.keyword.cloud_notm}}.
2. Fai clic su **Add service**, seleziona la categoria del servizio che vuoi aggiungere, fai clic su **Next** e scegli quindi il tuo servizio. Ad esempio, per aggiungere un database NoSQL alla tua applicazione, fai clic sulla categoria **Data** e seleziona **Cloudant**, che offre un piano lite per lo sviluppo gratuito. La {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} esegue il provisioning del servizio per te in base al piano selezionato.
Nota: se hai in precedenza eseguito il provisioning del servizio che intendi utilizzare, scegli la categoria **Existing**.
3. Dopo che è stato eseguito il provisioning del servizio, fai clic su **Download code** per rigenerare il progetto con l'SDK che si connette al tuo servizio.

<!--
<video of creating a project and adding a service>
-->

## Esecuzione delle applicazioni in locale
{: #node-run-local}

1. Utilizza il comando `ibmcloud dev build` per creare la tua applicazione.
2. Utilizza il comando `ibmcloud dev run` per eseguire l'applicazione in locale. La tua applicazione viene eseguita nel contenitore Docker sul tuo sistema locale. Puoi testare la tua applicazione in un browser accedendo a `http://localhost:3000`.
3. Un endpoint di controllo dell'integrità è disponibile all'indirizzo `http://localhost:3000/health`.
4. Puoi accedere al dashboard [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/){: new_window} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno") all'indirizzo `http://localhost:3000/appmetrics-dash`.

<!--
<video>
-->

## Distribuzione a IBM Cloud
{: #deploy_cloud}

Utilizza il comando `ibmcloud dev deploy` per eseguire la distribuzione a {{site.data.keyword.cloud_notm}} come un'applicazione Cloud Foundry. 

Per eseguire la distribuzione a IBM Container Services in {{site.data.keyword.cloud_notm}}, utilizza questo comando:
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## Passi successivi
{: #node-tutorial-nextsteps notoc}

Continua consultando gli argomenti nella guida alla programmazione di Node.js oppure, per distribuzioni più avanzate, puoi imparare a creare un cluster Kubernetes e distribuire a esso la tua applicazione Node.js.

### Configura un cluster Kubernetes
Per ulteriori informazioni sulla configurazione di un cluster Kubernetes in {{site.data.keyword.cloud_notm}}, consulta i [passi dell'esercitazione](/docs/containers?topic=containers-clusters#clusters).

### Distribuisci le applicazioni Node.js al cluster Kubernetes
Impara come [distribuire applicazioni Node.js a un cluster Kubernetes](/docs/containers?topic=containers-cs_apps_tutorial#cs_apps_tutorial).
