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

# Using Health check in Node.js apps
{: #healthcheck}

Health checks provide a simple mechanism to determine whether an application is behaving properly. You can add Health check support to an existing Node.js application or access native Health check support from Node.js apps that are created from {{site.data.keyword.cloud_notm}} [Starter Kits](https://console.bluemix.net/developer/appservice/starter-kits/).

## Health check overview
{: #overview}

Health checks are accessed with a browser over `HTTP`. Cloud Foundry manifest and Kubernetes files are generated with a Health check endpoint available at `/health`. Standard return codes are used for checking the UP or DOWN status, 200 for UP, and 5xx for DOWN. The response, or payload, is in a `JSON` format. You can also cache the Health check endpoint at a configurable interval for DoS.

## Adding Health check to an existing Node.js app
{: #add-healthcheck-existing}

Add Health check to an existing app by introducing a new route as shown in the following example:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

Check the status of the app with a browser by accessing the `/health` endpoint.

Health check is extensible, as you can add time stamps or other meaningful criteria.

## Accessing Health check from Node.js Starter Kit apps
{: #healthcheck-starterkit}

By default, when you generate a Node.js app by using a Starter Kit,
a basic (unauthorized) Health check endpoint is available at `/health` to check the status of the app (UP/DOWN).

The Health check endpoint is provided by the `/server/routers/health.js` file, which contains the following code:
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


