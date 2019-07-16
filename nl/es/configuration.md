---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-13"

keywords: configure node env, node environment, node credentials, ibm-cloud-env node

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Configuración del entorno Node.js
{: #configure-nodejs}

Mediante la implementación de principios nativos en la nube, una aplicación Node.js puede moverse de un entorno a otro, desde prueba a producción, sin cambiar el código ni utilizar vías de acceso de código no probadas.

El problema surge cuando existen diferencias significativas en la forma en que se presenta la configuración, en función del entorno de desarrollo. Por ejemplo, Cloud Foundry, que utiliza objetos JSON en forma de serie frente a Kubernetes que utiliza valores sin formato u objetos JSON en forma de serie. El desarrollo local, aparte de Kubernetes, tiene también diferentes consideraciones. Las credenciales se pueden presentar de forma distinta en entornos públicos o privados, que hacen todavía más difícil que las apps permanezcan sin cambios entre entornos.

Tanto si necesita añadir soporte para {{site.data.keyword.cloud}} a las aplicaciones existentes como crear apps con los Kits de inicio, el objetivo es proporcionar portabilidad para las apps Node.js en cualquier plataforma de desarrollo.

## Adición de la configuración de {{site.data.keyword.cloud_notm}} a aplicaciones de Node.js existentes
{: #addcloud-env-nodejs}

El módulo [`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") agrega variables de entorno procedentes de diversos proveedores de nube, como CloudFoundry y Kubernetes, para que la aplicación no dependa del entorno.

### Instalación del módulo `ibm-cloud-env`
{: #install-module-nodejs}

1. Instale el módulo `ibm-cloud-env` con el siguiente mandato:
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. Inicialice el módulo en el código haciendo referencia a `mappings.json` de este modo:
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  Si la vía de acceso del archivo de correlaciones no se especifica en `IBMCloudEnv.init()`, el módulo intenta cargar las correlaciones de una vía de acceso predeterminada de `/server/config/mappings.json`.
  {: tip}

  Archivo `mappings.json` de ejemplo:
  ```json
  {
    "service1-credentials": {
        "searchPatterns": [
            "cloudfoundry:my-service1-instance-name",
            "env:my-service1-credentials",
            "file:/localdev/my-service1-credentials.json"
        ]
    },
    "service2-username": {
        "searchPatterns":[
            "cloudfoundry:$.service2[@.name=='my-service2-instance-name'].credentials.username",
            "env:my-service2-credentials:$.username",
            "file:/localdev/my-service1-credentials.json:$.username"
        ]
    }
  }
  ```
  {: codeblock}

### Recuperación de las credenciales de servicio
{: #nodejs-get-creds}

Recupere los valores de la aplicación utilizando los mandatos siguientes.

1. Recuperar la variable `service1credentials`:
  ```js
  // esto será un diccionario
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. Recuperar la variable `service2username`:
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // esto será una serie
  ```
  {: codeblock}

Ahora la aplicación se puede implementar en cualquier entorno de ejecución abstrayendo las diferencias que se introducen desde distintos proveedores de cálculo de nube.

### Filtrado de los valores de etiquetas y códigos
{: #filter-values-nodejs}

Puede filtrar las credenciales generadas por el módulo en función de los códigos y las etiquetas de servicio, tal como se muestra en el ejemplo siguiente:
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // devuelve un Json con credenciales para la etiqueta del servicio especificado
```
{: codeblock}

## Utilización del gestor de configuración de Node.js desde apps del Kit de inicio
{: #nodejs-config-skit}

Las apps Node.js que se crean con [kits de inicio](https://cloud.ibm.com/developer/appservice/starter-kits){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") incluyen de forma automática credenciales y configuraciones necesarias para su ejecución en muchos destinos de despliegue en la nube, como
[Kubernetes](/docs/containers?topic=containers-getting-started), [Cloud Foundry](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf), [{{site.data.keyword.cfee_full_notm}}](/docs/cloud-foundry?topic=cloud-foundry-about), [un servidor virtual (VSI)](/docs/vsi?topic=virtual-servers-getting-started-tutorial) o
[{{site.data.keyword.openwhisk_short}}](/docs/openwhisk?topic=cloud-functions-getting_started).

  El despliegue de VSI está disponible para algunos kits de inicio. Para utilizar esta característica, vaya al
[panel de control de {{site.data.keyword.cloud_notm}}](https://{DomainName}) y pulse **Crear una app** en el mosaico **Apps**.
  {: note} 

### Comprensión de las credenciales de servicio
{: #credentials-nodejs}

La información de configuración de aplicación de servicios se almacena en el archivo `localdev-config.json` en el directorio `/server/config`. El archivo se encuentra en el directorio `.gitignore` para evitar que se almacene información confidencial en Git. La información de conexión para cualquier servicio configurado que se ejecute localmente, como el nombre de usuario, la contraseña y el nombre de host, se almacena en este archivo.

La aplicación utiliza el gestor de configuración para leer la información de conexión y configuración del entorno y este archivo. Utiliza un `mappings.json` personalizado, que se encuentra en el directorio `server/config`, para comunicar dónde se pueden encontrar las credenciales para cada servicio.

Las aplicaciones que se ejecutan localmente se pueden conectar a los servicios de {{site.data.keyword.cloud_notm}} utilizando credenciales desenlazadas que se leen desde el archivo `mappings.json`. Si necesita crear credenciales desenlazadas, puede hacerlo desde la consola web de {{site.data.keyword.cloud_notm}} o mediante el mandato `cf create-service-key` de la [CLI de CloudFoundry](https://docs.cloudfoundry.org/cf-cli/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").

Cuando envía la aplicación a {{site.data.keyword.cloud_notm}}, estos valores ya no se utilizan. En su lugar, la aplicación se conecta automáticamente a los servicios enlazados utilizando variables de entorno.

* **Cloud Foundry**: Las credenciales de servicio se toman de la variable de entorno `VCAP_SERVICES`. Para Cloud Foundry Enterprise Edition, consulte esta [Guía de aprendizaje de iniciación](/docs/cloud-foundry?topic=cloud-foundry-getting-started#getting-started) para obtener más información.

* **Kubernetes**: Las credenciales de servicio se toman de variables de entorno individuales por servicio.

* **{{site.data.keyword.cloud_notm}} Container Service**: Las credenciales de servicio se toman de VSI o de {{site.data.keyword.openwhisk}} (Openwhisk).

## Pasos siguientes
{: #next_steps-config notoc}

`ibm-cloud-config` soporta la búsqueda de valores utilizando tres tipos de patrón de búsqueda: `cloudfoundry`, `env` y `file`. Si desea consultar otros patrones de búsqueda admitidos y ejemplos de patrones de búsqueda, consulte la sección [Uso](https://github.com/ibm-developer/ibm-cloud-env#usage){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
