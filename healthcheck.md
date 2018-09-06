---

copyright:
  years: 2018
lastupdated: "2018-09-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Using Health check in Node.js apps
{: #healthcheck}

Health checks provide a simple mechanism to determine whether a server-side application is behaving properly. Many deployment environments, such as [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) and [Kubernetes](https://www.ibm.com/cloud/container-service), can be configured to poll health endpoints periodically to determine whether an instance of your service is ready to accept traffic.

## Health check overview
{: #overview}

Health checks are typically accessed over HTTP, and use standard return codes for indicating `UP` or `DOWN` status. Examples include returning `200` for `UP`, and `5xx` for `DOWN`. To be specific, a `503` return code is used when the application can’t handle requests or isn't started yet, and a `500` is used when the server experiences an error condition. The return value of a health check is variable, but a minimal JSON response, like `{“status”: “UP”}` provides consistency.

Cloud Foundry uses one health endpoint to indicate whether a service instance can handle requests. In Kubernetes, a health check endpoint is known as a `readiness` endpoint. Your application must define this endpoint to help determine automatic routing decisions. The success or failure of this endpoint can include considerations for required downstream services if an acceptable fallback is available. If you do check downstream services, caching the result is sometimes useful, to minimize overall load on the system.

Kubernetes defines an extra [liveness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) endpoint, which allows the application to indicate whether the process is to be restarted. A subset of considerations applies here: a `liveness` check might fail if a memory threshold is reached, for example. If your app is running on Kubernetes, consider adding a `liveness` endpoint to ensure that your process is restarted when necessary.

## Adding Health check to an existing Node.js app
{: #add-healthcheck-existing}

To add a health check endpoint to an existing app, start by introducing a new route as shown in the following example:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

Adding meaningful criteria here ensures that a successful response from the `/health` endpoint correctly indicates that the service is ready to handle requests.

## Accessing Health check from Node.js Starter Kit apps
{: #healthcheck-starterkit}

By default, when you generate a Node.js app by using a Starter Kit, a basic (unauthorized) health check endpoint is available at `/health` to check the status of the app (UP/DOWN).

The health check endpoint code is provided by the following `/server/routers/health.js` file:
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

You can customize the check to ensure that it returns successfully only when the application is ready to accept traffic.
{: tip}
