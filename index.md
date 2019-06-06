---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-06"

keywords: node getting started, node cloud native, create node app, add node service, node programming guide, node guide

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Getting started tutorial
{: #getting-started}

The following tutorial walks you through the steps to build, run locally, and deploy a Node.js app by using {{site.data.keyword.cloud_notm}} provided tools. You can use the [{{site.data.keyword.dev_cli_long}}](/docs/cli?topic=cloud-cli-ibmcloud-cli) on the command line or the web-based [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") as shown in the following tutorial steps. By using either of these methods, you can generate a production-ready Node.js application in just minutes.

Make sure that you are using the latest Node.js LTS release.

## Creating a Node.js app
{: #node-create-project}

1. From the [Starter Kits](https://cloud.ibm.com/developer/appservice/starter-kits){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") page in the {{site.data.keyword.dev_console}}, select a starter kit that is written in `Node.js`. You can also create a blank starter app by clicking **Create app** and selecting `Node.js` as the language.

    You must be logged in to an {{site.data.keyword.cloud_notm}} account to create an app. If you do not have an account, you can [register for a free account](https://cloud.ibm.com/registration){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon").
    {: tip}

2. Click **Create app**.
3. **Name** your app. A generic app name is provided if you want to use that.
4. Click **Create**. After your project is created, you can deploy by using a toolchain or you can continue to build, and deploy your project from the command line.
5. To create a deployment toolchain in the dashboard, click **Deploy**. Set up your deployment target according to the instructions for the method you choose:
  * **Deploy to [IBM Kubernetes Service](/docs/containers?topic=containers-app)**. This option creates a cluster of hosts, called worker nodes, to deploy and manage highly available application containers. You can create a cluster or deploy to an existing cluster.
  * **Deploy to Cloud Foundry**. This option deploys your cloud-native app without you needing to manage the underlying infrastructure. If your account has access to {{site.data.keyword.cfee_full_notm}}, you can select a deployer type of either **[Public Cloud](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** or **[Enterprise Environment](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)**, which you can use to create and manage isolated environments for hosting Cloud Foundry applications exclusively for your enterprise.
  * **Deploy to a [Virtual Server](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server)**. This option provisions a virtual server instance, loads an image that includes your app, creates a DevOps toolchain, and initiates the first deployment cycle for you.

6. Finalize your options, and then click **Create** to create the toolchain.
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

1. Return to your project in the {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}}.
2. Click **Add service**, select the category of the service you want to add, click **Next**, then choose your service. For example, to add a NoSQL database to your application, select the **Data** category, then select **Cloudant**, which offers a lite plan for free development. {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} provisions the service for you based on the selected plan.
Note: If you previously provisioned the service that you plan to use, choose the **Existing** category.
3. After the service is provisioned, click **Download code** to regenerate the project with the SDK that connects to your service.

<!--
<video of creating a project and adding a service>
-->

## Running apps locally
{: #node-run-local}

1. Use the `ibmcloud dev build` command to build your application.
2. Use the `ibmcloud dev run` command to run the application locally. Your application is run in the Docker containers on your local system. You can test your application in a browser by accessing `http://localhost:3000`.
3. A health check endpoint is available at `http://localhost:3000/health`.
4. You can access the [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/){: new_window} ![External link icon](../icons/launch-glyph.svg "External link icon") dashboard at `http://localhost:3000/appmetrics-dash`.

<!--
<video>
-->

## Deploying to IBM Cloud
{: #deploy_cloud}

Use the `ibmcloud dev deploy` command to deploy to {{site.data.keyword.cloud_notm}} as a Cloud Foundry application. 

To deploy to IBM Container Services in {{site.data.keyword.cloud_notm}}, use the following command:
```
ibmcloud dev deploy –target container 
```
{: codeblock}

## Next Steps
{: #node-tutorial-nextsteps notoc}

Continue checking out the topics the Node.js programming guide, or for more advanced deployments, you can learn to create a Kubernetes cluster and deploy your Node.js app to it.

### Set up a Kubernetes cluster
For more information about setting up a Kubernetes cluster in {{site.data.keyword.cloud_notm}}, check out the [tutorial steps](/docs/containers?topic=containers-clusters).

### Deploy Node.js apps to Kubernetes cluster
Learn how to [deploy Node.js apps to a Kubernetes cluster](/docs/containers?topic=containers-cs_apps_tutorial).
