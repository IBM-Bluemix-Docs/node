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

# Utilización de la comprobación de estado en apps Node.js
{: #healthcheck}

Las comprobaciones de estado proporcionan un mecanismo simple para determinar si una aplicación del lado del servidor se está comportando correctamente. Muchos entornos de despliegue, como [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) y [Kubernetes](https://www.ibm.com/cloud/container-service), se pueden configurar para sondear los puntos finales de estado de forma periódica para determinar si una instancia del servicio está lista para aceptar tráfico.

## Visión general de la comprobación de estado
{: #overview}

Normalmente se accede a las comprobaciones de estado por `HTTP`, y utilizan códigos de retorno estándares para indicar el estado `UP` o `DOWN`. Entre los ejemplos se incluye volver a `200` para `UP`, y `5xx` para `DOWN`. Por ejemplo, se utiliza un código de retorno `503` cuando la aplicación no puede manejar solicitudes y se utiliza un código de retorno `500` cuando el servidor experimenta una condición de error. El valor de retorno de una comprobación de estado es variable, pero una respuesta JSON mínima, como `{“status”: “UP”}`, proporciona coherencia.

Kubernetes define los sondeos de [actividad](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) y de [preparación](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/):

* El sondeo de actividad permite a una aplicación indicar si se puede reiniciar el proceso. Se aplica un subconjunto de consideraciones: la comprobación de `actividad` puede fallar si se alcanza un umbral de memoria, por ejemplo. Si la app se está ejecutando en Kubernetes, considere la posibilidad de añadir un punto final `liveness` para asegurarse de que el proceso se reinicia cuando sea necesario.

* El sondeo de preparación se utiliza para las decisiones de direccionamiento automático. El éxito o la anomalía indica si la aplicación puede recibir un nuevo trabajo. Este punto final puede incluir consideraciones para servicios descendentes necesarios. Considere la posibilidad de almacenar en memoria caché el resultado de las comprobaciones de servicio en sentido descendente para minimizar la carga global en el sistema (por ejemplo, la conectividad de base de datos de prueba como máximo una vez por segundo).

Cloud Foundry sólo utiliza un punto final de estado. Si esta comprobación falla, Cloud Foundry reinicia el proceso. Pero si tiene éxito, Cloud Foundry presupone que el proceso puede manejar un nuevo trabajo. Esta comprobación de estado convierte al punto final único en una especie de combinación entre actividad y preparación.

## Adición de comprobación de estado a una app Node.js existente
{: #add-healthcheck-existing}

Para añadir un punto final de comprobación de estado a una app existente, empiece introduciendo una nueva ruta, como se muestra en el ejemplo siguiente:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

La adición de criterios significativos aquí garantiza que una respuesta satisfactoria del punto final `/health` indica correctamente que el servicio está listo para manejar las solicitudes.

## Acceso a la comprobación de estado desde las apps del kit de iniciación de Node.js
{: #healthcheck-starterkit}

De forma predeterminada, al generar una app Node.js utilizando un kit de iniciación, hay disponible un punto final de comprobación de estado básico (no autorizado) en `/health` para comprobar el estado de la app (activa/inactiva).

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

Puede personalizar la comprobación para asegurarse de que sólo se devuelve correctamente cuando la aplicación está lista para aceptar tráfico.
{: tip}
