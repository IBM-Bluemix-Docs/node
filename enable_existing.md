---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-08"

keywords: ibmcloud dev enable, nodejs cloud deployment, cloud enable node, deploy node, build node, node debug

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Enabling existing Node.js applications for cloud deployment
{: #enable_existing}

You can generate the files that are needed to enable your Node.js application to run on {{site.data.keyword.cloud}} by using the [{{site.data.keyword.dev_cli_long}} CLI enable command](/docs/cli/idt?topic=cloud-cli-idt-cli#enable).

## Enabling your application
{: #enable_app}

Enter the following command from the root directory of your Node project:
```
ibmcloud dev enable
```
{: codeblock}

You're prompted you to verify the Node.js project, reply `y`. The `enable` command can also create services, and bind them to your application. For this basic example, reply `n`.

See the following sample output:
```
demo: $ ibmcloud dev enable
The enable feature is currently in Beta.
Please provide your experience and feedback at: https://ibm-cloud-tech.slack.com/messages/developer-tools/
Only server-side applications are supported by the enable feature
? Node application discovered. Do you want to proceed with this language choice? [y/n]> y

Using the resource group default (default) of your account
? Do you want to create a service and connect it to this application? [y/n]> n
                                    
The following files were added to your application:

.cfignore
.dockerignore
Dockerfile-tools
Dockerfile
Jenkinsfile
README.md.merge
cli-config.yml
run-debug
manifest.yml
run-dev
.bluemix/deploy.json
.bluemix/pipeline.yml
.bluemix/toolchain.yml
.bluemix/scripts/container_build.sh
.bluemix/scripts/kube_deploy.sh
chart/nodeapp/Chart.yaml
chart/nodeapp/bindings.yaml
chart/nodeapp/values.yaml
chart/nodeapp/templates/hpa.yaml
chart/nodeapp/templates/service.yaml
chart/nodeapp/templates/deployment.yaml
chart/nodeapp/templates/basedeployment.yaml
chart/nodeapp/templates/istio.yaml
.gitignore.merge
LICENSE


The following file conflicts need to be merged manually:

README.md.merge
.gitignore.merge

Files can be easily compared with 'git diff' or a similar tool.
The application, nodeapp, has been successfully saved into the current directory.
demo: $
```
{: screen}

## Build and deploy a cloud enabled Node application
{: #build_deploy}

Next, build your application with the [`build`](/docs/cli/idt?topic=cloud-cli-idt-cli#build) command:
```
ibmcloud dev build
```
{: codeblock}

If the build completes successfully, you can deploy your application to {{site.data.keyword.cloud_notm}} with the following [`deploy`](/docs/cli/idt?topic=cloud-cli-idt-cli#deploy) command:
```
ibmcloud dev deploy
```
{: codeblock}

## What to do if your enabled application does not build or deploy
{: #build_failure}

Not all applications are successfully enabled by the `enable` command. It makes a best effort attempt to determine the configuration that is needed to "cloud enable" your application. At a minimum, the original application needs to be able to run `npm install` and `npm start` successfully. For an application that uses a different run command than `npm start`, you can modify the `CMD` line in `Dockerfile`.

If your application does not build and/or deploy after running `ibmcloud dev enable`, you can modify the generated files manually to complete cloud enablement.

### Manually modifying the generated cloud enablement files
{: #manual_modify}

Once enabled, your application uses docker to run locally. If you do not already have a Dockerfile, one is created for you and port `3000` is exposed.

However, if your application requires a service such as MongoDB or Redis that runs in a separate container, we recommend using `docker-compose` to stand up both your application and your required service simultaneously.

See the following `docker-compose.yml` example that you can create in the root directory:
```yaml
version: '2'
services:
  web:
    build: .
    image: "app-express-run"
    container_name: "app-express-run"
    command: npm run start
    ports:
      - "3000:3000"
    links:
      - mongo
    environment:
      MONGO_URL: "mongo"
      MONGO_DB_NAME: "comments"
      NODE_ENV: "development"
      PORT: 3000
  mongo:
    image: mongo
    ports:
      - "27017:27017" 
```
{: codeblock}

This will expose your application on port `3000` and the MongoDB server will be available for client connection via port `27017`.

You must update the following line in `cli-config.yml` to point to the new Dockerfile: 
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "Dockerfile"
```
{: codeblock}

Update to:
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## Next Steps
{: #next_steps-existing notoc}

For more information, see [IBM Cloud Developer Tools CLI](/docs/cli/idt?topic=cloud-cli-idt-cli#idt-cli).