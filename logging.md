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

# Logging in Node.js
{: #logging_nodejs}

Log messages are strings with contextual information about the state and activity of the microservice at the time that the log entry is made. Logs are required to diagnose how and why services fail, and plays a supporting role to [appmetrics](/docs/node/appmetrics.html#metrics) in monitoring application health.

Given the transient nature of processes in Cloud environments, logs must be collected and sent elsewhere, usually to a centralized location for analysis. The most consistent way to log in cloud environments is to send log entries to standard output and error streams, which leaves the infrastructure to handle the rest.

## Adding Log4js support to Node.js app
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) is a popular logging framework for Node.js, and provides many native benefits, which include: 
* Logging to `stdout` or `stderr`
* Various appending options
* Configurable log message layout and patterns
* Using Log Levels for different log categories

1. To use Log4js, run the following [npm](https://nodejs.org/) command in the root directory of your application, which installs the package and adds it to your `package.json` file.
  ```bash
  npm install --save log4js
  ```
  {: codeblock}

2. To use it in your application, add the following lines of code:
  ```javascript
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

For more information about customizing the log messages with appenders, Log Levels, and configuration details, see the official [log4js-node documentation](https://log4js-node.github.io/log4js-node/).

## Monitoring logs with App Service
{: #monitoring}

Node.js apps that are created by using the {{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard) come with Log4js by default. Running the app natively or in a Cloud environment produces output like: `2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`. You can view the output as follows:
* Use `stdout` when you run locally.
* In the logs for [CloudFoundry](/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) and [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) deployments, which are accessed by `ibmcloud app logs --recent <APP_NAME>` and `kubectl logs <deployment name>`.

In the `server/server.js` file, you can see the following code:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

By default, the Log Level is set to `OFF` so that it can safely be used in libraries, but can be overridden by the application's **LOG_LEVEL** environment variable.
{: tip}

## Next Steps
{: #next_steps-logging notoc}

Learn more about viewing the logs in each of our deployment environments:
* [Kubernetes Logs](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cloud Foundry Logs](/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} Logs & Monitoring](/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Using a Log aggregator:
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK stack](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
