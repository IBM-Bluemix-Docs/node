---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-04"

keywords: cos nodejs, object storage nodejs, nodejs data, file storage nodejs, ibm-cos-sdk nodejs, creating object nodejs, downloading object nodejs, static nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Statischen Inhalt in Object Storage speichern
{: #object-storage}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://cloud.ibm.com/docs/services/cloud-object-storage/libraries/node.html#using-node-js -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://cloud.ibm.com/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage -->

{{site.data.keyword.cos_full_notm}} ist eine grundlegende Komponente des Cloud-Computing und stellt leistungsfähige Funktionen für Apple-Entwickler und deren Anwendungen bereit. Im Gegensatz zum Speichern von Informationen in einer Dateihierarchie (z. B. in einem Block- oder Dateispeicher) besteht ein Objektspeicher nur aus den Dateien und den zugehörigen Metadaten. Diese Dateien werden in Datensammlung gespeichert, die als Buckets bezeichnet werden. Diese Objekte sind definitionsgemäß unveränderlich, wodurch sie perfekt für Daten wie Bilder, Videos und andere statische Dokumente sind. Für Daten, die sich entweder häufig ändern oder relationale Daten sind, können Sie den [{{site.data.keyword.cloudant_short_notm}}](/docs/node?topic=nodejs-cloudant)-Datenbankservice verwenden.

{{site.data.keyword.cos_short}} (COS) ist ein Speichersystem, das verwendet werden kann, um unstrukturierte Daten zu speichern, die flexibel, kosteneffizient und skalierbar sind. Der Zugriff auf die Daten erfolgt über SDKs oder die IBM Benutzerschnittstelle. Sie können {{site.data.keyword.cos_short}} verwenden, um über ein Self-Service-Portal auf Ihre unstrukturierten Daten zuzugreifen, das von RESTful-APIs und SDKs unterstützt wird.

## Vorbereitende Schritte
{: #prereqs-cos}

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:
1. Sie müssen über ein [{{site.data.keyword.cloud}}-Konto ](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") verfügen.
2. Sie müssen über das [{{site.data.keyword.cos_short}} SDK for Node.js ](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") verfügen.
3. Sie müssen über Node 4.x+ verfügen.
4. Suchen Sie nach den Schlüsselwerten des Berechtigungsnachweises, die später für die SDK-Initialisierung verwendet werden sollen:

    * _**endpoint**_: Der öffentliche Endpunkt für Ihre Cloud Object Storage-Instanz. Der Endpunkt ist über das [{{site.data.keyword.cloud_notm}}-Dashboard ](https://cloud.ibm.com/dashboard/apps){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") verfügbar.
    * _**api-key**_: Der API-Schlüssel, der generiert wird, wenn die Serviceberechtigungsnachweise erstellt werden. Für das Erstellen und Löschen von Beispielen ist ein Schreibzugriff erforderlich.
    * _ **resource-instance-id**_: Die Ressourcen-ID für Ihre Cloud Object Storage-Instanz. Die Ressourcen-ID steht über die [{{site.data.keyword.cloud_notm}}-CLI](/docs/cli?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli) oder das [{{site.data.keyword.cloud_notm}}-Dashboard ](https://cloud.ibm.com/dashboard/apps){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") zur Verfügung.

## Schritt 1. Instanz von {{site.data.keyword.cos_short}} erstellen
{: #create-instance-cos}

1. Im [{{site.data.keyword.cloud_notm}}-Katalog](https://cloud.ibm.com/catalog/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") wählen Sie die Kategorie **Speicher** aus und klicken Sie auf {{site.data.keyword.cos_short}}. Die Seite mit der Servicekonfiguration wird geöffnet.
2. Geben Sie der Serviceinstanz entweder einen Namen oder verwenden Sie den voreingestellten Namen.
3. Wählen Sie Ihren Preistarif aus und klicken Sie auf **Erstellen**. Die Seite für die Object Storage-Instanz wird geöffnet.
4. Wählen Sie im Navigationsmenü die Option **Serviceberechtigungsnachweise** aus.
5. Klicken Sie auf der Seite für die Serviceberechtigungsnachweise auf **Neuer Berechtigungsnachweis**.
6. Stellen Sie auf der Seite 'Neuen Berechtigungsnachweis hinzufügen' sicher, dass die Rolle auf **Autor** gesetzt ist, und klicken Sie anschließend auf **Hinzufügen**. Der neue Berechtigungsnachweis wird erstellt und auf der Seite mit den Serviceberechtigungsnachweisen angezeigt.

## Schritt 2. SDK installieren
{: #install-cos}

Installieren Sie das {{site.data.keyword.cos_short}} SDK for Node.js mithilfe des Paketmanagers [npm](https://nodejs.org/){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") über die Befehlszeile: 
```
npm install ibm-cos-sdk
```
{: pre}

## Schritt 3. SDK initialisieren
{: #initialize-cos}

Nachdem Sie das SDK in Ihrer App initialisiert haben, können Sie {{site.data.keyword.cos_short}} zum Speichern von Daten verwenden. Initialisieren Sie Ihre Verbindung, indem Sie Ihre Berechtigungsnachweise angeben und eine Callback-Funktion bereitstellen, die ausgeführt werden soll, wenn alles bereit ist.

1. Laden Sie die Clientbibliothek, indem Sie die folgenden `require`-Definitionen zur Datei `server.js` hinzufügen.
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. Initialisieren Sie die Clientbibliothek, indem Sie Ihre Berechtigungsnachweise bereitstellen.
  ```js
  var config = {
    endpoint: '<endpunkt>',
    apiKeyId: '<api-schlüssel>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<instanz-id_der_ressource>',
  };
  ```
  {: codeblock}

  Wenn Sie Hilfe bei der Suche nach den Schlüsselwerten für den Berechtigungsnachweis für Ihre App benötigen, finden Sie in *Schritt 4* des Abschnitts [Vorbereitende Schritte](#prereqs-cos) entsprechende Details.
  {: tip}

3. Fügen Sie den folgenden Code zur Datei `server.js` hinzu.
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

### Daten mit Basisoperationen verwalten
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### Bucket erstellen
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

#### Objekt erstellen, hochladen und überschreiben
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

#### Objekt herunterladen
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

#### Objekt löschen
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

Informationen zu mehrteiligen Uploads, Sicherheitsfunktionen und weiteren Operationen finden Sie in der [vollständigen Dokumentation](/docs/services/cloud-object-storage/libraries?topic=cloud-object-storage-using-node-js#using-node-js).

## Schritt 4. App testen
{: #test-cos}

Ist alles richtig eingerichtet? Testen Sie es jetzt!

1. Führen Sie Ihre Anwendung aus und stellen Sie sicher, dass Sie die Initialisierung und die entsprechenden Operationen starten, wie z. B. das Erstellen eines Buckets und das Hinzufügen von Daten zum Bucket.
2. Kehren Sie zur {{site.data.keyword.cos_short}}-Serviceinstanz zurück, die Sie zuvor in Ihrem Web-Browser erstellt haben, und öffnen Sie das Service-Dashboard.
3. Wählen Sie das verwendete Bucket aus und die neu erstellten Objekte werden im Dashboard angezeigt.

Haben Sie Schwierigkeiten? Lesen Sie die [{{site.data.keyword.cos_short}}-API-Referenz](/docs/services/cloud-object-storage/api-reference?topic=cloud-object-storage-compatibility-api-about#compatibility-api-about). 

## Nächste Schritte
{: #next-cos notoc}

Gut gemacht! Sie haben Ihrer App ein Maß an sicherer Persistenz hinzugefügt. Machen Sie am besten gleich weiter, indem Sie eine der folgenden Optionen ausprobieren:

* Zeigen Sie die Quellcode für das [{{site.data.keyword.cos_short}} SDK for Node.js ](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") an.
* Sehen Sie sich den [Beispielcode für Bucket- und Objektoperationen ](https://github.com/ibm/ibm-cos-sdk-js#example-code){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") genauer an.
* Mit Starter-Kits können Sie die Funktionalität von {{site.data.keyword.cloud_notm}} optimal und schnell nutzen. Zeigen Sie die verfügbaren Starter-Kits im [Mobile-Enwicklerdashboard ](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![Symbol für externen Link](../icons/launch-glyph.svg "Symbol für externen Link") an. Laden Sie den Code herunter. Führen Sie die App aus!
* Wenn Sie mehr zu den Funktionen von {{site.data.keyword.cos_short}} und deren Verwendung erfahren möchten, [lesen Sie die Dokumentation](/docs/services/cloud-object-storage?topic=cloud-object-storage-about-ibm-cloud-object-storage#about-ibm-cloud-object-storage). 
