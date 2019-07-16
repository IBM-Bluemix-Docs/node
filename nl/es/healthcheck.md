---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-05"

keywords: healthcheck node, add healthcheck node, healthcheck endpoint nodes, readiness node, liveness node, endpoint node, probes node, health check node

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilización de una comprobación de estado en las apps Node.js
{: #node-healthcheck}

Las comprobaciones de estado proporcionan un mecanismo simple para determinar si una aplicación del lado del servidor se está comportando correctamente. Los entornos de nube, como [Kubernetes](https://www.ibm.com/cloud/container-service){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") y [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), se pueden configurar de modo que sondeen periódicamente el estado de los puntos finales para determinar si una instancia del servicio está lista para aceptar tráfico.

## Visión general de la comprobación de estado
{: #node-healthcheck-overview}

Las comprobaciones de estado proporcionan un mecanismo simple para determinar si una aplicación del lado del servidor se está comportando correctamente. Normalmente se consumen a través de HTTP y utilizan códigos de retorno estándares para indicar el estado UP (activo) o DOWN(inactivo). El valor de retorno de una comprobación de estado es variable, pero típicamente es una respuesta JSON mínima, como `{"status": "UP"}`.

Kubernetes tiene una noción matizada del estado del proceso. Define dos análisis:

- Se utiliza una prueba de _**preparación**_ para indicar si el proceso puede manejar solicitudes (es direccionable).

  Kubernetes no direcciona el trabajo a un contenedor con una prueba de actividad anómala. Una prueba de actividad puede fallar si un servicio no ha terminado de inicializarse, o si está ocupado, sobrecargado o no puede procesar las solicitudes.

- Una prueba de _**actividad**_ se utiliza para indicar si el proceso debe reiniciarse.

  Kubernetes detiene y reinicia un contenedor con una prueba de actividad ( `liveness`) anómala. Utilice una prueba de actividad para limpiar procesos en un estado no recuperable, por ejemplo, si se ha agotado la memoria o si el contenedor no se ha detenido correctamente después de que un proceso interno haya fallado.

A modo de comparación, Cloud Foundry utiliza un punto final de estado. Si la comprobación falla, el proceso se reinicia, pero si se realiza correctamente, las solicitudes se direccionan a la misma. En este entorno, el punto final se realiza correctamente cuando el proceso está activo. Se ha configurado un retraso inicial para posponer la comprobación de estado hasta que el servicio haya finalizado la inicialización para evitar ciclos de reinicio.

La tabla siguiente proporciona una orientación sobre las respuestas que los puntos finales de estado singulares, de actividad y de preparación van a proporcionar.

| Estado    | Preparación                   | Actividad                   | Estado                    |
|----------|-----------------------------|----------------------------|---------------------------|
|          | No es correcta y no se carga       | No es correcto y provoca un reinicio      | No es correcto y provoca un reinicio     |
| Iniciando | 503 - No disponible           | 200 - Correcto                   | Utilizar retraso para evitar la prueba   |
| Activo       | 200 - Correcto                    | 200 - Correcto                   | 200 - Correcto                  |
| Deteniendo | 503 - No disponible           | 200 - Correcto                   | 503 - No disponible         |
| Inactivo     | 503 - No disponible           | 503 - No disponible          | 503 - No disponible         |
| Con errores  | 500 - Error del servidor          | 500 - Error del servidor         | 500 - Error del servidor        |
{: caption="Tabla 1. Códigos de estado HTTP." caption-side="bottom"}

## Adición de una comprobación de estado a una app Node.js existente
{: #add-healthcheck-existing}

Añada una comprobación de actividad y estado mínima a una aplicación existente mediante la introducción de una nueva ruta, como se muestra en el ejemplo siguiente:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

Compruebe el estado de la app con un navegador accediendo al punto final `/health`.

## Acceso a la comprobación de estado desde las apps del kit de inicio de Node.js
{: #node-healthcheck-starterkit}

De forma predeterminada, al generar una app Node.js utilizando un kit de inicio, hay disponible un punto final de comprobación de estado básico (no autorizado) en `/health` para comprobar el estado de la app (activa/inactiva).

El siguiente archivo `/server/routers/health.js` proporciona el código de punto final de comprobación de estado:
```js
var express = require('express');

module.exports = function(app) {
  var router = express.Router();

  router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });

  app.use("/health", router);
}
```
{: codeblock}

## Recomendaciones para pruebas de actividad y preparación
{: #node-readiness-probes}

Las pruebas de comprobación pueden incluir la viabilidad de conexiones a servicios en sentido descendente en el resultado cuando no haya una reserva aceptable si el servicio en sentido descendente no está disponible. Esto no implica llamar a la comprobación de estado que proporciona directamente el servicio en sentido descendente, puesto que la infraestructura realiza la comprobación. En su lugar, considere la posibilidad de verificar el estado de las referencias existentes que tiene la aplicación en los servicios en sentido descendente: puede ser una conexión JMS a WebSphere MQ o un consumidor o productor Kafka inicializado. Si comprueba la viabilidad de referencias internas en servicios en sentido descendente, almacene en memoria caché el resultado para minimizar el impacto que tiene la comprobación de estado en la aplicación.

Una prueba de actividad, por el contrario, tiene en cuenta lo que se comprueba, ya que un error puede provocar una terminación inmediata del proceso. Un punto final HTTP simple que siempre devuelva `{"status": "UP"}` con el código de estado `200` es una elección razonable.

### Adición de soporte para los puntos finales de preparación (readiness) y actividad (liveness) de Kubernetes
{: #kube-readiness-add}

La [biblioteca `cloud-health-connect`](https://github.com/CloudNativeJS/cloud-health-connect){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") de [CloudNativeJS](https://github.com/cloudnativejs){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") proporciona una infraestructura para definir puntos finales de actividad y de preparación separados en Nodo que permitan la composición de orígenes para saber el estado de cada punto final.

## Configuración de pruebas de actividad y preparación en Kubernetes
{: #kube-readiness-config}

Declare las pruebas de actividad y preparación junto con el despliegue de Kubernetes. Ambas pruebas utilizan los mismos parámetros de configuración:

* El kubelet espera a `initialDelaySeconds` antes de la primera prueba.

* El kubelet prueba el servicio cada `periodSeconds` segundos. El valor predeterminado es 1.

* La prueba expira pasados `timeoutSeconds` segundos. El valor mínimo y valor predeterminado es 1.

* La prueba se realiza correctamente si es satisfactoria `successThreshold` veces tras un error. El valor predeterminado y mínimo es 1. El valor debe ser 1 en las pruebas de actividad.

* Cuando se inicia un pod y la prueba falla, Kubernetes intenta reiniciar el pod `failureThreshold` veces y luego abandona. El valor mínimo es 1 y el valor predeterminado es 3.
    - En una prueba de actividad, "abandonar" significa reiniciar el pod.
    - En una prueba de preparación, "abandonar" significa marcar el pod como `Unready` (no preparado).

Para evitar los ciclos de reinicio, establezca `livenessProbe.initialDelaySeconds` para que sea seguro más tiempo del que necesita el servicio para inicializarse. Puede utilizar un valor más corto para `readinessProbe.initialDelaySeconds`, para direccionar solicitudes al servicio en cuanto esté listo.

Consulte la siguiente configuración de ejemplo:
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```
{: codeblock}

Para obtener más información, consulte cómo [Configurar pruebas de actividad y de preparación](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
