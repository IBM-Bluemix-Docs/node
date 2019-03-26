---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-18"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Tutorial Introdução

O tutorial a seguir fornece orientação durante as etapas para construir, executar localmente e implementar um app Node.js usando as ferramentas fornecidas pelo {{site.data.keyword.cloud_notm}}. É possível usar o [{{site.data.keyword.dev_cli_long}}](/docs/cli/index.html#ibmcloud-cli) na linha de comandos ou no [{{site.data.keyword.cloud}} {{site.data.keyword.dev_console}}](https://cloud.ibm.com/developer/appservice/dashboard) baseado na web, conforme mostrado nas etapas de tutorial a seguir. Usando qualquer um desses métodos, é possível gerar um aplicativo Node.js pronto para produção em alguns minutos.

Certifique-se de que esteja usando a liberação LTS mais recente do Node.js.

## Criando um app Node.js
{: #create_project}

1. Na página [Kits de iniciador](https://cloud.ibm.com/developer/appservice/starter-kits) no {{site.data.keyword.dev_console}}, selecione um Kit de iniciador que esteja gravado no `Node.js`. Também é possível criar um app iniciador em branco clicando em **Criar app** e selecionando `Node.js` como a linguagem.

    Deve-se estar com login efetuado em uma conta do {{site.data.keyword.cloud_notm}} para criar um app. Se você não tiver uma conta, será possível [registrar-se para uma conta grátis](https://cloud.ibm.com/registration).
    {: tip}

2. Clique em  ** Criar app **.
3. ** Nome **  seu app. Um nome genérico de app será fornecido se você quiser usá-lo.
4. Insira um  ** nome do host exclusivo **. O nome do host é usado para acessar seu aplicativo, por exemplo: `expressjs-project.mybluemix.net`.
5. Clique em  ** Criar **. Após a criação de seu projeto, será possível implementar usando uma cadeia de ferramentas ou continuar construindo e implementando o projeto por meio da linha de comandos.
6. Para criar uma cadeia de ferramentas de implementação no painel, clique em **Implementar na nuvem**. Configure o método de implementação de acordo com as instruções para o método escolhido:
  * **Implemente no [Kubernetes](/docs/apps/deploying/containers.html#containers-kube)**. Essa opção cria um cluster de hosts, chamados de nós do trabalhador, para implementar e gerenciar contêineres de aplicativo altamente disponíveis. É possível criar um cluster ou implementar em um cluster existente.
  * **Implemente no Cloud Foundry**. Essa opção implementa o seu app nativo de nuvem sem você precisar gerenciar a infraestrutura subjacente. Se a sua conta tiver acesso ao {{site.data.keyword.cfee_full_notm}}, será possível selecionar um tipo de implementador do **[Public Cloud](/docs/cloud-foundry-public/about-cf.html#about-cf)** ou do **[Enterprise Environment](/docs/cloud-foundry-public/cfee.html#cfee)**, que é possível usar para criar e gerenciar ambientes isolados para hospedar aplicativos do Cloud Foundry exclusivamente para a sua empresa.
  * **Implemente em um [Servidor virtual](/docs/apps/vsi-deploy.html#vsi-deploy)**. Essa opção provisiona uma instância de servidor virtual, carrega uma imagem que inclui o seu app, cria uma cadeia de ferramentas do DevOps e inicia o primeiro ciclo de implementação para você.

7. Finalize suas opções e, em seguida, clique em **Criar** para criar a cadeia de ferramentas.

8. Se optar por continuar com a CLI, em vez da cadeia de ferramentas, faça download do projeto na máquina local, use `unzip` e `cd` no diretório-raiz. Agora é possível instalar os pré-requisitos usando o método de comando de plataformas:

    MacOS:
    ```
    curl -sL https://ibm.biz/idt-installer | bash
    ```
    {: codeblock}

    Windows (executar como Administrador):
    ```
    Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')
    ```
    {: codeblock}

## Incluindo um serviço
{: #add_service}

1. Retorne ao seu projeto no  {{site.data.keyword.cloud_notm}}  {{site.data.keyword.dev_console}}.
2. Clique em **Incluir serviço**, selecione a categoria do serviço que deseja incluir, clique em **Avançar** e, em seguida, escolha seu serviço. Por exemplo, para incluir um banco de dados NoSQL no aplicativo, clique na categoria **Dados** e, em seguida, selecione **Cloudant**, que oferece um plano Lite para desenvolvimento grátis. O {{site.data.keyword.cloud_notm}} {{site.data.keyword.dev_console}} provisiona o serviço para você com base no plano selecionado.
Nota: se você provisionou anteriormente o serviço que planeja usar, escolha a categoria **Existente**.
3. Depois que o serviço for provisionado, clique em **Fazer download do código** para gerar novamente o projeto com o SDK que se conecta a seu serviço.

<!--
<video of creating a project and adding a service>
-->

## Executando apps localmente
{: #run_local}

1. Use o comando `ibmcloud dev build` para construir seu aplicativo.
2. Use o comando `ibmcloud dev run` para executar o aplicativo localmente. Seu aplicativo é executado em contêineres do Docker no sistema local. É possível testar seu aplicativo em um navegador acessando `http://localhost:3000`.
3. Um terminal de verificação de funcionamento está disponível em `http://localhost:3000/health`.
4. É possível acessar o painel do [App Metrics](https://developer.ibm.com/node/monitoring-post-mortem/application-metrics-node-js/) em `http://localhost:3000/appmetrics-dash`.

<!--
<video>
-->

## Implementando no Cloud
{: #deploy_cloud}

Use o comando `ibmcloud dev deploy` para implementar no {{site.data.keyword.cloud_notm}} como um aplicativo Cloud Foundry. 

Para implementar no IBM Container Services no {{site.data.keyword.cloud_notm}}, use o comando a seguir:
```
ibmcloud dev deploy -target container 
```
{: codeblock}

## Próximas Etapas
{: #nextsteps}

Continue verificando os tópicos do guia de programação do Node.js ou para implementações mais avançadas, é possível aprender a criar um cluster do Kubernetes e implementar seu app Node.js nele.

### Configurar um cluster do Kubernetes
Para obter mais informações sobre como configurar um cluster do Kubernetes no {{site.data.keyword.cloud_notm}}, verifique as [etapas do tutorial](/docs/containers/cs_clusters.html#clusters).

### Implementar apps Node.js no cluster do Kubernetes
Saiba como [implementar apps Node.js em um cluster do Kubernetes](/docs/containers/cs_tutorials_apps.html#cs_apps_tutorial).
