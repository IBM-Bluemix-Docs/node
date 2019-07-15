---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-05"

keywords: ibmcloud dev enable, nodejs cloud deployment, cloud enable nodejs, deploy nodejs, build nodejs cloud, nodejs debug

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Ativando os aplicativos Node.js existentes para a implementação na nuvem
{: #enable_existing}

É possível gerar os arquivos que são necessários para ativar o aplicativo Node.js para execução no {{site.data.keyword.cloud}} usando o [comando enable da CLI do {{site.data.keyword.dev_cli_long}}](/docs/cli/idt?topic=cloud-cli-idt-cli#enable).

## Ativando o aplicativo
{: #enable_app}

Insira o seguinte comando por meio do diretório raiz de seu projeto Node:
```
Ibmcloud dev enable
```
{: codeblock}

Ao receber um prompt para verificar o projeto Node.js, responda `y`. O comando `enable` também pode criar serviços e ligá-los a seu aplicativo. Para esse exemplo básico, responda `n`.

Consulte a seguinte saída de amostra:
```
demo: $ ibmcloud dev enable
The enable feature is currently in Beta.
Please provide your experience and feedback at: https://ibm-cloud-tech.slack.com/messages/developer-tools/
Only server-side applications are supported by the enable feature
? Node application discovered. Do you want to proceed with this language choice? [ y/n ] > y

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

## Construa e implemente um aplicativo Node ativado para nuvem
{: #build_deploy}

Em seguida, construa seu aplicativo com o comando [`build`](/docs/cli/idt?topic=cloud-cli-idt-cli#build):
```
ibmcloud dev build
```
{: codeblock}

Se a construção for concluída com êxito, será possível implementar seu aplicativo no {{site.data.keyword.cloud_notm}} com o comando [`deploy`](/docs/cli/idt?topic=cloud-cli-idt-cli#deploy) a seguir:
```
ibmcloud dev deploy
```
{: codeblock}

## O que fazer se o aplicativo ativado não construir nem implementar
{: #build_failure}

Nem todos os aplicativos são ativados com êxito pelo comando `enable`. Ele tenta ao máximo determinar a configuração que é necessária para "ativar a nuvem" no aplicativo. No mínimo, o aplicativo original precisa ser capaz de executar `npm install` e `npm start` com êxito. Para um aplicativo que usa um comando de execução diferente de `npm start`, é possível modificar a linha `CMD` no `Dockerfile`.

Se o seu aplicativo não construir e/ou implementar após você executar `ibmcloud dev enable`, será possível modificar os arquivos gerados manualmente para concluir a ativação de nuvem.

### Modificando manualmente os arquivos de ativação de nuvem gerados
{: #manual_modify}

Depois de ativado, o aplicativo usa o Docker para executar localmente. Se você ainda não tiver um Dockerfile, um será criado para você, e a porta `3000` será exposta.

No entanto, se o aplicativo requerer um serviço como o MongoDB ou o Redis que é executado em um contêiner separado, recomendamos usar o `docker-compose` para ativar o aplicativo e o serviço necessário simultaneamente.

Consulte o seguinte `docker-compose.yml` de exemplo que você pode criar no diretório raiz:
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

O seu aplicativo é exposto na porta `3000` e o servidor do MongoDB está disponível para conexão do cliente na porta `27017`.

Deve-se atualizar a linha a seguir em `cli-config.yml` para apontar para o novo Dockerfile: 
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "Dockerfile"
```
{: codeblock}

Atualize para:
```yaml
# The name for the dockerfile for the run container
dockerfile-run : "docker-compose.yml"
```
{: codeblock}

## Próximas etapas
{: #next_steps-existing notoc}

Para obter mais informações, consulte a [CLI do IBM Cloud Developer Tools](/docs/cli/idt?topic=cloud-cli-idt-cli#idt-cli).
