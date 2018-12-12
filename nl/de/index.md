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

# Einführung - Lernprogramm

Das folgende Lernprogramm führt Sie durch die Schritte zum Erstellen, lokalen Ausführen und Bereitstellen einer Node.js-App mithilfe von im Rahmen von {{site.data.keyword.cloud_notm}} bereitgestellten Tools. Sie können die [{{site.data.keyword.dev_cli_long}}](https://console.bluemix.net/docs/cloudnative/dev_cli.html#add-cli) in der Befehlszeile oder die webbasierte [{{site.data.keyword.cloud}}-{{site.data.keyword.dev_console}}](https://console.bluemix.net/developer/appservice/dashboard) verwenden, wie in den folgenden Schritten des Lernprogramms gezeigt. Durch die Verwendung einer dieser Methoden können Sie eine für die Produktion bereite Node.js-Anwendung generieren.

Stellen Sie sicher, dass Sie das neueste LTS-Release von Node.js verwenden.

## Node.js-App erstellen
{: #create_project}

1. Wählen Sie auf der Seite mit den [Starter-Kits](https://console.bluemix.net/developer/appservice/starter-kits) in der {{site.data.keyword.dev_console}} ein Starter-Kit aus, das in `Node.js` geschrieben ist. Sie können auch eine leere Starter-App erstellen, indem Sie auf **App erstellen** klicken und dann `Node.js` als Sprache auswählen.

    Sie müssen bei einem {{site.data.keyword.cloud_notm}}-Konto angemeldet sein, um ein Projekt erstellen zu können. Wenn Sie über kein Konto verfügen, können Sie sich [für ein kostenloses Konto registrieren](https://console.bluemix.net/registration).
    {: tip}

2. Klicken Sie auf **App erstellen**.
3. Geben Sie der App einen **Namen**. Sie haben auch die Möglichkeit, einen generischen App-Namen zu verwenden.
4. Geben Sie einen **eindeutigen Hostnamen** ein. Der Hostname wird für den Zugriff auf Ihre Anwendung verwendet, zum Beispiel: `expressjs-project.mybluemix.net`.
5. Klicken Sie auf **Erstellen**. Nachdem Ihr Projekt erstellt wurde, können Sie es mit einer Toolchain bereitstellen; Sie können aber auch mit dem Erstellen fortfahren und das Projekt über die Befehlszeile bereitstellen.
6. Wenn Sie eine Toolchain erstellen möchten, klicken Sie auf **In der Cloud bereitstellen** und wählen Sie eine der folgenden Bereitstellungsmethoden aus.
    * **Cloud Foundry-App**: Sie müssen die zugrunde liegende Infrastruktur nicht verwalten.
    * **Kubernetes-Cluster**: Sie müssen eine Gruppe von Workerknoten bereitstellen. Sie können zum Beispiel VMs verwenden, um hoch verfügbare Anwendungscontainer bereitzustellen und zu verwalten. Sie können einen Cluster erstellen oder die Bereitstellung in einem vorhandenen Cluster vornehmen.

7. Wählen Sie die gewünschten Optionen aus und klicken Sie dann auf **Erstellen**, um die Toolchain zu erstellen.

8. Wenn Sie die CLI anstelle der Toolchain verwenden möchten, laden Sie das Projekt auf Ihre lokale Maschine herunter, dekomprimieren Sie das Projekt mit `unzip` und wechseln Sie mit `cd` in das Stammverzeichnis. Nun können Sie die Voraussetzungen mit der Befehlsmethode für Ihre Plattform installieren:

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows (als Administrator ausführen):
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## Service hinzufügen
{: #add_service}

1. Kehren Sie zu Ihrem Projekt in der {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} zurück.
2. Klicken Sie auf **Service hinzufügen**, wählen Sie die Kategorie des Service aus, den Sie hinzufügen möchten, klicken Sie auf **Weiter** und wählen Sie dann Ihren Service aus. Wenn Sie beispielsweise eine NoSQL-Datenbank zu Ihrer Anwendung hinzufügen möchten, klicken Sie auf die Kategorie **Daten** und wählen Sie dann **Cloudant** aus, das einen Lite-Plan für die kostenlose Entwicklung anbietet. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} stellt den Service für Sie basierend auf dem ausgewählten Plan bereit.
Hinweis: Wenn Sie den Service, den Sie verwenden möchten, bereits bereitgestellt haben, wählen Sie die Kategorie **Vorhanden** aus.
3. Nachdem der Service bereitgestellt wurde, klicken Sie auf **Code herunterladen**, um das Projekt mit dem SDK neu zu generieren, das eine Verbindung zu Ihrem Service herstellt.

<!--
<video of creating a project and adding a service>
-->

## Apps lokal ausführen
{: #run_local}

1. Verwenden Sie den Befehl `ibmcloud dev build`, um Ihre Anwendung zu erstellen.
2. Verwenden Sie den Befehl `ibmcloud dev run`, um die Anwendung lokal auszuführen. Ihre Anwendung wird in den Docker-Containern auf Ihrem lokalen System ausgeführt. Sie können die Anwendung in einem Browser testen, indem Sie auf `http://localhost:3000` zugreifen.
3. Ein Endpunkt für die Statusprüfung ist unter `http://localhost:3000/health` verfügbar.
4. Sie können unter `http://localhost:3000/appmetrics-dash` auf das [Application Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/)-Dashboard zugreifen.

<!--
<video>
-->

## Bereitstellung in der Cloud
{: #deploy_cloud}

Mit dem Befehl `ibmcloud dev deploy` können Sie die Bereitstellung in {{site.data.keyword.cloud_notm}} als Cloud Foundry-Anwendung durchführen. 

Verwenden Sie für die Bereitstellung in IBM Container Services in {{site.data.keyword.cloud_notm}} den folgenden Befehl:
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## Nächste Schritte
{: #nextsteps}

Sehen Sie sich als Nächstes die Themen im Programmierungshandbuch zu Node.js an. Wenn Sie mit erweiterten Bereitstellungen arbeiten, können Sie auch lernen, wie Sie einen Kubernetes-Cluster erstellen und die Node.js-App in diesem Cluster bereitstellen.

### Kubernetes-Cluster einrichten
Weitere Informationen zum Einrichten eines Kubernetes-Clusters in {{site.data.keyword.cloud_notm}} finden Sie in den entsprechenden [Schritten des Lernprogramms](https://console.bluemix.net/docs/containers/cs_clusters.html#clusters).

### Node.js-Apps in einem Kubernetes-Cluster bereitstellen
Erfahren Sie, wie Sie [Node.js-Apps in einem Kubernetes-Cluster bereitstellen](../containers/cs_tutorials_apps.html).
