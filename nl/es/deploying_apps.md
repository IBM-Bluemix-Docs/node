---

copyright:
  years: 2018
lastupdated: "2018-08-15"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Despliegue e integración de apps Swift
{: #deploy_apps}

Puede desplegar las apps Node.js con una cadena de herramientas o utilizando la interfaz de línea de mandatos. Una cadena de herramientas es un conjunto de integraciones de herramientas. La interfaz de línea de mandatos es una forma simple de desplegar sus apps e instancias de servicio.

Para obtener más información, consulte el apartado [Despliegue de apps](../apps/dep-app-tool.html).

## Desarrollo de apps Node.js sin servidor
{: #serverless}

Para facilitar el desarrollo sin servidor, puede utilizar la oferta de FaaS (Functions as a Service) de IBM, {{site.data.keyword.openwhisk}}. {{site.data.keyword.openwhisk_short}} permite que la lógica de la aplicación se ejecute en respuesta a sucesos o a invocaciones directas desde aplicaciones apps web o móviles a través de HTTP sin suministrar ni gestionar servidores.

{{site.data.keyword.openwhisk_short}} realiza la administración de sistema como el escalado automático, la gestión de disponibilidad y el mantenimiento, lo que permite a los desarrolladores seguir centrados en escribir la lógica de la aplicación.

Para obtener más información, consulte [Desarrollo de apps sin servidor](../apps/deploying/functions.html).

## Integración de los servicios de fondo con un SDK generado
{: #backend_gensdk}

El plug-in de generador de SDK de {{site.data.keyword.IBM_notm}} integra fácilmente servicios de fondo en la app con un SDK generado. Cuando se produzca un cambio en una API REST, puede volver a generar el SDK y sustituir el antiguo por una actualización de SDK. También puede integrar la CLI en un conducto de DevOps y garantizar que el SDK sea siempre coherente con la especificación de la API cada vez que se cree la app.

Para obtener más información, consulte [Integración de los servicios de fondo con un SDK generado](../apps/deploying/api_management.html).

## Despliegue en un clúster de Kubernetes
{: #deploy_kube}

Puede aprender a utilizar el servicio {{site.data.keyword.cloud_notm}} Kubernetes para desplegar una app Node.js contenerizada que utilice Watson Tone Analyzer. En el caso de ejemplo proporcionado, una empresa PR ficticia utiliza el servicio {{site.data.keyword.cloud_notm}} para analizar sus notas de prensa y recibir comentarios sobre el tono de sus mensajes. Para obtener más información, consulte la guía de aprendizaje [Despliegue de apps en clústeres](../containers/cs_tutorials_apps.html).

## Despliegue en un servidor virtual
{: #virtual_deploy}

Un servicio virtual ofrece un nivel más alto de transparencia, predictibilidad y automatización para todos los tipos de carga de trabajo. Terraform se utiliza para crear, cambiar y versionar su infraestructura de forma segura y eficiente.

Para obtener más información, consulte [Despliegue en un servidor virtual](../apps/vsi-deploy.html).
