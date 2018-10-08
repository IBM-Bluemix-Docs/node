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

# 在 Node.js 应用程序中使用运行状况检查
{: #healthcheck}

运行状况检查提供了一种简单的机制，用于确定服务器端应用程序是否在正常运行。可以将许多部署环境（例如，[Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) 和 [Kubernetes](https://www.ibm.com/cloud/container-service)）配置为定期轮询运行状况端点，以确定服务的实例是否已准备好接受流量。

## 运行状况检查概述
{: #overview}

运行状况检查通常通过 `HTTP` 进行访问，并使用标准返回码来指示 `UP` 或 `DOWN` 状态。示例包括返回 `200`（表示 `UP`）和 `5xx`（表示 `DOWN`）。例如，应用程序无法处理请求时，将使用 `503` 返回码；服务器遇到错误状况时，将使用 `500`。运行状况检查的返回值是可变的，但极简 JSON 响应（如 `{"status": "UP"}`）可提供一致性。

Kubernetes 定义了[活动性](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)和[就绪性](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)探测器：

* 活动性探测器允许应用程序指示进程是否可以重新启动。有一部分适用的考虑因素：例如，如果达到内存阈值，那么`活动性`检查可能会失败。如果应用程序正在 Kubernetes 上运行，请考虑添加 `liveness` 端点，以确保在必要时重新启动进程。

* 就绪性探测器用于自动进行路由决策。成功或失败指示应用程序是否可以接收新工作。此端点可以包含适用于必需的下游服务的考虑因素。考虑对下游服务检查结果进行高速缓存，以最大限度地减少系统的总体负载（例如，每秒最多测试一次数据库连接）。

Cloud Foundry 仅使用一个运行状况端点。如果此检查失败，Cloud Foundry 将重新启动该进程。但是，如果成功，Cloud Foundry 将假定该进程可以处理新工作。此运行状况检查将使单个端点成为活动性和就绪性的组合。

## 向现有 Node.js 应用程序添加运行状况检查
{: #add-healthcheck-existing}

要向现有应用程序添加运行状况检查端点，请首先引入新路径，如以下示例中所示：
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

在此添加有意义的条件可确保来自 `/health` 端点的成功响应正确指示服务已准备好处理请求。

## 通过 Node.js 入门模板工具包应用程序访问运行状况检查
{: #healthcheck-starterkit}

缺省情况下，使用入门模板工具包来生成 Node.js 应用程序时，`/health` 处提供了基本（未授权）运行状况检查端点，可用于检查应用程序的状态 (UP/DOWN)。

运行状况检查端点代码通过以下 `/server/routers/health.js` 文件提供：
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

您可以定制检查，以确保仅当应用程序准备好接受流量时才成功返回。
{: tip}
