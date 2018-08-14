---

copyright:
  years: 2018
lastupdated: "2018-08-14"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Storing static content in Object Storage
{: #object}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://console.bluemix.net/docs/services/cloud-object-storage/libraries/node.html#using-node-js -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://console.bluemix.net/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage -->

{{site.data.keyword.cos_full_notm}} is a fundamental component of cloud computing and provides powerful capabilities to Apple developers and their applications. Unlike storing information in a file hierarchy (such as Block or File storage), an object store consists only of the files and their metadata. These files are stored in collections that are known as buckets. By definition, these objects are immutable, which makes them perfect for data such as images, videos, and other static documents. For data that changes often or is relational, you can use the [{{site.data.keyword.cloudant_short_notm}}](/docs/node/cloudant.html) database service.

{{site.data.keyword.cos_short}} (COS) is a storage system that can be used to store unstructured data that is flexible, cost-effective, and scalable. The data is accessible through SDKs or by using the IBM user interface. You can use {{site.data.keyword.cos_short}} to access your unstructured data through a self-service portal that is backed by RESTful APIs and SDKs.

## Before you begin
{: #before}

Be sure that you have the following prerequisites ready to go:
* <a href="https://github.com/ibm/ibm-cos-sdk-js" target="_blank">{{site.data.keyword.cos_short}} SDK for Node.js <img src="../icons/launch-glyph.svg" alt="External link icon"></a>
* Node 4.x+
* You must have an <a href="https://www.ibm.com/cloud/" target="_blank">{{site.data.keyword.Bluemix}} account <img src="../icons/launch-glyph.svg" alt="External link icon"></a>

## Step 1. Creating an instance of {{site.data.keyword.cos_short}}
{: #create-instance}

1. In the [{{site.data.keyword.cloud_notm}} catalog](https://console.bluemix.net/catalog/), select the **Storage** category, and click {{site.data.keyword.cos_short}}. The service configuration page opens.
2. Give your service instance a name, or use the preset name.
3. Select your pricing plan and click **Create**. Your Object Storage instance page opens.
4. In the navigation menu, select **Service credentials**.
5. In the Service credentials page, click **New credential**.
6. In the Add new credential page, be sure that the role is set to **Writer,** and then click **Add.** The new credential is created and shown on the Service credentials page.

## Step 2. Installing the SDK
{: #install}

Install the {{site.data.keyword.cos_short}} SDK for Node.js by using the `npm` package manager from the command line:

```
npm install ibm-cos-sdk
```
{: pre}

## Step 3. Initializing the SDK
{: #initialize}

After you initialize the SDK in your app, you can by using {{site.data.keyword.cos_short}} to store data. Initialize your connection by supplying your credentials and providing a callback function to run when everything is ready.

1. Load the client library by adding the following `require` definitions to your `server.js` file.
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. Initialize the client library by supplying your credentials.
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

3. Add the following code to your `server.js` file.
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

  <table summary="Command components: Node.js apps">
  <caption>Command components for Node.js apps</caption>
    <tr>
      <th>Components</th>
      <th>Description</th>
    </tr>
    <tr>
      <td><i>endpoint</i></td>
      <td>The public endpoint for your Cloud Object Storage. The endpoint is available from the <a href="https://console.bluemix.net/dashboard/apps" target="_blank">{{site.data.keyword.cloud_notm}} Dashboard <img src="../icons/launch-glyph.svg" alt="External link icon"></a>.</td>
    </tr>
    <tr><td><i>api-key</i></td>
    <td>The API key that is generated when the service credentials are created. Write access is required for creation and deletion examples.</td>
    </tr>
    <tr>
      <td><i>resource-instance-id</i></td>
      <td>The resource ID for your cloud Object Storage. The resource ID is available through the [{{site.data.keyword.cloud_notm}} CLI](../cli/index.html) or <a href="https://console.bluemix.net/dashboard/apps" target="_blank">{{site.data.keyword.cloud_notm}} Dashboard <img src="../icons/launch-glyph.svg" alt="External link icon"></a>.</td>
    </tr>
  </table>

### Managing data with basic operations
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### Creating a bucket
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

#### Creating/uploading or overwriting an object
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

#### Downloading an object
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

#### Deleting an object
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

Check out the [full documentation](/docs/services/cloud-object-storage/libraries/node.html#using-node-js) for multipart uploads, security features, and other operations.

## Step 4. Testing your app
{: #test}

Is everything set-up correctly? Test it out!

1. Run your application, making sure to start the initialization and respective operations, such as creating a bucket and adding data to the bucket.
2. Return to the {{site.data.keyword.cos_short}} service instance that you previously created in your web browser, and open the service dashboard.
3. Select the bucket that is used, and you see your newly created objects in the dashboard.

Having trouble? Check out the [{{site.data.keyword.cos_short}} API Reference](/docs/services/cloud-object-storage/api-reference/about-api.html){:new_window}.

## Next steps
{: #next notoc}

Great job! You added a level of secure persistence to your app. Keep the momentum by trying one of the following options:

* View the <a href="https://github.com/ibm/ibm-cos-sdk-js" target="_blank">{{site.data.keyword.cos_short}} SDK for Node.js <img src="../icons/launch-glyph.svg" alt="External link icon"></a> source code.
* Check out the <a href="https://github.com/ibm/ibm-cos-sdk-js#example-code" target="_blank">example code for bucket and object operations <img src="../icons/launch-glyph.svg" alt="External link icon"></a>
* Starter Kits are one of the fastest ways to leverage the capabilities of IBM Cloud. View the available starter kits in the <a href="https://console.bluemix.net/developer/mobile/dashboard" target="_blank">Mobile developer dashboard <img src="../icons/launch-glyph.svg" alt="External link icon"></a>. Download the code. Run the app!
* To learn more about and take advantage of all of the features that {{site.data.keyword.cos_short}} has to offer, [check out the docs](/docs/services/cloud-object-storage/about-cos.html)!