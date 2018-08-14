---

copyright:
  years: 2017-2018
lastupdated: "2018-08-07"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Storing documents in {{site.data.keyword.cloud_notm}}
{: #cloudant}

{{site.data.keyword.cloudantfull}} is a document-oriented DataBase as a Service (DBaaS). It stores data as documents in JSON format. It is built with scalability, high availability, and durability in mind, and it is easy to configure for use in Node.js applications. {{site.data.keyword.cloudant_short_notm}} comes with a wide variety of indexing options that include MapReduce, {{site.data.keyword.cloudant_short_notm}} Query, full-text indexing, and geospatial indexing. The replication capabilities make it easy to keep data in sync between database clusters, desktop PCs, and mobile devices.
{:shortdesc}

For more information, see [{{site.data.keyword.cloudant_short_notm}} Basics](/docs/services/Cloudant/basics/index.html#cloudant-nosql-db-basics){:new_window}.

## Before you begin
{: #before}

Be sure that you have the following prerequisites ready to go:
 * <a href="https://github.com/cloudant/nodejs-cloudant" target="_blank">nodejs-cloudant <img src="../icons/launch-glyph.svg" alt="External link icon"></a> 2.3.0+ client library.
 * To access {{site.data.keyword.cloudant_short_notm}}, you must have either an <a href="https://www.ibm.com/cloud/cloudant" target="_blank">{{site.data.keyword.cloudant_short_notm}} account <img src="../icons/launch-glyph.svg" alt="External link icon"></a> or an <a href="https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app" target="_blank">{{site.data.keyword.cloud}} account <img src="../icons/launch-glyph.svg" alt="External link icon"></a>.
 * The code snippets in these instructions use IAM authentication.
 
### Enabling IAM with {{site.data.keyword.cloudant_short_notm}}
{: #enable_IAM}

Only new {{site.data.keyword.cloudant_short_notm}} service instances can be used with {{site.data.keyword.cloud_notm}} IAM.

All new {{site.data.keyword.cloudant_short_notm}} service instances are enabled to use IAM when provisioned. When you provision a new instance from the {{site.data.keyword.cloud_notm}} catalog, choose the **Use only IAM** authentication method. This mode means that only IAM credentials are provided via service binding and credential generation. You can find more information at [{{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)](/docs/services/Cloudant/guides/iam.html).

## Step 1. Creating an instance of {{site.data.keyword.cloudant_short_notm}}
{: #create-instance}

When you create an instance of {{site.data.keyword.cloudant_short_notm}}, you also create the database.

1. In the [{{site.data.keyword.cloud_notm}} catalog](https://console.bluemix.net/catalog/), select the **Database** category, and click {{site.data.keyword.cloudant_short_notm}}. The service configuration page opens.
2. Give your service instance a name, or use the preset name.
3. Select **Use only IAM** for the authentication method.
4. Select your pricing plan and click **Create**. The Cloudant NoSQL DB page opens.
5. Click **Launch Cloudant Dashboard** and provide credentials, if required. The Monitoring page opens.
6. From the navigation menu, click the **Databases** icon.
7. Click **Create Database**, provide a database name, and then click **Create.** Your database page opens.

If you want to see related information about provisioning an instance of the {{site.data.keyword.cloud_notm}} service, see <a href="https://console.bluemix.net/docs/services/Cloudant/tutorials/create_service.html#creating-a-cloudant-nosql-db-instance-on-ibm-cloud" target="_blank">Creating a Cloudant NoSQL DB instance on IBM Cloud tutorial <img src="../icons/launch-glyph.svg" alt="External link icon"></a>.

## Step 2. Installing the SDK
{: #install}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

Begin with your own Node.js project, and define this work as your dependency. In other words, put {{site.data.keyword.cloudant_short_notm}} in your package.json dependencies. Use the `npm` package manager from the command line:

```
npm install --save @cloudant/cloudant
```
{: pre}

Notice that your package.json now reflects this package.

## Step 3. Initializing the SDK
{: #initialize}

After you initialize the SDK in your app, you can begin leveraging {{site.data.keyword.cloudant_short_notm}} to store data. Initialize your connection by supplying your credentials and providing a callback function to run when everything is ready.

1. Load the client library by adding the following `require` definition to your `server.js` file.
  ```js
  var Cloudant = require('@cloudant/cloudant');
  ```
  {: codeblock}

2. Initialize the client library by supplying your credentials. Use the `iamauth` plug-in to create a database client with an IAM API key. 
  ```js
  var cloudant = new Cloudant({ url: 'https://examples.cloudant.com', plugins: { iamauth: { iamApiKey: 'xxxxxxxxxx' } } });
  ```
  {: codeblock}

3. List the databases by adding the following code to your `server.js` file.
  ```javascript
  cloudant.db.list(function(err, body) {
  body.forEach(function(db) {
    console.log(db);
    });
  });
  ```
  {: codeblock}

Uppercase `Cloudant` is the package that you load by using `require()`, while lowercase `cloudant` represents an authenticated connection to your {{site.data.keyword.cloudant_short_notm}} service.
{: tip}

### Managing data with basic operations
{: #basic_operations}
<!--Borrowed from https://github.com/cloudant/nodejs-cloudant/blob/master/example/crud.js-->

These basic operations illustrate the actions to create, read, update, and delete your documents by using the initialized client.

#### Creating a document
```js
var createDocument = function(callback) {
  console.log("Creating document 'mydoc'");
  // specify the id of the document so you can update and delete it later
  db.insert({ _id: 'mydoc', a: 1, b: 'two' }, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

#### Reading a document
```js
var readDocument = function(callback) {
  console.log("Reading document 'mydoc'");
  db.get('mydoc', function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep a copy of the doc so you know its revision token
    doc = data;
    callback(err, data);
  });
};
```
{: codeblock}

#### Updating a document
```js
var updateDocument = function(callback) {
  console.log("Updating document 'mydoc'");
  // make a change to the document, using the copy we kept from reading it back
  doc.c = true;
  db.insert(doc, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep the revision of the update so we can delete it
    doc._rev = data.rev;
    callback(err, data);
  });
};
```
{: codeblock}

#### Deleting a document
```js
var deleteDocument = function(callback) {
  console.log("Deleting document 'mydoc'");
  // supply the id and revision to be deleted
  db.destroy(doc._id, doc._rev, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

## Step 4. Testing your app
{: #test}

Is everything set-up correctly? Test it out!

1. Run your application, making sure to invoke the initialization and respective operations, such as creating a document.
2. Return to the {{site.data.keyword.cloudant_short_notm}} service instance that you previously created in your web browser, and open the service dashboard.
3. Select the database that is used to see your newly created documents in the dashboard.

Having trouble? Check out the [{{site.data.keyword.cloudant_short_notm}} API Reference](/docs/services/Cloudant/api/index.html#api-reference-overview){:new_window}.

## Next steps
{: #next notoc}

Great job! You added a level of secure persistence to your app. Keep the momentum by trying one of the following options:

* View the <a href="https://github.com/cloudant/nodejs-cloudant" target="_blank">{{site.data.keyword.cloudant_short_notm}} SDK for Node.js <img src="../icons/launch-glyph.svg" alt="External link icon"></a> source code.
* Check out the <a href="https://github.com/cloudant/nodejs-cloudant/tree/master/example" target="_blank">example code for database and document operations <img src="../icons/launch-glyph.svg" alt="External link icon"></a>
* Starter Kits are one of the fastest ways to leverage the capabilities of IBM Cloud. View the available starter kits in the <a href="https://console.bluemix.net/developer/mobile/dashboard" target="_blank">Mobile developer dashboard <img src="../icons/launch-glyph.svg" alt="External link icon"></a>. Download the code. Run the app!
* To learn more about and take advantage of all of the features that {{site.data.keyword.cloudant_short_notm}} has to offer, [check out the docs](/docs/services/Cloudant/getting-started.html)!
