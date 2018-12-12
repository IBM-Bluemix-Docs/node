---

copyright:
  years: 2018
lastupdated: "2018-10-17"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Statusprüfung in Node.js-Apps verwenden
{: #healthcheck}

Statusprüfungen bieten einen einfachen Mechanismus, um festzustellen, ob eine serverseitige Anwendung sich ordnungsgemäß verhält. Cloudumgebungen wie [Kubernetes](https://www.ibm.com/cloud/container-service) und [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) können so konfiguriert werden, dass in regelmäßigen Abständen Statusendpunkte abgefragt werden, um festzustellen, ob eine Instanz Ihres Service bereit ist, Datenverkehr zu akzeptieren.

## Übersicht über die Statusprüfung
{: #overview}

Statusprüfungen bieten einen einfachen Mechanismus, um festzustellen, ob eine serverseitige Anwendung sich ordnungsgemäß verhält. Sie werden in der Regel über HTTP genutzt und verwenden zum Anzeigen der Statusangaben UP oder DOWN standardmäßige Rückgabecodes. Der Rückgabewert einer Statusprüfung ist zwar variabel, aber eine minimale JSON-Antwort wie `{"status": "UP"}` ist die Regel.

Kubernetes hat eine differenzierte Vorstellung des Prozessstatus. Es werden zwei Arten von Tests definiert:

- Ein _**Readiness**_-Test wird verwendet, um anzugeben, ob der Prozess Anforderungen verarbeiten kann (d. h., ob er weiterleitbar ist).

  Kubernetes leitet keinerlei Arbeitsdaten an einen Container weiter, wenn der Readiness-Test fehlschlägt. Ein Readiness-Test kann fehlschlagen, wenn ein Service die Initialisierung noch nicht beendet hat oder anderweitig ausgelastet oder überlastet ist oder Anforderungen nicht verarbeiten kann.

- Ein _**Liveness**_-Test wird verwendet, um anzugeben, ob der Prozess erneut gestartet werden muss.

  Kubernetes stoppt einen Container und startet ihn neu, wenn ein `Liveness`-Test fehlschlägt. Mithilfe von Liveness-Tests ist es möglich, Prozesse zu bereinigen, die sich in einem nicht behebbaren Zustand befinden, z. B. wenn der Hauptspeicher erschöpft ist oder der Container nach dem Fehlschlagen eines internen Prozesses nicht ordnungsgemäß gestoppt wurde.

Im Vergleich dazu nutzt Cloud Foundry einen einzigen Statusendpunkt. Schlägt diese Überprüfung fehl, wird der Prozess erneut gestartet; bei Erfolg werden Anforderungen weitergeleitet. In dieser Umgebung hat der Endpunkt minimalen Erfolg, wenn der Prozess ein Live-Prozess ist. Um Neustartzyklen zu vermeiden wird eine Anfangsverzögerung konfiguriert, um die Statusüberprüfung so lange zurückzustellen, bis der Service mit der Initialisierung fertig ist.

Die folgende Tabelle stellt eine Übersicht der Antworten bereit, die die Readiness- und Liveness-Statusendpunkte sowie singuläre Statusendpunkte zur Verfügung stellen.

| Zustand      | Readiness                   | Liveness                   | Status                     |
|--------------|-----------------------------|----------------------------|----------------------------|
|              | Nicht OK - keine Auslastung | Nicht OK - Neustart        | Nicht OK - Neustart        |
|Wird gestartet| 503 - Nicht verfügbar       | 200 - OK                   | Verzög. zur Testvermeidung |
|Aktiv         | 200 - OK                    | 200 - OK                   | 200 - OK                   |
|Wird gestoppt | 503 - Nicht verfügbar       | 200 - OK                   | 503 - Nicht verfügbar      |
|Inaktiv       | 503 - Nicht verfügbar       | 503 - Nicht verfügbar      | 503 - Nicht verfügbar      |
|Fehlerhaft    | 500 - Serverfehler          | 500 - Serverfehler         | 500 - Serverfehler         |

## Statusprüfung zu einer vorhandenen Node.js-App hinzufügen
{: #add-healthcheck-existing}

Fügen Sie eine minimale Statusprüfung oder eine Liveness-Prüfung zu einer vorhandenen Anwendung hinzu, indem Sie eine neue Route einführen, wie im folgenden Beispiel dargestellt:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

Überprüfen Sie mithilfe eines Browsers den Status der App; greifen Sie hierfür auf den Endpunkt `/health` zu.

## Zugriff auf Statusprüfung von Node.js-Starter-Kit-Apps
{: #healthcheck-starterkit}

Wenn Sie eine Node.js-App mit einem Starter-Kit generieren, ist standardmäßig ein Basisendpunkt (nicht autorisiert) verfügbar, der unter `/health` verfügbar ist, um den Status der App zu überprüfen (UP/DOWN).

Der Code für den Endpunkt der Statusprüfung wird von der folgenden Datei `/server/routers/health.js` bereitgestellt:

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

## Empfehlungen für Readiness- und Liveness-Tests

Readiness-Tests können die Viabilität von Verbindungen zu Downstream-Services in ihrem Ergebnis beinhalten, wenn bei Nichtverfügbarkeit des Downstream-Service kein zulässiger Fallback aufweisbar ist. Dies bedeutet nicht, dass die Statusprüfung direkt aufgerufen wird, die der Downstream-Service bereitstellt; dies wird von der Infrastruktur für Sie übernommen. Stattdessen sollten Sie den Status der bereits vorhandenen Verweise Ihrer Anwendung auf Downstream-Services prüfen: Dies kann eine JMS-Verbindung zu WebSphere MQ oder ein initialisierter Kafka-Consumer oder -Producer sein. Wenn Sie die Viabilität interner Verweise auf Downstream-Services prüfen, stellen Sie das Ergebnis in den Cache, um die Auswirkungen der Statusprüfung auf Ihre Anwendung zu minimieren.

Ein Liveness-Test hingegen verhält sich Überprüfungen gegenüber vorsichtig, da ein Fehler zu einer sofortigen Beendigung des Prozesses führt. Ein einfacher HTTP-Endpunkt, der immer `{"status": "UP"}` mit dem Statuscode `200` zurückgibt, ist eine angemessene Wahl.

### Unterstützung für Kubernetes-Readiness und -Liveness hinzufügen

Die Bibliothek [`cloud-health-connect`](https://github.com/CloudNativeJS/cloud-health-connect) aus [CloudNativeJS] stellt ein Framework für die Definition separater Liveness- und Readiness-Endpunkte in Node bereit; diese ermöglichen die Erstellung von Quellen für den Zustand der einzelnen Endpunkte.

## Readiness- und Liveness-Tests in Kubernetes konfigurieren

Deklarieren Sie bei Ihrer Kubernetes-Bereitstellung Liveness- und Readiness-Tests. Beide Arten von Tests arbeiten mit denselben Konfigurationsparametern:

* Das Kubelet wartet eine Anzahl von Sekunden (Anfangsverzögerung `initialDelaySeconds`), bevor der erste Test stattfindet.

* Das Kubelet testet den Service alle paar Sekunden (eine angegebene Anzahl an Sekunden (`periodSeconds`)). Der Standardwert ist 1.

* Das Zeitlimit für den Test wird nach einer angegebenen Anzahl an Sekunden (`timeoutSeconds`) überschritten. Der Standardwert sowie der Minimalwert ist 1.

* Der Test ist dann erfolgreich, wenn er eine bestimmte Anzahl an Malen (`successThreshold`) nach einem Fehler erfolgreich war. Der Standardwert sowie der Minimalwert ist 1. Der Wert muss bei Liveness-Tests 1 sein.

* Wird ein Pod gestartet und schlägt der Test fehl, versucht Kubernetes, eine angegebene Anzahl an Malen (`failureThreshold`), den Pod erneut zu starten und stellt die Versuche dann ein. Der Minimalwert ist 1, der Standardwert ist 3.
    - Bei einem Liveness-Test bedeutet "einstellen", dass der Pod erneut gestartet wird.
    - Bei einem Readiness-Test bedeutet "einstellen", dass der Pod als 'Nicht betriebsbereit' (`Unready`) markiert wird.

Setzen Sie zur Vermeidung von Neustartzyklen den Wert für `livenessProbe.initialDelaySeconds` so, dass er deutlich nach der Serviceinitialisierung liegt. Sobald der Service betriebsbereit ist, können Sie dann einen kleineren Wert für `readinessProbe.initialDelaySeconds` verwenden, um Anforderungen an diesen weiterzuleiten.

Sehen Sie sich die folgende Beispielkonfiguration an:
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```
{: codeblock}

Weitere Informationen finden Sie in [Configure liveness and readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).
