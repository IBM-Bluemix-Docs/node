---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-13"

keywords: deploy nodejs app, serverless nodejs, back-end nodejs, generated sdk nodejs, cloud foundry deploy nodejs, kubernetes deploy nodejs, virtual nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Distribuzione e integrazione di applicazioni Node.js
{: #deploy_apps-nodejs}

Puoi distribuire le tue applicazioni Node.js con una toolchain oppure utilizzando l'interfaccia riga di comando. Una toolchain è una serie di integrazioni degli strumenti. L'interfaccia riga di comando è un modo semplice per distribuire le tue applicazioni e le tue istanze del servizio.

Per ulteriori informazioni, consulta [Distribuzione delle applicazioni](/docs/apps?topic=creating-apps-deploying-apps).

## Distribuzione di applicazioni Node.js senza server
{: #serverless-nodejs}

Per facilitare lo sviluppo senza server, puoi utilizzare l'offerta IBM FaaS (Functions as a Service), {{site.data.keyword.openwhisk}}. {{site.data.keyword.openwhisk_short}} abilita l'esecuzione della logica applicativa in risposta a eventi o a richiami diretti da applicazioni web o mobili su HTTP senza eseguire il provisioning o la gestione di server.

{{site.data.keyword.openwhisk_short}} esegue l'amministrazione del sistema, come il ridimensionamento automatico, la gestione della disponibilità e la manutenzione, consentendo agli sviluppatori di rimanere concentrati sulla scrittura della logica dell'applicazione.

Per ulteriori informazioni, vedi [Sviluppo di applicazioni senza server](/docs/apps/deploying?topic=creating-apps-serverless).

## Integrazione dei servizi di back-end con un SDK generato
{: #backend_gensdk}

Il plug-in {{site.data.keyword.IBM_notm}} SDK Generator integra facilmente i servizi di back-end alla tua applicazione con un SDK generato. Quando si verifica una modifica a un'API REST, puoi rigenerare l'SDK e sostituire quello precedente per un upgrade dell'SDK senza soluzione di continuità. Puoi anche integrare la CLI in una pipeline DevOps e assicurare che l'SDK sia sempre congruente con la specifica API ogni volta che l'applicazione viene creata.

Per ulteriori informazioni, vedi [Integrazione dei servizi di back-end con un SDK generato](/docs/swift/backend?topic=swift-sdkgen-cli).

## Distribuzione a un cluster Kubernetes
{: #deploy_kube}

Puoi imparare come utilizzare il servizio Kubernetes {{site.data.keyword.cloud_notm}} per distribuire un'applicazione Node.js inserita nel contenitore che utilizza Watson Tone Analyzer. Nello scenario fornito, una società di PR fittizia utilizza il servizio {{site.data.keyword.cloud_notm}} per analizzare i suoi comunicati stampa e ricevere un feedback sul tono dei suoi messaggi. Per ulteriori informazioni, vedi l'esercitazione [Distribuzione di applicazioni nei cluster Kubernetes](/docs/containers?topic=containers-cs_apps_tutorial).

## Distribuzione a Cloud Foundry
{: #node-deploy-cf}

Questa opzione distribuisce la tua applicazione nativa del cloud senza che tu debba gestire l'infrastruttura sottostante.

Se intendi distribuire la tua applicazione a [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry?topic=cloud-foundry-about), devi [preparare il tuo account {{site.data.keyword.cloud_notm}}](/docs/cloud-foundry?topic=cloud-foundry-prepare).

Se il tuo account ha accesso a {{site.data.keyword.cfee_full_notm}}, puoi selezionare un tipo di deployer **[Cloud pubblico](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** o **[Ambiente aziendale](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)**, che puoi utilizzare per creare e gestire ambienti isolati per ospitare applicazioni Cloud Foundry esclusivamente per la tua azienda.

## Distribuzione a un Virtual Server
{: #virtual_deploy}

Un servizio virtuale offre un elevato grado di trasparenza, prevedibilità e automazione per tutti i tipi di carichi di lavoro. Viene utilizzato Terraform per creare, modificare e controllare la versione dell'infrastruttura in modo sicuro ed efficiente.

  La distribuzione di VSI è disponibile per alcuni kit starter. Per utilizzare questa funzione, vai al [dashboard {{site.data.keyword.cloud_notm}}](https://{DomainName}) e fai clic su **Create an app** nel tile **Apps**.
  {: note} 

Per ulteriori informazioni, vedi [Distribuzione a un server virtuale](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server).
