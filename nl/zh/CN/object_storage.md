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

# 在 Object Storage 中存储静态内容
{: #object-storage}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://cloud.ibm.com/docs/services/cloud-object-storage/libraries/node.html#using-node-js -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://cloud.ibm.com/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage -->

{{site.data.keyword.cos_full_notm}} 是云计算的一个基本组件，为 Apple 开发者及其应用程序提供了强大的功能。与在文件层次结构（例如，Block Storage 或 File Storage）中存储信息不同，对象存储仅包含文件及其元数据。这些文件存储在称为存储区的集合中。根据定义，这些对象是不可变的，因此最适合用于存储图像、视频和其他静态文档等数据。对于经常更改的数据或关系数据，可以使用 [{{site.data.keyword.cloudant_short_notm}}](/docs/node/cloudant.html) 数据库服务。

{{site.data.keyword.cos_short}} (COS) 是一个存储系统，可用于存储非结构化数据，灵活、经济有效且可扩展。数据可通过 SDK 或使用 IBM 用户界面进行访问。您可以使用 {{site.data.keyword.cos_short}} 通过 RESTful API 和 SDK 支持的自助服务门户网站来访问非结构化数据。

## 开始之前
{: #prereqs-cos}

确保以下先决条件准备就绪：
1. 您必须具有 [{{site.data.keyword.cloud}} 帐户 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}。
2. 您必须具有 [{{site.data.keyword.cos_short}} SDK for Node.js ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/ibm/ibm-cos-sdk-js){: new_window}。
3. 您必须具有 Node 4.x+。
4. 找到稍后要用于 SDK 初始化的凭证密钥值：

    * _**endpoint**_ - Cloud Object Storage 的公共端点。该端点可在 [{{site.data.keyword.cloud_notm}} 仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/dashboard/apps){: new_window} 中获取。
    * _**api-key**_ - 创建服务凭证时生成的 API 密钥。创建和删除示例需要写访问权。
    * _**resource-instance-id**_ - Cloud Object Storage 的资源标识。资源标识可通过 [{{site.data.keyword.cloud_notm}} CLI](/docs/cli/index.html) 或 [{{site.data.keyword.cloud_notm}} 仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/dashboard/apps){: new_window} 获取。

## 步骤 1. 创建 {{site.data.keyword.cos_short}} 的实例
{: #create-instance-cos}

1. 在 [{{site.data.keyword.cloud_notm}} 目录](https://cloud.ibm.com/catalog/)中，选择**存储**类别，然后单击 {{site.data.keyword.cos_short}}。这将打开服务配置页面。
2. 为服务实例提供名称或使用预设名称。
3. 选择价格套餐，然后单击**创建**。这将打开 Object Storage 实例页面。
4. 在导航菜单中，选择**服务凭证**。
5. 在“服务凭证”页面中，单击**新建凭证**。
6. 在“添加新凭证”页面中，请确保角色设置为**写入者**，然后单击**添加**。这将创建新凭证，并且新凭证会显示在“服务凭证”页面上。

## 步骤 2. 安装 SDK
{: #install-cos}

通过从命令行使用 [npm](https://nodejs.org/) 软件包管理器来安装 {{site.data.keyword.cos_short}} SDK for Node.js：
```
npm install ibm-cos-sdk
```
{: pre}

## 步骤 3. 初始化 SDK
{: #initialize-cos}

在应用程序中初始化 SDK 后，可以使用 {{site.data.keyword.cos_short}} 来存储数据。通过提供凭证回并提供要在一切就绪时运行的回调函数，从而初始化连接。

1. 通过将以下 `require` 定义添加到 `server.js` 文件来装入客户机库。
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. 通过提供凭证来初始化客户机库。
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

  如果需要帮助查找应用程序的凭证密钥值，请查看[开始之前](/docs/node/object_storage.html#prereqs-cos)部分中的*步骤 4*，以了解有关在何处查找这些值的详细信息。
  {: tip}

3. 将以下代码添加到 `server.js` 文件。
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

### 使用基本操作管理数据
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### 创建存储区
```js
function doCreateBucket() {
    console.log('Creating bucket');
    return cos.createBucket({
        Bucket: 'my-bucket',
        CreateBucketConfiguration: {
          LocationConstraint: 'us-standard'
        },
    }).promise();
}
```
{: codeblock}

#### 创建/上传或覆盖对象
```js
function doCreateObject() {
    console.log('Creating object');
    return cos.putObject({
        Bucket: 'my-bucket',
        Key: 'foo',
        Body: 'bar'
    }).promise();
}
```
{: codeblock}

#### 下载对象
<!-- Verify this snippet with Nick when he returns from vacation -->
```js
function doGetObject() {
 console.log('Getting object');
 return cos.getObject({
   Bucket: 'my-bucket',
   Key: 'foo'
 }).createReadStream().pipe(fs.createWriteStream('./MyObject'));
}
```
{: codeblock}

#### 删除对象
```js
function doDeleteObject() {
    console.log('Deleting object');
    return cos.deleteObject({
        Bucket: 'my-bucket',
        Key: 'foo'
    }).promise();
}
```
{: codeblock}

有关多部分上传、安全功能和其他操作，请查看[完整文档](/docs/services/cloud-object-storage/libraries/node.html#using-node-js)。

## 步骤 4. 测试应用程序
{: #test-cos}

是否一切设置正确？请进行测试！

1. 运行应用程序，确保启动初始化和各个操作，例如创建存储区和向存储区添加数据。
2. 返回到先前在 Web 浏览器中创建的 {{site.data.keyword.cos_short}} 服务实例，然后打开服务仪表板。
3. 选择使用的存储区，然后在仪表板中会看到新创建的对象。

遇到困难？请查看[{{site.data.keyword.cos_short}} API 参考](/docs/services/cloud-object-storage/api-reference/about-api.html){:new_window}。

## 后续步骤
{: #next-cos notoc}

太棒了！您已向应用程序添加了安全持久性级别。请一鼓作气尝试下列其中一个选项：

* 查看 [{{site.data.keyword.cos_short}}SDK for Node.js ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/ibm/ibm-cos-sdk-js){:new_window} 源代码。
* 查看[存储区和对象操作的示例代码 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/ibm/ibm-cos-sdk-js#example-code){:new_window}。
* 入门模板工具包是使用 {{site.data.keyword.cloud_notm}} 的功能的最快方法之一。请在[移动开发者仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/developer/mobile/dashboard){:new_window} 中查看可用的入门模板工具包。下载代码。运行应用程序！
* 要了解有关 {{site.data.keyword.cos_short}} 提供的所有功能的更多信息并利用这些功能，请[查看文档](/docs/services/cloud-object-storage/about-cos.html)。
