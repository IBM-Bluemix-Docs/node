---

copyright:
  years: 2018, 2020
lastupdated: "2020-05-05"

keywords: node getting started, node cloud native, create node app, add node service, node programming guide, node guide

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:external: target="_blank" .external}

# Getting started tutorial
{: #getting-started}

The following tutorial walks you through the steps to build, run locally, and deploy a Node.js app by using {{site.data.keyword.cloud_notm}} provided tools. You can use the [{{site.data.keyword.dev_cli_short}} (`ibmcloud dev`) commands](/docs/cli?topic=cloud-cli-getting-started) on the command line or the web-based [{{site.data.keyword.cloud_notm}} App Development console](https://{DomainName}/developer/appservice/dashboard){: external} as shown in the following tutorial steps. By using either of these methods, you can generate a production-ready Node.js application in just minutes.

Make sure that you are using the latest Node.js LTS release.

## Creating a Node.js app
{: #node-create-project}

1. Log in to your {{site.data.keyword.cloud_notm}} account. If you don't have an account, you can [register for a free account](https://{DomainName}/registration){: external}.
2. Go to the [{{site.data.keyword.cloud_notm}} App Development console](https://{DomainName}/developer/appservice/starter-kits){: external}, and select a starter kit that is written in `Node.js`, or select the **Create App** tile to use a blank starter kit. Then select the **Create** tab.
3. Name your app, and select a resource group.
4. Optional. Provide tags to classify your app. For more information, see [Working with tags](/docs/resources?topic=resources-tag).
5. Ensure that **Node.js** is selected as the platform, and then click **Create**. After your app is created, you can add services and then deploy it by using a toolchain, or you can continue to build and deploy your project from the [command line](/docs/cli?topic=cloud-cli-getting-started).
7. If you choose to continue with the CLI instead of the toolchain, download the project to your local machine, `unzip`, and `cd` into the root directory. Now you can install the prerequisites by using your platforms command method:

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows (run as Administrator):
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## Adding a service
{: #node-add-service}

1. Return to your app in the {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}.
2. On the App details page, click **Create service** or **Connect existing services**, depending on whether you already have services that you want to connect to this app.
3. Select the kind of service you want, and follow the prompts to either add an existing service to your app or create a service instance. For example, to add a NoSQL database to your application, select the **Databases** category, then select **Cloudant**, which offers a Lite plan for free development. {{site.data.keyword.cloud_notm}} provisions the service for you, based on the selected plan.
4. After the service is provisioned, click **Download code** to regenerate the app with the SDK that connects to your service.

After you add all the services that you want, the services are displayed in the App details page.

After you connect a service to your app, you can navigate to the service documentation and API references. Simply click the links within the **Services** card to view the related docs.

For more information, see [Adding a service to your app](/docs/apps?topic=creating-apps-add-service).

## Running apps locally
{: #node-run-local}

1. Use the `ibmcloud dev build` command to build your application.
2. Use the `ibmcloud dev run` command to run the application locally. Your application is run in the Docker containers on your local system. You can test your application in a browser by accessing `http://localhost:3000`.
3. A health check endpoint is available at `http://localhost:3000/health`.

## Deploying to IBM Cloud
{: #deploy_cloud}

Use the `ibmcloud dev deploy` command to deploy to {{site.data.keyword.cloud_notm}} as a Cloud Foundry application. 

To deploy to IBM Container Services in {{site.data.keyword.cloud_notm}}, use the following command:
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## Next steps
{: #node-tutorial-nextsteps notoc}

Continue checking out the topics the Node.js programming guide, or for more advanced deployments, you can learn to create a Kubernetes cluster and deploy your Node.js app to it. For more information, see [Deploying and integrating Node.js apps](/docs/node?topic=nodejs-deploy_apps-nodejs).

### Set up a Kubernetes cluster
For more information about setting up a Kubernetes cluster in {{site.data.keyword.cloud_notm}}, check out the [tutorial steps](/docs/containers?topic=containers-clusters).
