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

# 设置端到端跟踪
{: #e2e-tracing}

以下教程重点介绍了 Zipkin 以及如何使用 [appmetrics-zipkin](https://github.com/RuntimeTools/appmetrics-zipkin) 模块来跟踪 Node.js 应用程序。您可以在原始 [appmetrics-zipkin 公告](https://developer.ibm.com/node/2017/10/26/add-zipkin-open-tracing-support-node-js-application-one-line-code/)中了解有关 Zipkin 的更多信息。 

在以下步骤中，通过 `appmetrics-zipkin` 模块使用两个小型应用程序（一个前端，一个后端）在两个端点之间进行跟踪。您可以从头开始，也可以将此处描述的原则应用于现有 Node.js 应用程序。 

## 步骤 1. 安装和启用 appmetrics-zipkin 模块
{: #install-zipkin}

在 Node.js 应用程序的 `package.json` 文件所在的位置中，输入以下 [npm](https://nodejs.org/) 命令以将 `appmetrics-zipkin` 模块添加到依赖项列表中：
```
npm install --save appmetrics-zipkin
```
{: codeblock}

将以下行添加到 Node.js 服务器代码中其他任何 appmetrics `require` 语句**之前**：
```js
var appzip = require('appmetrics-zipkin');
```
{: codeblock}

以下语句将跟踪添加到 `HTTP` 和 `request` 方法调用，并将数据发送到 Zipkin 服务器。缺省情况下，该模块在 `localhost` 和端口 `9411` 处查找 Zipkin 服务器。可以使用以下语法来更改主机名和端口：
```js
var appzip = require('appmetrics-zipkin')({
 host: "my.host.here",
 port: 12345, // changeme
 serviceName:'my-service-name'
});
```
{: codeblock}

如常发送请求。例如：

```
http.request(options, callback).end();
```
{: codeblock} 

## 步骤 2. 设置 Zipkin 服务器
{: #setup-zipkin-server}

现在，您需要设置数据发送到的位置，具体地说，就是存储跟踪的位置；跟踪由多个跨度组成。在部署到任何云之前，可以通过在本地或容器中设置 Zipkin 服务器来测试 e2e 跟踪配置。 

### 在本地设置 Zipkin

Zipkin 在单个 `jar` 文件中提供，以便您可以在要使用 Zipkin 的系统上通过以下命令来下载并运行该文件：

1. 下载 Zipkin：
  ```
  wget zipkin.jar 'https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=1.31.3&c=exec'
  ```
  {: codeblock}

2. 启动 Zipkin：
  ```
  java -jar zipkin.jar
  ```
  {: codeblock}

  `wget` 命令用于下载 Zipkin 文件，`java -jar` 命令用于启动 Zipkin 服务器。您还可以从其他位置下载 Zipkin，但是对于本教程，务必使用 V1.x，这样跟踪格式才会与 Zipkin 服务器所期望的格式相匹配。

  如果此命令的输出太详细或者您希望在后台运行 Zipkin，那么可以为 `wget` 命令添加 `-q -O`，并为 Zipkin 添加 `/dev/null 2>&1 &`。在此阶段，您将下载 Zipkin `.jar` 文件，并运行 main 方法来启动 Zipkin 服务器。

### 在 Docker 容器中设置 Zipkin

您可以选择通过运行以下命令在 Docker 容器中运行 Zipkin 服务器：
```
docker run -d -p 9411:9411 openzipkin/zipkin
```
{: codeblock}

在端口 `9411` 上，`openzipkin/zipkin` 模块通过一个简单命令进行下载、安装并启动。

### 访问 Zipkin 控制台
下图显示了在端口 `9411` 的 `localhost` 上运行的 Zipkin 服务器：

![ZipkinNoData](images/ZipkinNoData.png)

可以单击**查找跟踪**并修改搜索选项，以选择性地仅显示特定时间段内的跟踪。您还可以进行过滤，以显示涉及特定服务名称的跟踪。服务名称是在检测代码时指定的，在示例方案中，我们使用的服务名称是“getter”和“pusher”。

## 步骤 3. 测试示例场景
{: #example-scenario}

如果遵循 [GitHub 项目的文档](https://github.com/ibm-developer/nodejs-zipkin-tracing)，那么最终将获得以下样本应用程序。这是一个简单的进程，涉及跟踪两个端点之间的请求和响应。以下图像显示了 Zipkin 服务器以及显示屏上的已收集跟踪数据。要记住的关键点是务必包含 `require('appmetrics-zipkin')`，并可选择包含 Zipkin 服务器配置代码。以下示例场景显示了如何将 Zipkin 跟踪快速添加到现有 Node.js 应用程序中。

### 跟踪方案概述：
* **前端**（称为 pusher）用于提示用户输入要创建并转换为小写的字符串的长度。数字越大，字符串越大，处理请求所需的时间越长。在端口 `3000` 上可用。
* **后端**（称为 getter）用于处理请求，并在端口 `3001` 上可用。
* **Zipkin 服务器**在本地运行，或在可查看跟踪数据的 Kubernetes 上运行。

### 前端应用程序 (pusher)
前端应用程序 (pusher) 服务发送请求（简单的前端）：
![frontend_app](images/frontend_app.png)

### 后端应用程序 (getter)
后端应用程序 (getter) 接收请求，该应用程序侦听的是另一个端口：![backend_app](images/Backend.png)

### 将请求从 pusher 发送到 getter
将请求从 pusher 发送到 getter：
![500please](images/500Please.png)

### 使用 Zipkin Web UI 查看跟踪
可以使用 `localhost:9411` 上的 Zipkin Web UI 来查看发送到 Zipkin 的跟踪数据。您可以看到 **getter** 接收用户输入（用户希望使用 pusher 服务向 getter 发送长度为 500 个字符的消息）：
![Getter500msg](images/Getter500Msg.png)

将显示用户请求详细信息。请注意“500”是为用户的请求提供的参数。用户希望生成包含 500 个字符的字符串。您可以看到用户具体请求的内容以及处理此请求所用时间。从服务器返回的请求的内容（有效内容）并未显示。 

我们关注的是响应时间和参数，以便可以确定用户在遇到响应缓慢时所请求的内容：![GetterGet](images/GetterGet.png)

### 识别慢请求
下面是一个慢请求的示例。以下用户请求将 5,000,000 个字符从大写转换为小写（如您所做的一样）。这显然需要更长时间：
![SlowRequest](images/SlowRequest.png)

单击此跨度将生成以下输出。同样，您可以看到使用时间多得多的高成本请求。更实际的场景会涉及可能许多 Node.js 微服务在各种终端持续接收所有方式的请求。通过使用端点的高级别视图，可以快速确定哪些服务的响应速度慢，以及用户具体请求的内容：
![TookaWhile](images/TookAWhile.png)

使用此示例，您现在具有以下场景：

* pusher 向 getter 发送了一条消息（一个跨度）。
* getter 发回响应（一个跨度）。
* 包含两个跨度的完全跟踪在本地部署的 Zipkin 服务器上显示。

随着应用程序变得越来越复杂，服务变得越来常用，显然有必要落实此类跟踪。高级别跟踪对于开发者非常有价值，开发者可以借此快速、有效地识别问题并对问题分类。虽然有很多替代方法可用，但我们的方法能使此操作尽可能简单，而且完全公开执行。

本教程针对不使用 Kubernetes 的部署的内容到此结束。如果您希望继续跟踪在 Kubernetes 上运行的 Node.js 应用程序，请查看下一部分。

## 后续步骤
{: #next-steps}

* 了解如何借助 [CloudNativeJS](https://www.cloudnativejs.io/) 社区项目来构建 Cloud Native Node.js 应用程序，该项目提供了多种资产和工具，可帮助您将这些应用程序部署到基于 Docker 和 Kubernetes 的云上。

* 如果您已准备好将跟踪添加到在 Kubernetes 上运行的 Node.js 应用程序，请查看[跟踪使用 Kubernetes 的 Node.js 应用程序](https://developer.ibm.com/node/tutorial-end-end-tracing-node-js-applications/#appservice)。

