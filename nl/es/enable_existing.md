---

copyright:
  years: 2018
lastupdated: "2018-10-17"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Habilitación de aplicaciones Node.js existentes para despliegue en la nube
{: #enable_existing}

Puede generar los archivos necesarios para hacer que la aplicación Node.js pueda ejecutarse en {{site.data.keyword.cloud}} utilizando el [mandato enable de la CLI de {{site.data.keyword.dev_cli_long}}](https://console.bluemix.net/docs/cli/idt/commands.html#enable).

## Habilitación de la aplicación
{: #enable_app}

Escriba el mandato siguiente desde el directorio raíz del proyecto Node:
```
ibmcloud dev enable
```
{: codeblock}

Se le solicitará que verifique el proyecto Node.js, responda `y` (sí). El mandato `enable` también puede crear servicios y enlazarlos a la aplicación. En este ejemplo básico, responda `n` (no).

Consulte el siguiente resultado de ejemplo:
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

## Compilación y despliegue de una aplicación Node habilitada para la nube
{: #build_deploy}

A continuación, compile la aplicación con el mandato [`build`](/docs/cli/idt/commands.html#build):
```
ibmcloud dev build
```
{: codeblock}

Si la compilación finaliza correctamente, puede desplegar la aplicación en {{site.data.keyword.cloud_notm}} con el siguiente [mandato `deploy`](/docs/cli/idt/commands.html#deploy):
```
ibmcloud dev deploy
```
{: codeblock}

## Qué hacer si la aplicación habilitada no se compila o no se despliega
{: #build_failure}

No todas las aplicaciones se pueden habilitar correctamente con el mandato `enable`. Se debe realizar el esfuerzo de intentar determinar la configuración necesaria para "habilitar para la nube" su aplicación. Como mínimo, la aplicación original debe poder ejecutar `npm install` y `npm start` correctamente. Para una aplicación que utilice un mandato de ejecución distinto de `npm start`, puede modificar la línea `CMD` en `Dockerfile`.

Si la aplicación no se compila o no se despliega tras ejecutar `ibmcloud dev enable`, puede modificar los archivos generados para completar la habilitación para la nube.

### Modificación manual de los archivos de habilitación para la nube
{: #manual_modify}

Una vez habilitada, la aplicación utiliza docker para ejecutarse localmente. Si no tiene un Dockerfile, se crea uno automáticamente y se expone el puerto `3000`.

Sin embargo, si la aplicación requiere un servicio como MongoDB o Redis que se ejecuta en un contenedor separado, se recomienda utilizar `docker-compose` para que configure la aplicación y el servicio requerido simultáneamente.

Consulte el siguiente ejemplo de `docker-compose.yml` que puede crear en el directorio raíz:
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

Esto expondrá la aplicación en el puerto `3000` y el servidor de MongoDB estará disponible para la conexión del cliente por el puerto `27017`.

Debe actualizar la siguiente línea del archivo `cli-config.yml` para que haga referencia al nuevo Dockerfile: 
```yaml
# Nombre del dockerfile para el contenedor de ejecución
dockerfile-run : "Dockerfile"
```
{: codeblock}

Actualícela a:
```yaml
# Nombre del dockerfile para el contenedor de ejecución
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## Pasos siguientes
{: #next_steps notoc}

Para obtener más información, consulte [CLI de IBM Cloud Developer Tools](https://console.bluemix.net/docs/cli/idt/commands.html#idt-cli).
