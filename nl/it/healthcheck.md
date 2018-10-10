---

copyright:
  years: 2018
lastupdated: "2018-09-18"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilizzo del controllo di integrità nelle applicazioni Node.js
{: #healthcheck}

I controlli di integrità forniscono un semplice meccanismo per determinare se un'applicazione lato server sta funzionando correttamente o meno. Molti ambienti di distribuzione, quali [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) e [Kubernetes](https://www.ibm.com/cloud/container-service), possono essere configurati per eseguire il polling di endpoint di integrità con cadenza periodica per determinare se un'istanza del tuo servizio è pronta ad accettare traffico.

## Panoramica dei controlli di integrità
{: #overview}

Ai controlli di integrità si accede di norma su `HTTP`; tali controlli utilizzano dei codici di ritorno standard per indicare uno stato di `UP` o `DOWN`. Degli esempi includono la restituzione di `200` per `UP` e `5xx` per `DOWN`. Ad esempio, un codice di ritorno `503` viene utilizzato quando l'applicazione non è in grado di gestire delle richieste e un codice di ritorno `500` viene utilizzato quando il server riscontra una condizione di errore. Il valore di ritorno di un controllo di integrità è variabile ma una risposta JSON minima, come `{“status”: “UP”}` fornisce una congruenza.

Kubernetes definisce dei probe sia di attività ([liveness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)) che di disponibilità ([readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)).

* Il probe di attività (liveness) consente a un'applicazione di indicare se il processo può essere riavviato. Si applica un sottoinsieme di considerazioni: un controllo `liveness` potrebbe non riuscire se viene raggiunta una soglia di memoria, ad esempio: Se la tua applicazione è in esecuzione su Kubernetes, prendi in considerazione l'aggiunta di un endpoint `liveness` per assicurarti che il processo venga riavviato quando necessario.

* Il probe di disponibilità (readiness) viene utilizzato per le decisioni di instradamento automatiche. La riuscita o meno indica se l'applicazione può ricevere nuovo lavoro. Questo endpoint può includere delle considerazioni per i servizi di downstream richiesti. Considera la memorizzazione in cache del risultato dei controlli dei servizi di downstream per ridurre al minimo il carico complessivo sul sistema (ad esempio, esegui il test della connettività al database al massimo una volta al secondo).

Cloud Foundry utilizza solo un singolo endpoint di integrità. Se questo controllo non riesce, Cloud Foundry riavvia il processo. Se però riesce, Cloud Foundry presume che il processo possa gestire nuovo lavoro. Questo controllo di integrità rende il singolo endpoint una sorta di combinazione tra attività (liveness) e disponibilità (readiness).

## Aggiunta del controllo di integrità a un'applicazione Node.js esistente
{: #add-healthcheck-existing}

Per aggiungere un endpoint di controllo di integrità a un'applicazione esistente, inizia introducendo un nuovo instradamento, come mostrato nel seguente esempio:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

L'aggiunta di criteri ponderati qui garantisce che una risposta positiva dall'endpoint `/health` indichi che il servizio è pronto a gestire richieste.

## Accesso al controllo di integrità dalle applicazioni kit starter Node.js
{: #healthcheck-starterkit}

Per impostazione predefinita, quando generi un'applicazione Node.js utilizzando un kit starter, un endpoint di controllo di integrità di base (non autorizzato) è disponibile in `/health` per controllare lo stato dell'applicazione (UP/DOWN).

Il codice dell'endpoint di controllo di integrità viene fornito dal seguente file `/server/routers/health.js`:
```js
var express = require('express');

module.exports = function(app) {
  var router = express.Router();

  router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });

  app.use("/health", router);
}
```
{: codeblock}

Puoi personalizzare il controllo per assicurarti che restituisca uno stato di riuscito solo quando l'applicazione è pronta ad accettare traffico.
{: tip}
