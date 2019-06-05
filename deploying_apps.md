---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-05"

keywords: deploy nodejs app, serverless nodejs, back-end nodejs, generated sdk nodejs, cloud foundry deploy nodejs, kubernetes deploy nodejs, virtual nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Deploying and integrating Node.js apps
{: #deploy_apps-nodejs}

You can deploy your Node.js apps with a toolchain or by using the command-line interface. A toolchain is a set of tool integrations. The command-line interface is a simple way to deploy your apps and service instances.

For more information, see [Deploying apps](/docs/apps?topic=creating-apps-deploying-apps).

## Developing serverless Node.js apps
{: #serverless-nodejs}

To facilitate serverless development, you can use the IBM Functions as a Service (FaaS) offering, {{site.data.keyword.openwhisk}}. {{site.data.keyword.openwhisk_short}} enables application logic to be run in response to events or direct invocations from web or mobile apps over HTTP without provisioning or managing servers.

{{site.data.keyword.openwhisk_short}} performs system administration like auto-scaling, availability management, and maintenance, allowing developers to remain focused on writing application logic.

For more information, see [Developing serverless apps](/docs/apps/deploying?topic=creating-apps-serverless).

## Integrating back-end services with a generated SDK
{: #backend_gensdk}

The {{site.data.keyword.IBM_notm}} SDK Generator plug-in easily integrates back-end services to your app with a generated SDK. When a change to a REST API occurs, you can regenerate the SDK and replace the old one for a seamless SDK upgrade. You can also integrate the CLI into a DevOps pipeline and ensure that the SDK is always consistent with the API spec each time the app is built.

For more information, see [Integrating back-end services with a generated SDK](/docs/swift/backend?topic=swift-sdkgen-cli).

## Deploying to a Kubernetes cluster
{: #deploy_kube}

You can learn how to use {{site.data.keyword.cloud_notm}} Kubernetes Service to deploy a containerized Node.js app that uses Watson Tone Analyzer. In provided scenario, a fictional PR firm uses the {{site.data.keyword.cloud_notm}} service to analyze their press releases and receive feedback on the tone of their messages. For more information, see the tutorial [Deploying apps into Kubernetes clusters](/docs/containers?topic=containers-cs_apps_tutorial).

## Deploying to Cloud Foundry
{: #node-deploy-cf}

This option deploys your cloud-native app without you needing to manage the underlying infrastructure.

If you plan to deploy your app to [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry?topic=cloud-foundry-about), you must [prepare your {{site.data.keyword.cloud_notm}} account](/docs/cloud-foundry?topic=cloud-foundry-prepare).

If your account has access to {{site.data.keyword.cfee_full_notm}}, you can select a deployer type of either **[Public Cloud](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** or **[Enterprise Environment](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)**, which you can use to create and manage isolated environments for hosting Cloud Foundry applications exclusively for your enterprise.

## Deploying to a Virtual Server
{: #virtual_deploy}

A virtual service delivers a higher degree of transparency, predictability, and automation for all workload types. Terraform is used for building, changing, and versioning your infrastructure safely and efficiently.

For more information, see [Deploying to a Virtual Server](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server).