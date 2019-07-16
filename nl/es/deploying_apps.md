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

# Despliegue e integración de apps Swift
{: #deploy_apps-nodejs}

Puede desplegar las apps Node.js con una cadena de herramientas o utilizando la interfaz de línea de mandatos. Una cadena de herramientas es un conjunto de integraciones de herramientas. La interfaz de línea de mandatos es una forma simple de desplegar sus apps e instancias de servicio.

Para obtener más información, consulte el apartado [Despliegue de apps](/docs/apps?topic=creating-apps-deploying-apps).

## Desarrollo de apps Node.js sin servidor
{: #serverless-nodejs}

Para facilitar el desarrollo sin servidor, puede utilizar la oferta de FaaS (Functions as a Service) de IBM, {{site.data.keyword.openwhisk}}. {{site.data.keyword.openwhisk_short}} permite que la lógica de la aplicación se ejecute en respuesta a sucesos o a invocaciones directas desde aplicaciones apps web o móviles a través de HTTP sin suministrar ni gestionar servidores.

{{site.data.keyword.openwhisk_short}} realiza la administración de sistema como el escalado automático, la gestión de disponibilidad y el mantenimiento, lo que permite a los desarrolladores seguir centrados en escribir la lógica de la aplicación.

Para obtener más información, consulte [Desarrollo de apps sin servidor](/docs/apps/deploying?topic=creating-apps-serverless).

## Integración de los servicios de fondo con un SDK generado
{: #backend_gensdk}

El plugin SDK Generator de {{site.data.keyword.IBM_notm}} integra fácilmente servicios de fondo en la app con un SDK generado. Cuando se produzca un cambio en una API REST, puede volver a generar el SDK y sustituir el antiguo por una actualización de SDK. También puede integrar la CLI en un conducto de DevOps y garantizar que el SDK sea siempre coherente con la especificación de la API cada vez que se cree la app.

Para obtener más información, consulte [Integración de los servicios de fondo con un SDK generado](/docs/swift/backend?topic=swift-sdkgen-cli).

## Despliegue en un clúster de Kubernetes
{: #deploy_kube}

Puede aprender a utilizar el servicio {{site.data.keyword.cloud_notm}} Kubernetes para desplegar una app Node.js contenerizada que utilice Watson Tone Analyzer. En el caso de ejemplo proporcionado, una empresa PR ficticia utiliza el servicio {{site.data.keyword.cloud_notm}} para analizar sus notas de prensa y recibir comentarios sobre el tono de sus mensajes. Para obtener más información, consulte la guía de aprendizaje [Despliegue de clústeres de Kubernetes](/docs/containers?topic=containers-cs_apps_tutorial).

## Despliegue en Cloud Foundry
{: #node-deploy-cf}

Esta opción despliega la app nativa de la nube sin necesidad de gestionar la infraestructura subyacente.

Si tiene intención de desplegar la app en [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry?topic=cloud-foundry-about), debe [preparar la cuenta de {{site.data.keyword.cloud_notm}}](/docs/cloud-foundry?topic=cloud-foundry-prepare).

Si la cuenta tiene acceso a {{site.data.keyword.cfee_full_notm}}, puede seleccionar el tipo de desplegador de **[nube pública](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** o de **[entorno de empresa](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)**, que puede utilizar para crear y gestionar entornos aislados para alojar aplicaciones de Cloud Foundry exclusivamente para su empresa.

## Despliegue en un servidor virtual
{: #virtual_deploy}

Un servicio virtual ofrece un nivel más alto de transparencia, predictibilidad y automatización para todos los tipos de carga de trabajo. Terraform se utiliza para crear, cambiar y versionar su infraestructura de forma segura y eficiente.

  El despliegue de VSI está disponible para algunos kits de inicio. Para utilizar esta característica, vaya al
[panel de control de {{site.data.keyword.cloud_notm}}](https://{DomainName}) y pulse **Crear una app** en el mosaico **Apps**.
  {: note} 

Para obtener más información, consulte [Despliegue en un servidor virtual](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server).
