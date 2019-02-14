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

# Storing documents in {{site.data.keyword.cloud_notm}}
{: #cloudant}

{{site.data.keyword.cloudantfull}} is a document-oriented Database as a Service (DBaaS). It stores data as documents in JSON format. {{site.data.keyword.cloudant_short_notm}} is built with scalability, high availability, and durability in mind, and is easy to configure for use in Node.js applications. It comes with a wide variety of indexing options that includes MapReduce, {{site.data.keyword.cloudant_short_notm}} Query, full-text indexing, and geospatial indexing. The replication capabilities make it easy to keep data in sync between database clusters, desktop PCs, and mobile devices.
{:shortdesc}

For more information, see [{{site.data.keyword.cloudant_short_notm}} Basics](/docs/services/Cloudant/basics/index.html#cloudant-nosql-db-basics){:new_window}.

## Before you begin
{: #prereqs-cloudant}

Be sure that you have the following prerequisites ready to go:
 * [Nodejs-cloudant ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/cloudant/nodejs-cloudant){:new_window} 2.3.0+ client library.
 * You must have an [{{site.data.keyword.cloud}} account ![External link icon](../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}.
 * To access {{site.data.keyword.cloudant_short_notm}}, you must create a service in the [{{site.data.keyword.cloud_notm}} Dashboard ![External link icon](../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/dashboard/apps){: new_window}, and then launch the {{site.data.keyword.cloudant_short_notm}} Dashboard from that service instance.
 * The code snippets in these instructions use IAM authentication.
 
### Enabling IAM with {{site.data.keyword.cloudant_short_notm}}
{: #enable_IAM-cloudant}

Only new {{site.data.keyword.cloudant_short_notm}} service instances can be used with {{site.data.keyword.cloud_notm}} IAM.

All new {{site.data.keyword.cloudant_short_notm}} service instances are enabled to use {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM) when provisioned. When you provision a new instance from the {{site.data.keyword.cloud_notm}} Catalog, choose the **Use only IAM** authentication method. This mode means that only IAM credentials are provided by service binding and credential generation. You can find more information at [{{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)](/docs/services/Cloudant/guides/iam.html).

## Step 1. Creating an instance of {{site.data.keyword.cloudant_short_notm}}
{: #create-instance-cloudant}

When you create an instance of {{site.data.keyword.cloudant_short_notm}}, you also create the database.

1. Log in to your {{site.data.keyword.cloud_notm}} account.
2. From the [{{site.data.keyword.cloud_notm}} Dashboard ![External link icon](../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/dashboard/apps){: new_window}, click **Create resource**. The {{site.data.keyword.cloud_notm}} Catalog opens.
3. In the [{{site.data.keyword.cloud_notm}} Catalog](https://cloud.ibm.com/catalog/), select the **Databases** category, and then click {{site.data.keyword.cloudant_short_notm}}. The service configuration page opens.
4. Complete the information in the following fields:
  * **Service name** - Either type a name for your service instance, or use the preset name.
  * **Choose a region/location to deploy in** - Select a region in which to deploy your service.
  * **Select a resource group** - Select a resource group, or accept the default.
  * **Available authentication methods** - Select **Use only IAM** for the authentication method.
5. Select your pricing plan, and then click **Create**. The page for your service instance opens.
6. To create a service credential, complete these steps:
  1. From the navigation menu, select **Service credentials**.
  2. Click **New credential**. The Add new credential page opens.
  3. In the Add new credential page, complete the fields, and then click **Add**. The new service credential is added to the service instance.
  4. If you want to view the service credential details, click **View credentials** in the **Actions** column of the new credential.
7. From the navigation menu, select **Manage**, and then click **Launch Cloudant Dashboard**.
8. From the navigation menu, click the **Databases** icon.
9. Click **Create Database**, provide a database name, and then click **Create.** Your database page opens.

If you want to see related information about provisioning an instance of the {{site.data.keyword.cloud_notm}} service, see [Creating an IBM Cloudant instance on IBM Cloud tutorial](/docs/services/Cloudant/tutorials/create_service.html#creating-a-cloudant-nosql-db-instance-on-ibm-cloud){: new_window}.

## Step 2. Installing the SDK
{: #install-cloudant}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

Begin with your own Node.js project, and define this work as your dependency. In other words, put {{site.data.keyword.cloudant_short_notm}} in your package.json dependencies. Use the [npm](https://nodejs.org/) package manager from the command line to install the SDK:
```
npm install --save @cloudant/cloudant
```
{: codeblock}

Notice that your `package.json` file now shows the Cloudant package.

## Step 3. Initializing the SDK
{: #initialize-cloudant}

After you initialize the SDK in your app, you can use {{site.data.keyword.cloudant_short_notm}} to store data. To initialize your connection, enter your credentials and provide a callback function to run when everything is ready.

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

Uppercase `Cloudant` is the package that you load by using `require()`. Lowercase `cloudant` is the authenticated connection to your {{site.data.keyword.cloudant_short_notm}} service.
{: tip}

### Managing data with basic operations
{: #basic_operations-cloudant}
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
{: #test-cloudant}

Is everything set up correctly? Test it out!

1. Run your application, making sure to start the initialization and respective operations, such as creating a document.
2. From the [{{site.data.keyword.cloud_notm}} Dashboard ![External link icon](../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/dashboard/apps){: new_window}, click the {{site.data.keyword.cloudant_short_notm}} service instance that you previously created. When the service instance opens, click **Launch Cloudant Dashboard**.
3. In the {{site.data.keyword.cloudant_short_notm}} Dashboard, select the database where you created the new documents.

Having trouble? Check out the [{{site.data.keyword.cloudant_short_notm}} API Reference](/docs/services/Cloudant/api/index.html#api-reference-overview){:new_window}.

## Next steps
{: #next-cloudant notoc}

Great job! You added a level of secure persistence to your app. Keep the momentum by trying one of the following options:

* View the [{{site.data.keyword.cloudant_short_notm}} SDK for Node.js ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/cloudant/nodejs-cloudant){: new_window} source code.
* Check out the [example code for database and document operations ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/cloudant/nodejs-cloudant/tree/master/example){: new_window}.
* Starter Kits are one of the fastest ways to use the capabilities of {{site.data.keyword.cloud}}. View the available starter kits in the [Mobile developer dashboard ![External link icon](../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/developer/mobile/dashboard){: new_window}. Download the code. Run the app!
* To learn more about and take advantage of all of the features that {{site.data.keyword.cloudant_short_notm}} offers, [check out the docs](/docs/services/Cloudant/getting-started.html)!
