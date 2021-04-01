---

copyright:
  years: 2018, 2021
lastupdated: "2021-04-01"

keywords: nodejs logging, view logs nodejs, add logging nodejs, log4j nodejs, stdout nodejs, nodejs log, output nodejs, nodejs logger

subcollection: node

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:external: target="_blank" .external}

# Logging in Node.js
{: #logging_nodejs}

Log messages are strings with contextual information about the state and activity of the microservice at the time that the log entry is made. Logs are required to diagnose how and why services fail, and plays a supporting role to [appmetrics](/docs/node?topic=node-metrics) in monitoring application health.

Given the transient nature of processes in cloud environments, logs must be collected and sent elsewhere, usually to a centralized location for analysis. The most consistent way to log in cloud environments is to send log entries to standard output and error streams, which leaves the infrastructure to handle the rest.

You can use [Log4js](https://github.com/log4js-node/log4js-node){: external}, which is a popular logging framework for Node.js, and provides many native benefits that include: 
* Logging to `stdout` or `stderr`
* Various appending options
* Configurable log message layout and patterns
* Using `Log Levels` for different log categories

## Adding Log4js support to existing Node.js app
{: #add_log4j}

1. First, install `log4js` by running the following [npm](https://nodejs.org/en/){: external} command in the root directory of your application, which installs the package and adds it to your `package.json` file.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. To use it in your application, add the following lines of code to your app:
  ```js
  var log4js = require('log4js');

  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  **stdout Output**:
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

3. To write log events to the standard error stream, you can configure an appender like the following example:
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  For more information about customizing the log messages with appenders, `Log Levels`, and configuration details, see the official [log4js-node documentation](https://log4js-node.github.io/log4js-node/){: external}.

## Monitoring with App Service apps
{: #monitoring}

Node.js apps that are created by using the {{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard){: external} come with Log4js by default. You can open the `server/server.js` file to see the following Log4js code:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

By default, the `Log Level` is set to `OFF` so that it can safely be used in libraries, but can be overridden by the application's **LOG_LEVEL** environment variable.
{: tip}

### Viewing the log output
{: #node-view-log-output}

See the following sample log output from running the app natively or in a cloud environment:
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

You can view log output by using the following methods:
* For local environments, use `stdout`.
* For [Cloud Foundry](/docs/cli?topic=cli-ibmcloud_commands_apps#ibmcloud_app_logs) deployments, you can access logs by running:
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* For [Kubernetes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs){: external} deployments, you can access logs by running:
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## Next steps
{: #next_steps-logging notoc}

Learn more about viewing logs in the following deployment environments:
* [Managing Kubernetes cluster logs with {{site.data.keyword.la_full_notm}}](/docs/log-analysis?topic=log-analysis-kube)
* [Viewing logs in Cloud Foundry](/docs/log-analysis?topic=log-analysis-monitor_cfapp_logs)
* [Viewing logs in {{site.data.keyword.openwhisk}}](/docs/openwhisk?topic=openwhisk-logs)
