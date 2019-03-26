---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configuración del rastreo de extremo a extremo
{: #e2e-tracing}

La guía de aprendizaje siguiente se centra en Zipkin y el uso del módulo [appmetrics-zipkin](https://github.com/RuntimeTools/appmetrics-zipkin) para el rastreo de aplicaciones Node.js. Puede obtener más información sobre Zipkin en el [anuncio de appmetrics-zipkin](https://developer.ibm.com/node/2017/10/26/add-zipkin-open-tracing-support-node-js-application-one-line-code/) original. 

En los pasos siguientes, se utilizan dos pequeñas aplicaciones (una frontal y una de fondo) para rastrear entre dos puntos finales utilizando el módulo `appmetrics-zipkin`. Puede empezar de cero o aplicar los principios que se describen aquí en las aplicaciones Node.js existentes. 

## Paso 1. Instalación y habilitación del módulo appmetrics-zipkin
{: #install-zipkin}

En la misma ubicación que el archivo `package.json` de la aplicación Node.js, escriba el siguiente mandato [npm](https://nodejs.org/) para añadir el módulo `appmetrics-zipkin` a la lista de dependencias:
```
npm install --save appmetrics-zipkin
```
{: codeblock}

Añada la siguiente línea al código de servidor de Node.js, **ANTES** de cualquier otra sentencia `require` de appmetrics:
```js
var appzip = require('appmetrics-zipkin');
```
{: codeblock}

La siguiente sentencia hace que el rastreo se añada a las llamadas de método `HTTP` y `request` y los datos que se van a enviar al servidor Zipkin. De forma predeterminada, el módulo busca el servidor Zipkin en `localhost` y el puerto `9411`. Puede cambiar el nombre de host y el puerto utilizando la sintaxis siguiente:
```js
var appzip = require('appmetrics-zipkin')({
 host: "my.host.here",
 port: 12345, // changeme
 serviceName:'my-service-name'
});
```
{: codeblock}

Envíe una solicitud como lo haría normalmente. Por ejemplo:
```
http.request(options, callback).end();
```
{: codeblock} 

## Paso 2. Configuración de un servidor Zipkin
{: #setup-zipkin-server}

Ahora necesita un lugar al que enviar sus datos, concretamente los seguimientos, que están formados por tramos. Antes de realizar el despliegue en cualquier nube, puede probar la configuración de rastreo e2e configurando un servidor Zipkin localmente o en un contenedor. 

### Configuración local de Zipkin
{: #local-setup-zipkin}

Zipkin se proporciona en un solo archivo `jar` para que pueda descargarlo y ejecutarlo utilizando los siguientes mandatos en el sistema en el que desea que Zipkin esté disponible:

1. Descarga de Zipkin:
  ```
  wget zipkin.jar 'https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=1.31.3&c=exec'
  ```
  {: codeblock}

2. Inicio de Zipkin:
  ```
  java -jar zipkin.jar
  ```
  {: codeblock}

  El mandato `wget` descarga el archivo Zipkin y el mandato `java -jar` inicia el servidor Zipkin. También puede descargar Zipkin desde otras ubicaciones, pero es importante que utilice la versión 1.x para esta guía de aprendizaje para que el formato de rastreo coincida con lo que espera el servidor Zipkin.

  Si la salida de este mandato es demasiado detallada o si desea ejecutar Zipkin en segundo plano, puede añadir `-q -O` al mandato `wget` y `/dev/null 2>&1 &` para Zipkin. En este nivel, está descargando el archivo `.jar` de Zipkin y ejecutando el método principal para iniciar el servidor Zipkin.

### Configuración de Zipkin en un contenedor Docker
{: #setup-docker-zipkin}

Si lo desea, puede ejecutar un servidor Zipkin en un contenedor Docker ejecutando el siguiente mandato:
```
docker run -d -p 9411:9411 openzipkin/zipkin
```
{: codeblock}

El módulo `openzipkin/zipkin` se descarga, se instala y se inicia en el puerto `9411` utilizando un mandato simple.

### Acceso a la consola Zipkin
{: #zipkin-console}

La imagen siguiente muestra el servidor Zipkin que se ejecuta en `localhost` en el puerto `9411`:

![ZipkinNoData](images/ZipkinNoData.png)

Puede pulsar **Find traces** y modificar las opciones de búsqueda para mostrar de forma selectiva sólo los rastreos en un determinado periodo de tiempo. También puede filtrar para que se muestren los rastreos que implican nombres de servicios determinados. Los nombres de servicio se especifican cuando se instrumenta el código, en el caso de ejemplo se utiliza "getter" y "pusher".

## Paso 3. Prueba de un caso de ejemplo
{: #example-scenario-tracing}

Si sigue la [documentación del proyecto GitHub](https://github.com/ibm-developer/nodejs-zipkin-tracing), puede terminar con la siguiente aplicación de ejemplo. Es un proceso simple que implica el rastreo de una solicitud y respuesta entre dos puntos finales. Las imágenes siguientes muestran el servidor Zipkin con los datos de rastreo recopilados en la pantalla. El punto clave a recordar es la inclusión de `require ('appmetrics-zipkin')` y, opcionalmente, el código de configuración del servidor Zipkin. El siguiente caso de ejemplo muestra cómo puede añadir rápidamente el rastreo de Zipkin a las aplicaciones Node.js existentes.

### Visión general del caso de ejemplo de rastreo
{: #tracing-scenario}

* Un **frontal**, que se conoce como pusher, solicita al usuario la longitud de una serie que se va a crear y convertir en minúsculas. Cuanto mayor sea el número, mayor será la serie y más tiempo se tardará en manejar la solicitud. Disponible en el puerto `3000`.
* Un **programa de fondo**, conocido como getter, maneja la solicitud y está disponible en el puerto `3001`.
* Un **servidor Zipkin** se ejecuta localmente o en Kubernetes donde puede ver los datos de rastreo.

### App frontal (pusher)
{: #tracing-pusher}

El servicio de app frontal (pusher) envía la solicitud (nuestro frontal sencillo): ![frontend_app](images/frontend_app.png)

### App de fondo (getter)
{: #tracing-getter}

La app de fondo (getter) recibe la solicitud, que está a la escucha en un puerto distinto: ![backend_app](images/Backend.png)

### Envío de una solicitud desde el pusher al getter
{: #tracing-request}

Envíe una solicitud del pusher al getter: ![500please](images/500Please.png)

### Visualización de rastreos con la interfaz de usuario web de Zipkin
{: #tracing-viewing}

Los datos de rastreo enviados a Zipkin se pueden visualizar con la IU web de Zipkin en `localhost:9411`. Puede ver que el **getter** recibe la entrada de usuario (el usuario desea enviar un mensaje largo de 500 caracteres al getter, utilizando el servicio pusher):
![Getter500msg](images/Getter500Msg.png)

Se muestran los detalles de la solicitud de usuario. Observe el “500”, que es el parámetro que se proporciona para la solicitud del usuario. Desean generar una serie de 500 caracteres. Puede ver exactamente lo que ha solicitado el usuario y cuánto tiempo ha tardado en manejar esta solicitud. El contenido de la solicitud (carga útil), devuelto desde el servidor, no es visible. 

Estamos preocupados por los tiempos de respuesta y los parámetros para poder determinar qué usuarios están solicitando cuándo experimentan tiempos de respuesta lentos: ![GetterGet](images/GetterGet.png)

### Identificación de la solicitud lenta
{: #tracing-slowreq}

Este es el aspecto que tendría una solicitud lenta. El siguiente usuario está solicitando la conversión de 5.000.000 de caracteres de mayúsculas a minúsculas (como lo hace). Es algo que obviamente lleva más tiempo: ![SlowRequest](images/SlowRequest.png)

Al hacer clic en este tramo, se va a la siguiente salida. De nuevo, puede ver la costosa solicitud que ha consumido mucho más tiempo. Un caso más realista podría consistir en muchos microservicios Node.js recibiendo continuamente todo tipo de solicitudes en distintos puntos finales. Al tener una vista de alto nivel de los puntos finales, puede determinar rápidamente qué servicios están respondiendo lentamente y qué están solicitando exactamente los usuarios: ![TookaWhile](images/TookAWhile.png)

Con este ejemplo, ahora tiene la siguiente situación:

* El pusher envía un mensaje al getter (un tramo).
* El getter devuelve una respuesta (un tramo).
* El rastreo completo, que consta de dos tramos, es visible en el servidor de Zipkin que se despliega localmente.

A medida que las aplicaciones se vuelven más complejas y los servicios se vuelven más populares, la necesidad de disponer de este tipo de rastreo es obvia. El rastreo a alto nivel proporciona valor a los desarrolladores para que los problemas se puedan identificar y priorizar de forma rápida y eficaz. Existen muchas alternativas disponibles, pero nuestro enfoque es simplificarlo tanto como sea posible y actuar de forma totalmente abierta.

La guía de aprendizaje finaliza aquí para los despliegues sin Kubernetes. Consulte la siguiente sección si desea rastrear las apps Node.js que se ejecutan en Kubernetes.

## Pasos siguientes
{: #next-steps-tracing}

* Aprenda a crear aplicaciones Node.js de Cloud nativas con la ayuda del proyecto de la comunidad [CloudNativeJS](https://www.cloudnativejs.io/) que proporciona activos y herramientas para ayudarle a desplegarlas en nubes basadas en Docker y Kubernetes.

* Si está listo para añadir el rastreo a las aplicaciones Node.js que se ejecutan en Kubernetes, consulte [Rastreo de las aplicaciones Node.js que utilizan Kubernetes](https://developer.ibm.com/node/tutorial-end-end-tracing-node-js-applications/#appservice).

