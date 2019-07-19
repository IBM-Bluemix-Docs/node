---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-11"

keywords: nodejs metrics, application metrics nodejs, node appmetrics, nodejs autoscaling, nodejs dash, appmetrics-dashs nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Anwendungsmetriken mit Node.js-Apps verwenden
{: #metrics}

Hier erfahren Sie, wie Sie die Node.js-Anwendungsmetriken installieren, auf sie zugreifen und sie besser verstehen können. Sie können Node.js-Apps mit dem Dashboard [Node Application Metrics](https://developer.ibm.com/open/projects/node-application-metrics/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") überwachen, um die Leistung Ihrer Node.js-Anwendung zu visualisieren, indem Metriken in einem webbasierten Front-End angezeigt werden.
{: shortdesc}

## Probleme in visueller Form darstellen
{: #identify-problems}

Anwendungsmetriken sind wichtig für die Überwachung der Leistung Ihrer Anwendung. Eine Live-Ansicht von Metriken wie CPU-, Speicher-, Latenzzeit- und HTTP-Metriken ist erforderlich, um sicherzustellen, dass Ihre Anwendung über einen bestimmten Zeitraum hinweg effektiv ausgeführt wird. Zur Anpassung an die aktuelle Workload durch dynamische Skalierung von Instanzen können Sie einen Cloud-Service wie den [Auto-Scaling-Service](/docs/services/Auto-Scaling?topic=Auto-Scaling) von Cloud Foundry verwenden, der sich auf Metriken stützt. Durch die Verwendung von Anwendungsmetriken werden Sie genau informiert, wann Sie ein Scale-up oder ein Scale-down für Instanzen durchführen oder Instanzen löschen müssen, wenn diese nicht mehr benötigt werden, um die Kosten niedrig zu halten.

Anwendungsmetriken werden als Zeitreihendaten erfasst. Das Zusammenfassen und Visualisieren erfasster Metriken kann helfen, allgemeine Leistungsprobleme wie die folgenden zu erkennen:

* Lange HTTP-Antwortzeiten bei einigen oder allen Routen
* Schlechter Durchsatz in der Anwendung
* Nachfragespitzen, die zur einer Leistungsminderung führen
* Höhere CPU-Belastung als erwartet
* Hohe oder steigende Speicherbelegung (möglicher Speicherverlust)

Das integrierte Dashboard für Anwendungsmetriken ([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link")) enthält auch ein Diagramm für 'Weitere Anforderungen', das die Dauer der Datenbankanforderung für unterstützte Datenbanken (MongoDB, MySQL, Postgres, LevelDB und Redis), Socket.IO und Riak-Ereignisse anzeigt.

Über das Dashboard kann ein Knotenbericht oder eine Heapspeichermomentaufnahme generiert werden, um eine detailliertere Analyse zu ermöglichen.

## Metriken zu vorhandenen Node.js-Apps hinzufügen
{: #add-appmetrics-existing}

Fügen Sie Überwachungsfunktionen zu vorhandenen Express-Anwendungen mit dem Konstruktor [`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") hinzu, um eine Reihe von Konfigurationsoptionen zu übergeben. Eine der Optionen verwendet beispielsweise einen vorhandenen Server, anstatt mit `appmetrics-dash` einen zusätzlichen Server zu starten.

### Dashboard installieren
{: #install-appmetrics}

1. Verwenden Sie zum Beispiel die folgende einfache Express-Anwendung 'Hello World':
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

2. Installieren Sie das Dashboard `appmetrics` mit dem folgenden Befehl [npm](https://nodejs.org/en/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link"):
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. Fügen Sie Unterstützung für `appmetrics-dash` zu Ihrer vorhandenen App hinzu, indem Sie den folgenden Code hinzufügen:
  ```js
  var dash = require('appmetrics-dash').attach()
  ```
  {: codeblock}

  Dieser Befehl fordert `appmetrics-dash` auf, den bereits erstellten Server zu verwenden und einen Endpunkt des Typs `appmetrics-dash` hinzuzufügen.

  Der überarbeitete Code sieht nun wie im folgenden Beispiel aus:
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

## Metriken von Starter-Kits verwenden
{: #appmetrics-starterkits}

Die Node.js-Anwendungen, die von Starter-Kits erstellt werden, werden automatisch mit `appmetrics-dash` und dem zugehörigen Dashboard standardmäßig bereitgestellt, müssen jedoch für die Verwendung aktiviert werden.

Der appmetrics-Code befindet sich in der generierten Quellendatei der Anwendung namens `/app_name/server/server.js`:
```js
// Entfernen Sie die Kommentarzeichen im folgenden Code, um das Zipkin-Tracing zu aktivieren und passen Sie ihn entsprechend Ihrer Netzkonfiguration an:
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## Auf das Dashboard zugreifen
{: #access-dashboard}

Nachdem Sie Ihre Anwendung gestartet haben, rufen Sie `http://<hostname>:<port>/appmetrics-dash` in einem Browser auf.

Verwenden Sie den Standardwert `localhost:3001/appmetrics-dash` für Apps, die lokal ausgeführt werden.
{: tip}

Die Benutzerschnittstelle des Überwachungsdashboards von Application Metrics for Node.js stellt eine Reihe von Metriken bereit, einschließlich HTTP-Anforderungen und Ereignisschleifenlatenz, wie im folgenden Video zu [Überwachungsmetriken für Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") zu sehen ist.

## Informationen zu den Daten
{: #understanding-data}

![Appmetrics-Dashboard](images/appmetricsdash-1.png "Appmetrics-Dashboard.")

Die meisten Daten werden in Form von Kurvendiagrammen dargestellt. 'Eingehende HTTP-Anforderungen', 'Abgehende HTTP-Anforderungen' und 'Weitere Anforderungen' zeigen die Ereignisdauer im Zeitverlauf an. 'HTTP-Durchsatz' zeigt Anforderungen pro Sekunde an. 'Durchschnittliche Antwortzeiten' zeigt die fünf eingehenden HTTP-Anforderungen an, die im Durchschnitt am längsten dauerten. Die Diagramme zu CPU und Speicher zeigen die System- und Prozessnutzung im Zeitverlauf an. Heapspeicher zeigt die maximale Größe des Heapspeichers und die belegte Größe des Heapspeichers im Laufe der Zeit an. Die Latenz von Ereignisschleifen zeigt Latenzbeispiele, die in Intervallen aus der Node.js-Ereignisschleife entnommen werden, mit einem Punkt für die kürzeste Latenzzeit, einem Punkt für die durchschnittliche Latenzzeit und einem Punkt für die längste Latenzzeit für jede entnommene Stichprobe.

Wenn ein Diagramm Punkte enthält, können Sie durch Bewegen des Mauszeigers über diese Punkte weitere Informationen anzeigen. Beispiel: Für `Eingehende HTTP-Anforderungen` werden die Antwortzeit und die angeforderte URL angezeigt.

In allen Diagrammen zusammen werden maximal 15 Minuten an Daten angezeigt.

Wenn eine große Datenmenge von der überwachten Anwendung produziert wird, beginnt das Dashboard automatisch mit der Anzeige von Daten. Wenn Sie sich das Diagramm für eingehende HTTP-Anforderungen erneut ansehen, können Sie sehen, dass jeder Punkt alle Anforderungen für einen 2-Sekunden-Zeitraum anzeigt. Die folgende QuickInfo zeigt die Gesamtzahl der Anforderungen zusammen mit der durchschnittlichen Zeit und der längsten Zeit an. Die längste Zeit ist der Wert, der dargestellt wird.

![QuickInfo anzeigen](images/tooltip-1.png)




