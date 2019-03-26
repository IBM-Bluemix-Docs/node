---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-18"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Einführung - Lernprogramm

Das folgende Lernprogramm führt Sie durch die Schritte zum Erstellen, lokalen Ausführen und Bereitstellen einer Node.js-App mithilfe von im Rahmen von {{site.data.keyword.cloud_notm}} bereitgestellten Tools. Sie können die [{{site.data.keyword.dev_cli_long}}](/docs/cli/index.html#ibmcloud-cli) über die Befehlszeile oder die webbasierte [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard) verwenden, wie in den folgenden Schritten des Lernprogramms beschrieben. Durch die Verwendung einer dieser Methoden können Sie eine für die Produktion bereite Node.js-Anwendung generieren.

Stellen Sie sicher, dass Sie das neueste LTS-Release von Node.js verwenden.

## Node.js-App erstellen
{: #create_project}

1. Wählen Sie auf der Seite mit den [Starter-Kits](https://cloud.ibm.com/developer/appservice/starter-kits) in der {{site.data.keyword.dev_console}} ein Starter-Kit aus, das in `Node.js` geschrieben ist. Sie können auch eine leere Starter-App erstellen, indem Sie auf **App erstellen** klicken und dann `Node.js` als Sprache auswählen.

    Sie müssen bei einem {{site.data.keyword.cloud_notm}}-Konto angemeldet sein, um eine App zu erstellen. Wenn Sie über kein Konto verfügen, können Sie sich [für ein kostenloses Konto registrieren](https://cloud.ibm.com/registration).
    {: tip}

2. Klicken Sie auf **App erstellen**.
3. Geben Sie der App einen **Namen**. Sie haben auch die Möglichkeit, einen generischen App-Namen zu verwenden.
4. Geben Sie einen **eindeutigen Hostnamen** ein. Der Hostname wird für den Zugriff auf Ihre Anwendung verwendet, zum Beispiel: `expressjs-project.mybluemix.net`.
5. Klicken Sie auf **Erstellen**. Nachdem Ihr Projekt erstellt wurde, können Sie es mit einer Toolchain bereitstellen; Sie können aber auch mit dem Erstellen fortfahren und das Projekt über die Befehlszeile bereitstellen.
6. Klicken Sie zum Erstellen einer Bereitstellungs-Toolchain im Dashboard auf **In Cloud bereitstellen**. Richten Sie die Bereitstellungsmethode ein, indem Sie die Anweisungen für die von Ihnen ausgewählte Methode ausführen: 
  * **Bereitstellung in [Kubernetes](/docs/apps/deploying/containers.html#containers-kube)**. Mit dieser Option wird ein Cluster mit Hosts erstellen, die als Workerknoten bezeichnet werden, um hoch verfügbare Anwendungscontainer bereitzustellen und zu verwalten. Sie können einen Cluster erstellen oder die Bereitstellung in einem vorhandenen Cluster vornehmen.
  * **Bereitstellung in Cloud Foundry**. Mit dieser Option wird die cloudnative App bereitgestellt, ohne dass Sie die zugrunde liegende Infrastruktur verwalten müssen. Wenn Ihr Konto über Zugriff auf {{site.data.keyword.cfee_full_notm}} verfügt, können Sie als Bereitstellertyp entweder **[Public Cloud](/docs/cloud-foundry-public/about-cf.html#about-cf)** oder **[Enterprise Environment](/docs/cloud-foundry-public/cfee.html#cfee)** auswählen, das zum Erstellen und Verwalten isolierter Umgebungen für das exklusive Hosting von Cloud Foundry-Anwendungen für Ihr Unternehmen verwendet werden kann. 
  * **Bereitstellung auf einem [virtuellen Server](/docs/apps/vsi-deploy.html#vsi-deploy)**. Mit dieser Option wird eine virtuelle Serverinstanz eingerichtet, ein Image mit Ihrer App geladen, eine DevOps-Toolchain erstellt und der erste Bereitstellungszyklus initiiert. 

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
Weitere Informationen zum Einrichten eines Kubernetes-Clusters in {{site.data.keyword.cloud_notm}} finden Sie in den entsprechenden [Schritten des Lernprogramms](/docs/containers/cs_clusters.html#clusters).

### Node.js-Apps in einem Kubernetes-Cluster bereitstellen
Erfahren Sie, wie Sie [Node.js-Apps in einem Kubernetes-Cluster bereitstellen](/docs/containers/cs_tutorials_apps.html#cs_apps_tutorial).
