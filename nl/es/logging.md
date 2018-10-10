---

copyright:
  years: 2018
lastupdated: "2018-09-18"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Registro en Node.js
{: #logging_nodejs}

Los mensajes de registro son series con información contextual sobre el estado y la actividad del microservicio en el momento en que se realiza la entrada de registro. Los registros son necesarios para diagnosticar cómo y por qué fallan los servicios, y desempeñan un rol de soporte en [appmetrics](appmetrics.html) en la supervisión del estado de la aplicación.

Dada la naturaleza transitoria de los procesos en entornos de nube, los registros deben recopilarse y enviarse a otro lugar, normalmente a una ubicación centralizada para su análisis. La forma más coherente de iniciar sesión en entornos de nube es enviar entradas de registro a la salida estándar y a las secuencias de error, que deja que la infraestructura maneje el resto.

## Adición de soporte de Log4js a la app Node.js
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) es una infraestructura de registro popular para Node.js, y proporciona muchas ventajas nativas, como: 
* Registro en `stdout` o `stderr`
* Diversas opciones de adjunción
* Diseño y patrones de mensajes de registro configurables
* Utilización de niveles de registro para distintas categorías de registro

1. Para utilizar Log4js, ejecute el siguiente mandato de [npm](https://nodejs.org/) en el directorio raíz de la aplicación, que instala el paquete y lo añade al archivo `package.json`.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Para utilizarlo en la aplicación, añada las siguientes líneas de código:
  ```javascript
  var log4js = require('log4js');
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **Salida stdout**:
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

Para obtener más información sobre la personalización de los mensajes de registro con agregadores, niveles de registro y detalles de configuración, consulte la [documentación de log4js-node](https://log4js-node.github.io/log4js-node/).

## Supervisión de registros con App Service
{: #monitoring}

De forma predeterminada, las apps Node.js creadas utilizando {{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard) incluyen Log4js. La ejecución de la app de forma nativa o en un entorno de nube produce una salida como: `2018-07-26 12:40:15.121] [INFO] MyAppName-MyAppName listening on http://localhost:3000`. Puede ver la salida como se indica a continuación:
* Utilice `stdout` cuando se ejecute localmente.
* En los registros para los despliegues de [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) y [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/), a los que se accede mediante `ibmcloud app logs --recent <APP_NAME>` y `kubectl logs <deployment name>`.

En el archivo `server/server.js`, puede ver el código siguiente:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

De forma predeterminada, el nivel de registro se establece en `INFO` y la variable de entorno **LOG_LEVEL** de la aplicación la puede alterar temporalmente.
{: tip}

## Pasos siguientes
{: #next_steps notoc}

Obtenga más información sobre la visualización de los registros en cada uno de los entornos de despliegue:
* [Registros de Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Registros de Cloud Foundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} Registros y supervisión](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Utilización de un agregador de registros:
* [{{site.data.keyword.cloud_notm}} Log Analysis ](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Pila de ELK privada](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
