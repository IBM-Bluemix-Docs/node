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

# Guía de aprendizaje de iniciación

En la siguiente guía de aprendizaje se indican los pasos para crear, ejecutar localmente y desplegar una app Node.js utilizando las herramientas proporcionadas por {{site.data.keyword.cloud_notm}}. Puede utilizar la [{{site.data.keyword.dev_cli_long}}](/docs/cli/index.html#ibmcloud-cli) en la línea de mandatos o [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard) basado en web como se muestran en los siguientes pasos de la guía de aprendizaje. Al utilizar cualquiera de estos métodos, puede generar una aplicación Node.js lista para producción en tan solo unos minutos.

Asegúrese de utilizar el release más reciente de Node.js LTS.

## Creación de una app Node.js
{: #create_project}

1. En la página [Kits de inicio](https://cloud.ibm.com/developer/appservice/starter-kits) de {{site.data.keyword.dev_console}}, seleccione un Kit de inicio escrito en `Node.js`. También puede crear una app de inicio en blanco pulsando **Crear app** y seleccionando `Node.js` como lenguaje.

    Debe haber iniciado sesión en una cuenta de {{site.data.keyword.cloud_notm}} para crear una app. Si no tiene una cuenta, puede [registrarse para una cuenta gratuita](https://cloud.ibm.com/registration).
    {: tip}

2. Pulse **Crear app**.
3. Dé un **Nombre** a su app. Se proporciona un nombre de app genérico si desea utilizarlo.
4. Especifique un **nombre de host exclusivo**. El nombre de host se utiliza para acceder a la aplicación, por ejemplo: `expressjs-project.mybluemix.net`.
5. Pulse **Crear**. Una vez que se haya creado el proyecto, puede desplegarlo utilizando una cadena de herramientas o puede seguir compilando y desplegando el proyecto desde la línea de mandatos.
6. Para crear una cadena de herramientas de despliegue, pulse **Desplegar en la nube**. Configure el método de despliegue de acuerdo con las instrucciones correspondientes al método que elija:
  * **Desplegar en [Kubernetes](/docs/apps/deploying/containers.html#containers-kube)**. Esta opción crea un clúster de hosts, denominado nodos trabajadores, para desplegar y gestionar contenedores de aplicaciones de alta disponibilidad. Puede crear un clúster o desplegar en un clúster existente.
  * **Desplegar en Cloud Foundry**. Esta opción despliega la app nativa de la nube sin necesidad de gestionar la infraestructura subyacente. Si la cuenta tiene acceso a {{site.data.keyword.cfee_full_notm}}, puede seleccionar el tipo de desplegador de **[nube pública](/docs/cloud-foundry-public/about-cf.html#about-cf)** o de **[entorno de empresa](/docs/cloud-foundry-public/cfee.html#cfee)**, que puede utilizar para crear y gestionar entornos aislados para alojar aplicaciones de Cloud Foundry exclusivamente para su empresa.
  * **Desplegar en un [servidor virtual](/docs/apps/vsi-deploy.html#vsi-deploy)**. Esta opción proporciona una instancia de servidor virtual, carga una imagen que incluye la app, crea una cadena de herramientas DevOps e inicia automáticamente el primer ciclo de despliegue.

7. Finalice las opciones y, a continuación, pulse **Crear** para crear la cadena de herramientas.

8. Si opta por continuar con la CLI en lugar de la cadena de herramientas, descargue el proyecto en la máquina local, haga `unzip` y `cd` en el directorio raíz. Ahora puede instalar los requisitos previos utilizando el método de mandato de las plataformas:

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows (debe ejecutarse como Administrador):
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## Adición de un servicio
{: #add_service}

1. Vuelva a su proyecto en {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}.
2. Pulse **Añadir servicio**, seleccione la categoría del servicio que desea añadir, pulse **Siguiente** y, a continuación, elija el servicio. Por ejemplo, para añadir una base de datos NoSQL a la aplicación, pulse en la categoría **Datos** y, a continuación, seleccione **Cloudant**, que ofrece un plan lite para el desarrollo gratuito. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} suministra el servicio por usted de acuerdo con el plan seleccionado.
Nota: Si anteriormente ha suministrado el servicio que tiene previsto utilizar, elija la categoría **Existente**.
3. Cuando se haya suministrado el servicio, pulse **Descargar código** para volver a generar el proyecto con el SDK que se conecta al servicio.

<!--
<video of creating a project and adding a service>
-->

## Ejecución de apps localmente
{: #run_local}

1. Utilice el mandato `ibmcloud dev build` para crear la aplicación.
2. Utilice el mandato `ibmcloud dev run` para ejecutar la aplicación localmente. La aplicación se ejecuta en los contenedores de Docker en el sistema local. Puede probar la aplicación en un navegador accediendo a `http://localhost:3000`.
3. Un punto final de comprobación de estado está disponible en `http://localhost:3000/health`.
4. Puede acceder al panel de control de [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/) en `http://localhost:3000/appmetrics-dash`.

<!--
<video>
-->

## Despliegue en la nube
{: #deploy_cloud}

Utilice el mandato `ibmcloud dev deploy` para desplegar en {{site.data.keyword.cloud_notm}} como una aplicación de Cloud Foundry. 

Para desplegar en IBM Container Services en {{site.data.keyword.cloud_notm}}, utilice el siguiente mandato:
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## Pasos siguientes
{: #nextsteps}

Siga consultando los temas de la guía de programación de Node.js o, para despliegues más avanzados, puede aprender a crear un clúster de Kubernetes y desplegar la app Node.js en él.

### Configurar un clúster de Kubernetes
Para obtener más información sobre la configuración de un clúster de Kubernetes en {{site.data.keyword.cloud_notm}}, consulte los [pasos de la guía de aprendizaje](/docs/containers/cs_clusters.html#clusters).

### Desplegar apps Node.js en un clúster de Kubernetes
Obtenga más información sobre cómo [desplegar apps Node.js en un clúster Kubernetes](/docs/containers/cs_tutorials_apps.html#cs_apps_tutorial).
