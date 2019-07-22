---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-11"

keywords: nodejs metrics, application metrics nodejs, node appmetrics, nodejs autoscaling, nodejs dash, appmetrics-dashs nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 将应用程序度量值与 Node.js 应用程序配合使用
{: #metrics}

了解如何安装、访问和理解 Node.js 应用程序度量值。您可以使用 [Node Application Metrics](https://developer.ibm.com/open/projects/node-application-metrics/){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 仪表板监视 Node.js 应用程序，以通过在基于 Web 的前端显示度量值来可视化 Node.js 应用程序的性能。
{: shortdesc}

## 直观地识别问题
{: #identify-problems}

应用程序度量值对于监视应用程序的性能非常重要。能实时查看度量值（如 CPU、内存、等待时间和 HTTP 度量值）是确保应用程序持续有效运行所必需的。您可以使用云服务（如 Cloud Foundry 的[自动缩放](/docs/services/Auto-Scaling?topic=Auto-Scaling)），依靠度量值来动态缩放实例，以便与当前工作负载相匹配。通过使用应用程序度量值，您可以精确地获悉何时扩展、缩减或清除不再需要的实例，从而使成本保持在较低水平。

应用程序度量值将作为时间序列数据进行捕获。聚集和可视化捕获的度量值可帮助识别常见的性能问题，例如：

* 某些或所有路径上的 HTTP 响应时间缓慢
* 应用程序中的吞吐量差
* 导致性能下降的需求峰值
* 高于预期 CPU 使用率
* 内存使用量高或不断增长（潜在内存泄漏）

内置的应用程序度量值仪表板 ([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")) 还包含表示“其他请求”的图表，该图表显示支持的数据库（MongoDB、MySQL、Postgres、LevelDB 和 Redis）的数据库请求持续时间、Socket.IO 和 Riak 事件。

可以在仪表板中生成“节点报告”或“堆快照”以启用更深入的分析。

## 向现有 Node.js 应用程序添加度量值
{: #add-appmetrics-existing}

使用 [`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标External link icon") 构造函数将监视功能添加到现有 Express 应用程序，以传入若干配置选项。例如，其中一个选项是使用现有服务器，而不使 `appmetrics-dash` 启动额外的服务器。

### 安装仪表板
{: #install-appmetrics}

1. 例如，使用以下简单的“Hello World”Express 应用程序：
  ```js
  var express = require('express')
  var app = express()
  app.get('/', function (req, res) {
      res.send('Hello World!')
  })
  app.listen(3000, function () {
      console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

2. 使用以下 [npm](https://nodejs.org/en/){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 命令安装 `appmetrics` 仪表板：
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. 通过添加以下代码，向现有应用程序添加 `appmetrics-dash` 支持：
  ```js
  var dash = require('appmetrics-dash').attach()
  ```
  {: codeblock}

  此命令指示 `appmetrics-dash` 使用已经创建的服务器，并添加 `appmetrics-dash` 端点。

  现在，修订后的代码类似于以下示例：
  ```js
  var express = require('express')
  var app = express()
  var dash = require('appmetrics-dash').attach()
  app.get('/', function (req, res) {
    res.send('Hello World!')
  })
  app.listen(3000, function () {
    console.log('Example app listening on port 3000!')
  })
  ```
  {: codeblock}

## 通过入门模板工具包使用度量值
{: #appmetrics-starterkits}

缺省情况下，通过入门模板工具包创建的 Node.js 应用程序会自动随附 `appmetrics-dash` 及其仪表板，但必须启用该仪表板才可使用。

可以在生成的名为 `/app_name/server/server.js` 的应用程序源文件中找到 appmetrics 代码：
```js
// 取消注释以下行以启用 zipkin 跟踪，可定制以适合您的网络配置：
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## 访问仪表板
{: #access-dashboard}

启动应用程序后，在浏览器中转至 `http://<hostname>:<port>/appmetrics-dash`。

对于正在本地运行的应用程序，请使用缺省值 `localhost:3001/appmetrics-dash`。
{: tip}

Application Metrics for Node.js 监视仪表板 UI 提供了一系列度量值，包括 HTTP 请求和事件循环等待时间，如以下视频 [Monitoring Metrics for Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be){: new_window} ![外部链接图标](../icons/launch-glyph.svg "外部链接图标") 中所示。

## 了解数据
{: #understanding-data}

![Appmetrics 仪表板](images/appmetricsdash-1.png "Appmetrics 仪表板。")

大多数数据会绘制成折线图。“HTTP 传入请求”、“HTTP 传出请求”和“其他请求”显示随时间变化的事件持续时间。“HTTP 吞吐量”显示每秒请求数。“平均响应时间”显示平均所用时间最长的最常用的 5 个传入 HTTP 请求。“CPU”和“内存”图形显示随时间变化的系统和进程使用情况。“堆”显示最大堆大小，以及随时间变化的使用堆大小。“事件循环等待时间”显示从 Node.js 事件循环定期采集的等待时间样本，其中采集每个样本的最短等待时间、平均等待时间和最长等待时间分别用一个点表示。

如果图形具有点，那么将鼠标悬停在点上可提供其他信息。例如，对于 `HTTP 传入请求`，将显示响应时间和请求的 URL。

在所有图形中，最多显示 15 分钟的数据。

如果受监视的应用程序正在生成大量数据，那么仪表板会自动开始显示数据。再次查看“HTTP 传入请求”图表时，可以看到每个点显示 2 秒时间段内的所有请求。以下工具提示显示请求总数以及所用平均时间和最长时间。最长时间即为绘制的值。

![显示工具提示](images/tooltip-1.png)




