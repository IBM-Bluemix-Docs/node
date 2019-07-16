---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-10"

keywords: nodejs logging, view logs nodejs, add logging nodejs, log4j nodejs, stdout nodejs, nodejs log, output nodejs, nodejs logger

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Registro en Node.js
{: #logging_nodejs}

Los mensajes de registro son series con información contextual sobre el estado y la actividad del microservicio en el momento en que se realiza la entrada de registro. Los registros son necesarios para diagnosticar cómo y por qué fallan los servicios, y desempeñan un rol de soporte en [appmetrics](/docs/node?topic=nodejs-metrics) en la supervisión del estado de la aplicación.

Dada la naturaleza transitoria de los procesos en entornos de nube, los registros deben recopilarse y enviarse a otro lugar, normalmente a una ubicación centralizada para su análisis. La forma más coherente de iniciar sesión en entornos de nube es enviar entradas de registro a la salida estándar y a las secuencias de error, que deja que la infraestructura maneje el resto.

Puede utilizar [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), que es una infraestructura de registro popular para Node.js, y que proporciona muchas ventajas nativas, como: 
* Registro en `stdout` o `stderr`
* Diversas opciones de adición
* Diseño y patrones de mensajes de registro configurables
* Utilización de `Niveles de registro` para distintas categorías de registro

## Adición de soporte de Log4js a una app Node.js existente
{: #add_log4j}

1. Primero, instale `log4js` ejecutando el siguiente mandato de [npm](https://nodejs.org/en/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") en el directorio raíz de la aplicación, que instala el paquete y lo añade al archivo `package.json`.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. Para utilizarlo en la aplicación, añada las siguientes líneas de código a su app:
  ```js
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

3. Para escribir sucesos de registro en la secuencia de error estándar, puede configurar un agregador como el siguiente ejemplo:
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  Para obtener más información sobre la personalización de los mensajes de registro con agregadores, `Niveles de registro` y detalles de configuración, consulte la [documentación de log4js-node](https://log4js-node.github.io/log4js-node/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") oficial.

## Supervisión con apps de App Service
{: #monitoring}

De forma predeterminada, las apps Node.js creadas utilizando {{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") incluyen Log4js. Puede abrir el archivo `server/server.js` para ver el siguiente código de Log4js:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

De forma predeterminada, el `Nivel de registro` se establece en `OFF` (desactivado) para que se pueda utilizar de forma segura en bibliotecas, pero se puede alterar temporalmente por la variable de entorno **LOG_LEVEL** de la aplicación.
{: tip}

### Visualización de la salida de registro
{: #node-view-log-output}

VeR el siguiente ejemplo de salida de registro de la ejecución de la app de forma nativa o en un entorno de nube:
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

Puede visualizar la salida del registro utilizando los métodos siguientes:
* Para entornos locales, utilice `stdout`.
* Para despliegues [Cloud Foundry](/docs/cli/reference?topic=cloud-cli-ibmcloud_commands_apps#ibmcloud_app_logs), puede acceder a los registros ejecutando:
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* Para despliegues de [Kubernetes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), puede acceder a los registros ejecutando:
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## Pasos siguientes
{: #next_steps-logging notoc}

Para obtener más información sobre la visualización de registros en cada entorno:
* [Registros de Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/#basic-logging-in-kubernetes){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")
* [Registros de Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps?topic=cloudloganalysis-logging_cf_apps)
* [Registros y supervisión de {{site.data.keyword.openwhisk}}](/docs/openwhisk?topic=cloud-functions-logs)

Utilización de un agregador de registros:
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [Pila de ELK de {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")
