---

copyright:
  years: 2017, 2019
lastupdated: "2019-01-14"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 在 {{site.data.keyword.cloud_notm}} 中存储文档
{: #cloudant}

{{site.data.keyword.cloudantfull}} 是面向文档的数据库即服务 (DBaaS)。它将数据存储为 JSON 格式的文档。{{site.data.keyword.cloudant_short_notm}} 秉承可扩展性、高可用性和耐久性而构建，可轻松配置用于 Node.js 应用程序。它随附多种索引选项，包括 MapReduce、{{site.data.keyword.cloudant_short_notm}} Query、全文索引和地理空间索引。通过其复制功能，可以轻松实现数据库集群、台式 PC 和移动设备之间的数据同步。
{:shortdesc}

有关更多信息，请参阅 [{{site.data.keyword.cloudant_short_notm}} 基础知识](/docs/services/Cloudant/basics/index.html#cloudant-nosql-db-basics){:new_window}。

## 开始之前
{: #prereqs-cloudant}

确保以下先决条件准备就绪：
 * [Nodejs-cloudant ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/cloudant/nodejs-cloudant){:new_window} 2.3.0+ 客户机库。
 * 您必须具有 [{{site.data.keyword.cloud}} 帐户 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}。
 * 要访问 {{site.data.keyword.cloudant_short_notm}}，必须在 [{{site.data.keyword.cloud_notm}} 仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/dashboard/apps){: new_window} 中创建服务，然后通过该服务实例来启动 {{site.data.keyword.cloudant_short_notm}} 仪表板。
 * 这些指示信息中的代码片段使用的是 IAM 认证。
 
### 通过 {{site.data.keyword.cloudant_short_notm}} 启用 IAM
{: #enable_IAM-cloudant}

只有新的 {{site.data.keyword.cloudant_short_notm}} 服务实例才能与 {{site.data.keyword.cloud_notm}} IAM 配合使用。

所有新的 {{site.data.keyword.cloudant_short_notm}} 服务实例在供应后均可使用 {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)。通过 {{site.data.keyword.cloud_notm}}“目录”供应新实例时，请选择**仅使用 IAM** 认证方法。此方式意味着通过服务绑定和凭证生成仅会提供 IAM 凭证。您可以在 [{{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)](/docs/services/Cloudant/guides/iam.html) 处找到更多信息。

## 步骤 1. 创建 {{site.data.keyword.cloudant_short_notm}} 的实例
{: #create-instance-cloudant}

创建 {{site.data.keyword.cloudant_short_notm}} 的实例时，还将创建数据库。

1. 登录到 {{site.data.keyword.cloud_notm}} 帐户。
2. 在 [{{site.data.keyword.cloud_notm}} 仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/dashboard/apps){: new_window} 中，单击**创建资源**。这将打开{{site.data.keyword.cloud_notm}}“目录”。
3. 在 [{{site.data.keyword.cloud_notm}} 目录](https://cloud.ibm.com/catalog/)中，选择**数据库**类别，然后单击 {{site.data.keyword.cloudant_short_notm}}。这将打开服务配置页面。
4. 在以下字段中填写信息：
  * **服务名称** - 输入服务实例的名称，或使用预设名称。
  * **选择要部署到的区域/位置** - 选择要在其中部署服务的区域。
  * **选择资源组** - 选择资源组，或接受缺省值。
  * **可用认证方法** - 选择**仅使用 IAM** 作为认证方法。
5. 选择价格套餐，然后单击**创建**。这将打开服务实例的页面。
6. 要创建服务凭证，请完成以下步骤：
  1. 在导航菜单中，选择**服务凭证**。
  2. 单击**新建凭证**。这将打开“添加新凭证”页面。
  3. 在“添加新凭证”页面中，填写相应字段，然后单击**添加**。新服务凭证将添加到服务实例。
  4. 如果要查看服务凭证详细信息，请单击新凭证的**操作**列中的**查看凭证**。
7. 在导航菜单中，选择**管理**，然后单击**启动 Cloudant 仪表板**。
8. 在导航菜单中，单击**数据库**图标。
9. 单击**创建数据库**，提供数据库名称，然后单击**创建**。这将打开数据库页面。

如果要查看有关供应 {{site.data.keyword.cloud_notm}} 服务实例的相关信息，请参阅[在 IBM Cloud 上创建 IBM Cloudant 实例教程](/docs/services/Cloudant/tutorials/create_service.html#creating-a-cloudant-nosql-db-instance-on-ibm-cloud){: new_window}。

## 步骤 2. 安装 SDK
{: #install-cloudant}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

从您自己的 Node.js 项目着手，并将此工作定义为依赖项。换言之，就是将 {{site.data.keyword.cloudant_short_notm}} 置于 package.json 依赖项中。通过命令行使用 [npm](https://nodejs.org/) 软件包管理器来安装 SDK：
```
npm install --save @cloudant/cloudant
```
{: codeblock}

请注意，现在 `package.json` 文件显示有 Cloudant 软件包。

## 步骤 3. 初始化 SDK
{: #initialize-cloudant}

在应用程序中初始化 SDK 后，可以使用 {{site.data.keyword.cloudant_short_notm}} 来存储数据。要初始化连接，请输入凭证并提供回调函数以在一切就绪时运行。

1. 通过将以下 `require` 定义添加到 `server.js` 文件来装入客户机库。
  ```js
  var Cloudant = require('@cloudant/cloudant');
  ```
  {: codeblock}

2. 通过提供凭证来初始化客户机库。使用 `iamauth` 插件通过 IAM API 密钥来创建数据库客户机。 
  ```js
  var cloudant = new Cloudant({ url: 'https://examples.cloudant.com', plugins: { iamauth: { iamApiKey: 'xxxxxxxxxx' } } });
  ```
  {: codeblock}

3. 通过将以下代码添加到 `server.js` 文件来列出数据库。
  ```javascript
  cloudant.db.list(function(err, body) {
  body.forEach(function(db) {
    console.log(db);
    });
  });
  ```
  {: codeblock}

大写的 `Cloudant` 是您使用 `require()` 装入的软件包。小写的 `cloudant` 是与您的 {{site.data.keyword.cloudant_short_notm}} 服务的已认证连接。
{: tip}

### 使用基本操作管理数据
{: #basic_operations-cloudant}
<!--Borrowed from https://github.com/cloudant/nodejs-cloudant/blob/master/example/crud.js-->

这些基本操作说明了使用已初始化的客户机来创建、读取、更新和删除文档的操作。

#### 创建文档
```js
var createDocument = function(callback) {
  console.log("Creating document 'mydoc'");
  // 指定文档的标识，以便日后可以更新及删除该文档
  db.insert({ _id: 'mydoc', a: 1, b: 'two' }, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

#### 读取文档
```js
var readDocument = function(callback) {
  console.log("Reading document 'mydoc'");
  db.get('mydoc', function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // 保留文档的副本，以便知道其修订版标记
    doc = data;
    callback(err, data);
  });
};
```
{: codeblock}

#### 更新文档
```js
var updateDocument = function(callback) {
  console.log("Updating document 'mydoc'");
  // 使用之前在回读时保留的副本，对文档进行更改
  doc.c = true;
  db.insert(doc, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // 保留更新的修订版，以便可以将其删除
    doc._rev = data.rev;
    callback(err, data);
  });
};
```
{: codeblock}

#### 删除文档
```js
var deleteDocument = function(callback) {
  console.log("Deleting document 'mydoc'");
  // 提供要删除的标识和修订版
  db.destroy(doc._id, doc._rev, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

## 步骤 4. 测试应用程序
{: #test-cloudant}

是否一切设置正确？请进行测试！

1. 运行应用程序，确保启动初始化和各个操作，例如创建文档。
2. 在 [{{site.data.keyword.cloud_notm}} 仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/dashboard/apps){: new_window} 中，单击先前创建的 {{site.data.keyword.cloudant_short_notm}} 服务实例。服务实例打开时，单击**启动 Cloudant 仪表板**。
3. 在 {{site.data.keyword.cloudant_short_notm}} 仪表板中，选择在其中创建了新文档的数据库。

遇到困难？请查看 [{{site.data.keyword.cloudant_short_notm}} API 参考](/docs/services/Cloudant/api/index.html#api-reference-overview){:new_window}。

## 后续步骤
{: #next-cloudant notoc}

太棒了！您已向应用程序添加了安全持久性级别。请一鼓作气尝试下列其中一个选项：

* 查看 [{{site.data.keyword.cloudant_short_notm}} SDK for Node.js ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/cloudant/nodejs-cloudant){: new_window} 源代码。
* 查看[数据库和文档操作的示例代码 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/cloudant/nodejs-cloudant/tree/master/example){: new_window}。
* 入门模板工具包是使用 {{site.data.keyword.cloud}} 的功能的最快方法之一。请在[移动开发者仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} 中查看可用的入门模板工具包。下载代码。运行应用程序！
* 要了解有关 {{site.data.keyword.cloudant_short_notm}} 提供的所有功能的更多信息并利用这些功能，请[查看文档](/docs/services/Cloudant/getting-started.html)！
