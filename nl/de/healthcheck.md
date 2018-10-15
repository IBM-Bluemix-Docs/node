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

# Statusprüfung in Node.js-Apps verwenden
{: #healthcheck}

Statusprüfungen bieten einen einfachen Mechanismus, um festzustellen, ob eine serverseitige Anwendung sich ordnungsgemäß verhält. Viele Bereitstellungsumgebungen wie [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) und [Kubernetes](https://www.ibm.com/cloud/container-service) können so konfiguriert werden, dass in regelmäßigen Abständen Statusendpunkte abgefragt werden, um festzustellen, ob eine Instanz Ihres Service bereit ist, Datenverkehr zu akzeptieren.

## Übersicht über die Statusprüfung
{: #overview}

Auf Statusprüfungen wird in der Regel über `HTTP` zugegriffen und sie verwenden Rückgabecodes zum Anzeigen der Statusangaben `UP` oder `DOWN`. Beispiele hierfür sind die Rückgabe von `200` für `UP` und `5xx` für `DOWN`. Der Rückgabecode `503` wird beispielsweise verwendet, wenn die Anwendung keine Anforderungen verarbeiten kann und der Code `500`, wenn für den Server eine Fehlerbedingung auftritt. Der Rückgabewert einer Statusprüfung ist variabel, aber eine minimale JSON-Antwort wie `{“status”: “UP”}` gewährleistet Konsistenz.

Kubernetes definiert sowohl [Liveness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)- und [Readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)-Sonden:

* Die Liveness-Sonde ermöglicht es einer Anwendung anzugeben, ob der Prozess erneut gestartet werden kann. Dabei gelten einige untergeordnete Überlegungen: Eine `liveness`-Überprüfung kann beispielsweise fehlgeschlagen, wenn ein Speicherschwellenwert erreicht wird. Wenn Ihre App unter Kubernetes ausgeführt wird, sollten Sie einen `liveness`-Endpunkt hinzufügen, um sicherzustellen, dass Ihr Prozess bei Bedarf erneut gestartet wird.

* Die Readiness-Sonde wird bei Entscheidungen für das automatische Routing verwendet. Eine erfolgreiche Durchführung oder ein Fehlschlagen entscheiden darüber, ob die Anwendung neue Arbeit erhalten kann. Dieser Endpunkt kann Aspekte für erforderliche Downstream-Services enthalten. Ziehen Sie in Betracht, das Ergebnis von Downstream-Serviceprüfungen zwischenzuspeichern, um die Gesamtbelastung für das System zu minimieren (z. B. Testen der Datenbankkonnektivität höchstens einmal pro Sekunde).

Cloud Foundry verwendet nur einen einzigen Statusendpunkt. Wenn diese Prüfung fehlschlägt, startet Cloud Foundry den Prozess erneut. Wenn sie jedoch erfolgreich ist, geht Cloud Foundry davon aus, dass der Prozess neue Arbeit verarbeiten kann. Durch diese Statusprüfung wird der einzelne Endpunkt zu einer Art Kombination aus 'Liveness' (Lebendigkeit) und 'Readiness (Bereitschaft).

## Statusprüfung zu einer vorhandenen Node.js-App hinzufügen
{: #add-healthcheck-existing}

Um einen Endpunkt für die Statusprüfung zu einer vorhandenen App hinzuzufügen, führen Sie eine neue Route ein, wie im folgenden Beispiel dargestellt:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

Durch das Hinzufügen aussagekräftiger Kriterien wird sichergestellt, dass eine erfolgreiche Antwort vom Endpunkt `/health` korrekt angibt, dass der Service bereit ist, Anforderungen zu verarbeiten.

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

Sie können die Prüfung anpassen, um sicherzustellen, dass sie nur dann als erfolgreich zurückgegeben wird, wenn die Anwendung bereit ist, Datenverkehr zu akzeptieren.
{: tip}
