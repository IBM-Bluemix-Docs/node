---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-05"

keywords: nodejs tracing, debug nodejs apps, troubleshooting nodejs, appmetrics-zipkin node, zipkin docker nodejs, nodejs slow, nodejs tracing

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Umfassendes Tracing einrichten
{: #e2e-tracing}

Das folgende Lernprogramm konzentriert sich auf Zipkin und die Verwendung des Moduls [appmetrics-zipkin](https://github.com/CloudNativeJS/appmetrics-zipkin){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") für das Tracing von Node.js-Anwendungen. Weitere Informationen zu Zipkin finden Sie in der ursprünglichen [Ankündigung zu appmetrics-zipkin](https://developer.ibm.com/node/2017/10/26/add-zipkin-open-tracing-support-node-js-application-one-line-code/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link"). 

In den folgenden Schritten werden zwei kleine Anwendungen (ein Front-End, ein Back-End) verwendet, um mit dem Modul `appmetrics-zipkin` zwischen zwei Endpunkten ein Trace zu erstellen. Sie können bei Null anfangen oder die hier beschriebenen Prinzipien auf Ihre vorhandenen Node.js-Anwendungen anwenden. 

## Schritt 1. Modul 'appmetrics-zipkin' installieren und aktivieren
{: #install-zipkin}

Geben Sie an der Position, an der sich auch die Datei `package.json` Ihrer Node.js-Anwendung befindet, den folgenden Befehl [npm](https://nodejs.org/en/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") ein, um das Modul `appmetrics-zipkin` Ihrer Abhängigkeitsliste hinzuzufügen:
```
npm install --save appmetrics-zipkin
```
{: codeblock}

Fügen Sie die folgende Zeile zu Ihrem Node.js-Server-Code hinzu, und zwar **VOR** allen anderen appmetrics-Anweisungen des Typs `require`:
```js
var appzip = require('appmetrics-zipkin');
```
{: codeblock}

Die folgende Anweisung bewirkt, dass das Tracing zu Ihren `HTTP`- und `request`-Methodenaufrufen hinzugefügt wird und die Daten an den Zipkin-Server gesendet werden. Das Modul sucht standardmäßig bei `localhost` und Port `9411` nach dem Zipkin-Server. Sie können den Hostnamen und den Port mithilfe der folgenden Syntax ändern:
```js
var appzip = require('appmetrics-zipkin')({
 host: "my.host.here",
 port: 12345, // changeme
 serviceName:'my-service-name'
});
```
{: codeblock}

Senden Sie eine Anforderung, wie Sie es üblicherweise tun würden. Zum Beispiel:
```
http.request(options, callback).end();
```
{: codeblock} 

## Schritt 2. Zipkin-Server einrichten
{: #setup-zipkin-server}

Sie benötigen nun eine Position, an die Ihre Daten gesendet werden sollen, insbesondere die Traces, die aus Bereichen bestehen. Vor der Bereitstellung in einer Cloud können Sie die Konfiguration der e2e-Tracefunktion testen, indem Sie einen Zipkin-Server lokal oder in einem Container einrichten. 

### Zipkin lokal einrichten
{: #local-setup-zipkin}

Zipkin wird als einzelne `jar`-Datei bereitgestellt, sodass Sie das Traceprogramm mit den folgenden Befehlen auf dem System herunterladen und ausführen können, auf dem Sie Zipkin zur Verfügung stellen möchten:

1. So laden Sie Zipkin herunter:
  ```
  wget zipkin.jar 'https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=1.31.3&c=exec'
  ```
  {: codeblock}

2. So starten Sie Zipkin:
  ```
  java -jar zipkin.jar
  ```
  {: codeblock}

  Mit dem Befehl `wget` wird die Zipkin-Datei heruntergeladen und mit dem Befehl `java -jar` wird der Zipkin-Server gestartet. Sie können Zipkin auch von anderen Positionen herunterladen, es ist jedoch wichtig, dass Sie Version 1.x für dieses Lernprogramm verwenden, damit das Trace-Format mit dem übereinstimmt, was der Zipkin-Server erwartet.

  Wenn die Ausgabe dieses Befehls zu ausführlich ist oder Sie Zipkin im Hintergrund ausführen möchten, können Sie `-q -O` für den Befehl `wget` und `/dev/null 2>&1 &` für Zipkin hinzufügen. An diesem Punkt laden Sie die `.jar`-Datei für Zipkin herunter und führen die Hauptmethode zum Starten des Zipkin-Servers aus.

### Zipkin in einem Docker-Container einrichten
{: #setup-docker-zipkin}

Sie können optional einen Zipkin-Server in einem Docker-Container ausführen, indem Sie den folgenden Befehl ausgeben:
```
docker run -d -p 9411:9411 openzipkin/zipkin
```
{: codeblock}

Das Modul `openzipkin/zipkin` wird heruntergeladen, installiert und an Port `9411` mit einem einfachen Befehl gestartet.

### Auf die Zipkin-Konsole zugreifen
{: #zipkin-console}

Die folgende Abbildung zeigt den Zipkin-Server, der auf `localhost` bei Port `9411` ausgeführt wird:

![ZipkinNoData](images/ZipkinNoData.png)

Sie können auf die Option zum **Suchen nach Traces** klicken und die Suchoptionen so ändern, dass selektiv nur Traces innerhalb eines bestimmten Zeitraums angezeigt werden. Sie können auch einen Filter anwenden, um Traces für bestimmte Servicenamen anzuzeigen. Die Servicenamen werden beim Instrumentieren Ihres Codes angegeben; im Beispielszenario werden 'getter' und 'pusher' verwendet.

## Schritt 3. Beispielszenario testen
{: #node-example-tracing}

Wenn Sie die [Dokumentation des GitHub-Projekts](https://github.com/ibm-developer/nodejs-zipkin-tracing){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") befolgen, erhalten Sie die folgende Beispielanwendung. Es handelt sich um einen einfachen Prozess, bei dem für eine Anforderung und eine Antwort zwischen zwei Endpunkten ein Trace erstellt wird. Die folgenden Abbildungen zeigen den Zipkin-Server mit erfassten Tracedaten. Sie müssen unbedingt daran denken, `require('appmetrics-zipkin')` einzuschließen und optional den Code für die Zipkin-Serverkonfiguration. Das folgende Beispielszenario zeigt, wie Sie das Zipkin-Tracing schnell in Ihren vorhandenen Node.js-Anwendungen hinzufügen können.

### Übersicht über das Tracing-Szenario
{: #tracing-scenario}

* Ein **Front-End**, das als Pusher bezeichnet wird, fordert den Benutzer zur Eingabe der Länge einer Zeichenfolge auf, die erstellt und in Kleinbuchstaben konvertiert werden soll. Je größer die Zahl, umso größer die Zeichenfolge und umso länger dauert es, bis die Anforderung verarbeitet wird. Verfügbar bei Port `3000`.
* Ein **Back-End**, das als Getter bezeichnet wird, verarbeitet die Anforderung und ist bei Port `3001` verfügbar.
* Ein **Zipkin-Server** wird lokal oder auf Kubernetes ausgeführt, wo Ihre Tracedaten angezeigt werden.

### Front-End-App (Pusher)
{: #tracing-pusher}

Der Front-End-App-Service (Pusher) sendet die Anforderung (das einfache Front-End):
![frontend_app](images/frontend_app.png)

### Back-End-App (Getter)
{: #tracing-getter}

Die Back-End-App (Getter) empfängt die Anforderung, die an einem anderen Port empfangsbereit ist:
![backend_app](images/Backend.png)

### Anforderung vom Pusher an den Getter senden
{: #tracing-request}

Senden Sie eine Anforderung vom Pusher an den Getter:
![500please](images/500Please.png)

### Traces in der Zipkin-Webbenutzerschnittstelle anzeigen
{: #tracing-viewing}

Die an Zipkin gesendeten Tracedaten können über die Zipkin-Webbenutzerschnittstelle an `localhost:9411` angezeigt werden. Sie können sehen, dass der **Getter** Benutzereingaben empfängt (es soll eine 500 Zeichen lange Nachricht an den Getter mithilfe des Pusher-Service gesendet werden):
![Getter500msg](images/Getter500Msg.png)

Die Details der Benutzeranforderungen werden angezeigt. Beachten Sie den Wert '500', der der Parameter ist, der für die Anforderung des Benutzers bereitgestellt wird. Es sollte eine Zeichenfolge mit 500 Zeichen generiert werden. Sie können genau sehen, was der Benutzer angefordert und wie lange die Verarbeitung der Anforderung gedauert hat. Der Inhalt der Anforderung (Nutzdaten), der vom Server zurückgegeben wird, ist nicht sichtbar. 

Optimale Antwortzeiten und Parameter sind wichtig; daher ist es möglich festzustellen, was Benutzer anfordern, wenn sie es mit langsamen Antwortzeiten zu tun haben:
![GetterGet](images/GetterGet.png)

### Eine langsame Anforderung identifizieren
{: #tracing-slowreq}

So sieht eine langsame Anforderung aus. Der folgende Benutzer fordert die Konvertierung von 5.000.000 Zeichen von Großbuchstaben in Kleinbuchstaben an (wie Sie es tun). Dies ist etwas, das offensichtlich länger dauert:
![SlowRequest](images/SlowRequest.png)

Wenn Sie auf diesen Bereich klicken, wird die folgende Ausgabe angezeigt. Auch hier sehen Sie die kostenintensive Anforderung, für die viel mehr Zeit benötigt wurde. Ein realistischeres Szenario würde potenziell viele Node.js-Mikroservices beinhalten, die alle Anforderungen kontinuierlich an verschiedenen Endpunkten empfangen. Durch eine übergeordnete Ansicht der Endpunkte können Sie schnell feststellen, welche Services langsam antworten und welche Benutzer genau diese Services anfordern:
![TookaWhile](images/TookAWhile.png)

Bei diesem Beispiel haben Sie nun das folgende Szenario:

* Der Pusher sendet eine Nachricht an den Getter (ein Bereich).
* Der Getter sendet eine Antwort zurück (ein Bereich).
* Der vollständige Trace, der aus den beiden Bereichen besteht, wird auf dem lokal implementierten Zipkin-Server angezeigt.

Wenn Ihre Anwendungen komplexer und Ihre Services immer beliebter werden, wird die Notwendigkeit, eine solche Tracefunktion zu haben, immer offensichtlicher. Das Tracing auf einer hohen Ebene bietet Entwicklern einen Mehrwert, damit Probleme schnell und effektiv erkannt werden können und eine entsprechende Triage ausgeführt werden kann. Es gibt zahlreiche Alternativen; der hier gezeigte Ansatz soll das Vorgehen so einfach und transparent wie möglich machen.

Das Lernprogramm endet hier für Bereitstellungen ohne Kubernetes. Schauen Sie sich den nächsten Abschnitt an, wenn Sie das Tracing für Node.js-Apps, die auf Kubernetes ausgeführt werden, durchführen möchten.

## Nächste Schritte
{: #next-steps-tracing}

* Erfahren Sie, wie Sie für die Cloud native Node.js-Anwendungen mithilfe des Community-Projekts [CloudNativeJS](https://www.cloudnativejs.io/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") erstellen können, das Assets und Tools für die Bereitstellung dieser Anwendungen in Docker- und Kubernetes-basierten Clouds bietet.

* Wenn Sie bereit sind, Tracing zu Ihren Node.js-Anwendungen hinzuzufügen, die unter Kubernetes ausgeführt werden, lesen Sie sich den Abschnitt zu [Tracing für Node.js-Anwendungen, die Kubernetes verwenden](https://developer.ibm.com/node/tutorial-end-end-tracing-node-js-applications/#appservice){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") durch.

