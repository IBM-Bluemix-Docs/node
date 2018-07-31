---

copyright:
  years: 2018
lastupdated: "2018-07-30"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Deploying and integrating Node.js apps
{: #deploy_apps}

You can deploy your Node.js apps with a toolchain or by using the command line interface. A toolchain is a set of tool integrations. The command line interface is a simple way to deploy your apps and service instances.

For more information, see [Deploying apps](../apps/dep-app-tool.html).

## Developing serverless Node.js apps
{: #serverless}

To facilitate serverless development, you can use IBM's Functions as a Service (FaaS) offering, {{site.data.keyword.openwhisk}}. {{site.data.keyword.openwhisk_short}} enables application logic to be run in response to events or direct invocations from web or mobile apps over HTTP without provisioning or managing servers.

{{site.data.keyword.openwhisk_short}} performs system administration like auto-scaling, availability management, and maintenance, allowing developers to remain focused on writing application logic.

For more information, see [Developing serverless apps](../apps/deploying/functions.html).

## Integrating back-end services with a generated SDK
{: #backend_gensdk}

The {{site.data.keyword.IBM_notm}} SDK Generator plug-in easily integrates back-end services to your app with a generated SDK. When a change to a REST API occurs, you can regenerate the SDK and replace the old one for a seamless SDK upgrade. You can also integrate the CLI into a devops pipeline and ensure that the SDK is always consistent with the API spec each time the app is built.

For more information, see [Integrating back-end services with a generated SDK](../apps/deploying/api_management.html).

## Deploying to a Kubernetes cluster
{: #deploy_kube}

You can learn how to use {{site.data.keyword.cloud_notm}} Kubernetes Service to deploy a containerized Node.js app that leverages Watson Tone Analyzer. In provided scenario, a fictional PR firm uses the {{site.data.keyword.cloud_notm}} service to analyze their press releases and receive feedback on the tone of their messages. For more information, see the tutorial [Deploying apps into clusters](../containers/cs_tutorials_apps.html).

## Deploying to a Virtual Server
{: #virtual_deploy}

A virtual service delivers a higher degree of transparency, predictability, and automation for all workload types. Terraform is used for building, changing, and versioning your infrastructure safely and efficiently.

For more information, see [Deploying to a Virtual Server](../apps/vsi-deploy.html).