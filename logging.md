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

# Logging in Node.js
{: #logging_nodejs}

Logs are required to diagnose how and why services either failed or are behaving erroneously. Given the transient nature of processes in Cloud environments, logs must be collected and sent elsewhere, usually to a centralized location for analysis. Unlike [`appmetrics`](appmetrics.html), logs are not meant to be used for measuring application performance. Ideally, applications emit logs as event streams, rely on the environment to collect them, and send them to the right places.

Apps can be designed to emit logs in JSON format natively so that the logs can be parsed independently of the log aggregation technology (or dashboards) that are used. Or, you might want to use a custom parser with custom dashboards for enhanced monitoring.

## Adding Log4js support to Node.js app
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) is a popular logging framework for Node.js, and provides many native benefits, which include: 
* Logging to `stdout` or `stderr`
* Various appending options
* Configurable log message layout and patterns
* Using Log Levels for different log categories

1. To use Log4js, run the following command in the root directory of your application, which installs the package and adds it to your `package.json` file.
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

Node.js apps that are created by using the {{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard) come with Log4js by default. Running the app natively or in a Cloud environment produces output like: `2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`. You can view the output as follows:
* Use `stdout` when you run locally.
* In the logs for [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) and [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/) deployments, which are accessed by `ibmcloud app logs --recent <APP_NAME>` and `kubectl logs <deployment name>`.

In the `server/server.js` file, you can see the following code:
```javascript
const logger = log4js.getLogger(appName);
const app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

By default, the Log Level is set to `INFO`, and can be overridden by the application's **LOG_LEVEL** environment variable.
{: tip}

## Next Steps
{: #next_steps notoc}

Learn more about viewing the logs in each of our deployment environments:
* [Kubernetes Logs](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [Cloud Foundry Logs](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [{{site.data.keyword.openwhisk}} Logs & Monitoring](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Using a Log aggregator:
* [{{site.data.keyword.cloud_notm}} Log Analysis](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK stack](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
