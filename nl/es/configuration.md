---

copyright:
  years: 2018
lastupdated: "2018-09-20"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configuración del entorno Node.js

Mediante la implementación de principios nativos en la nube, una aplicación Node.js puede moverse de un entorno a otro, desde prueba a producción, sin cambiar el código ni utilizar vías de acceso de código no probadas.

El problema surge cuando existen diferencias significativas en la forma en que se presenta la configuración, en función del entorno de desarrollo. Por ejemplo, CloudFoundry, que utiliza objetos JSON en forma de serie frente a Kubernetes que utiliza valores sin formato u objetos JSON en forma de serie. El desarrollo local, aparte de Kubernetes, tiene también diferentes consideraciones. Las credenciales se pueden presentar de forma distinta en entornos públicos o privados, que hacen todavía más difícil que las apps permanezcan sin cambios entre entornos.

Tanto si necesita añadir soporte para {{site.data.keyword.cloud}} a las aplicaciones existentes como crear apps con los Kits de iniciación, el objetivo es proporcionar portabilidad para las apps Node.js en cualquier plataforma de desarrollo.

## Adición de la configuración de {{site.data.keyword.cloud_notm}} a aplicaciones de Node.js existentes
{: #addcloud-env}

El módulo [`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env) agrega las variables de entorno de diversos proveedores de nube, como CloudFoundry y Kubernetes, por lo que la aplicación es independiente del entorno.

### Instalación del módulo `ibm-cloud-env`
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

  Si la vía de acceso del archivo de correlaciones no se especifica en `IBMCloudEnv.init ()`, el módulo intenta cargar las correlaciones de una vía de acceso predeterminada de `/server/config/mappings.json`.
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
        "searchPatterns": [
            "cloudfoundry:$.service2[@.name=='my-service2-instance-name'].credentials.username",
            "env:my-service2-credentials:$.username",
            "file:/localdev/my-service1-credentials.json:$.username"
        ]
    }
  }
  ```
  {: codeblock}

### Utilización de los valores en una app de Node.js
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
Puede filtrar las credenciales generadas por el módulo en función de los códigos y las etiquetas de servicio, tal como se muestra en el ejemplo siguiente:
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // devuelve un Json con credenciales para la etiqueta del servicio especificado
```
{: codeblock}

## Utilización del gestor de configuración de Node.js desde apps del Kit de iniciación

Las apps Node.js creadas con [Kits de iniciación](https://console.bluemix.net/developer/appservice/starter-kits/) se proporcionan automáticamente con las credenciales y la configuración necesarias para ejecutarse en muchos entornos de despliegue de nube (CF, K8s, VSI y Functions).

### Comprensión de las credenciales de servicio

La información de configuración de aplicación de servicios se almacena en el archivo `localdev-config.json` en el directorio `/server/config`. El archivo se encuentra en el directorio `.gitignore` para evitar que se almacene información confidencial en Git. La información de conexión para cualquier servicio configurado que se ejecute localmente, como el nombre de usuario, la contraseña y el nombre de host, se almacena en este archivo.

La aplicación utiliza el gestor de configuración para leer la información de conexión y configuración del entorno y este archivo. Utiliza un `mappings.json` personalizado, que se encuentra en el directorio `server/config`, para comunicar dónde se pueden encontrar las credenciales para cada servicio.

Las aplicaciones que se ejecutan localmente se pueden conectar a los servicios de {{site.data.keyword.cloud_notm}} utilizando credenciales desenlazadas que se leen desde el archivo `mappings.json`. Si necesita crear credenciales desenlazadas, puede hacerlo desde la consola web de {{site.data.keyword.cloud_notm}} o utilizando el mandato de la [CLI de CloudFoundry](https://docs.cloudfoundry.org/cf-cli/) `cf create-service-key`.

Cuando envía la aplicación a {{site.data.keyword.cloud_notm}}, estos valores ya no se utilizan. En su lugar, la aplicación se conecta automáticamente a los servicios enlazados utilizando variables de entorno.

* **Cloud Foundry**: Las credenciales de servicio se toman de la variable de entorno `VCAP_SERVICES`.

* **Kubernetes**: Las credenciales de servicio se toman de variables de entorno individuales por servicio.

* **{{site.data.keyword.cloud_notm}} Container Service**: Las credenciales de servicio se toman de VSI o de {{site.data.keyword.openwhisk}} (Openwhisk).


## Pasos siguientes
{: #next_steps notoc}

`ibm-cloud-config` soporta la búsqueda de valores utilizando tres tipos de patrón de búsqueda: `cloudfoundry`, `env` y `file`. Si desea obtener información sobre otros patrones de búsqueda soportados y ejemplos de patrón de búsqueda, consulte la sección [Uso](https://github.com/ibm-developer/ibm-cloud-env#usage).
