---

copyright:
  years: 2018
lastupdated: "2018-09-06"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Armazenando conteúdo estático no Object Storage
{: #object}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://console.bluemix.net/docs/services/cloud-object-storage/libraries/node.html#using-node-js -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://console.bluemix.net/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage -->

O {{site.data.keyword.cos_full_notm}} é um componente fundamental da computação em nuvem e fornece recursos poderosos para desenvolvedores da Apple e seus aplicativos. Ao contrário de armazenar informações em uma hierarquia de arquivos (como armazenamento de Bloco ou de Arquivo), um armazenamento de objeto consiste apenas nos arquivos e seus metadados. Esses arquivos são armazenados em coleções conhecidas como depósitos. Por definição, esses objetos são imutáveis, o que os torna perfeitos para dados como imagens, vídeos e outros documentos estáticos. Para dados que mudam com frequência ou são dados relacionais, é possível usar o serviço de banco de dados do [{{site.data.keyword.cloudant_short_notm}}](/docs/node/cloudant.html).

{{site.data.keyword.cos_short}} (COS) é um sistema de armazenamento que pode ser usado para armazenar dados não estruturados que sejam flexíveis, com custo reduzido e escaláveis. Os dados são acessíveis por meio de SDKs ou usando a interface com o usuário da IBM. É possível usar o {{site.data.keyword.cos_short}} para acessar os dados não estruturados por meio de um portal de autoatendimento que é suportado por APIs de RESTful e SDKs.

## Antes de começar
{: #before}

Verifique se os pré-requisitos a seguir estão prontos:
1. Deve-se ter uma conta do [{{site.data.keyword.cloud_notm}} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}.
2. Deve-se ter o [{{site.data.keyword.cos_short}} SDK for Node.js ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/ibm/ibm-cos-sdk-js){: new_window}.
3. Você deve ter o Nó 4.x +.
4. Localize os valores da chave de credencial a serem usados posteriormente para inicialização do SDK:

    * _**endpoint**_ - O terminal público para seu Cloud Object Storage. O terminal está disponível no [Painel do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/apps){: new_window}.
    * _**api-key**_ - A chave API gerada quando as credenciais de serviço são criadas. O acesso de gravação é necessário para exemplos de criação e exclusão.
    * _**resource-instance-id**_ - O ID do recurso para seu Cloud Object Storage. O ID do recurso fica disponível por meio da [CLI do {{site.data.keyword.cloud_notm}}](../cli/index.html) ou do [painel do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/apps){: new_window}.

## Etapa 1. Criando uma instância do  {{site.data.keyword.cos_short}}
{: #create-instance}

1. No [catálogo do {{site.data.keyword.cloud_notm}}](https://console.bluemix.net/catalog/), selecione a categoria **Armazenamento** e clique em {{site.data.keyword.cos_short}}. A página de configuração de serviço é aberta.
2. Dê um nome à sua instância de serviço ou use o nome de pré-configuração.
3. Selecione o seu plano de precificação e clique em **Criar**. A página da instância do Object Storage é aberta.
4. No menu de navegação, selecione **Credenciais de serviço**.
5. Na página Credenciais de serviço, clique em **Nova credencial**.
6. Na página Incluir nova credencial, certifique-se de que a função esteja configurada como **Gravador** e, em seguida, clique em **Incluir.** A nova credencial é criada e mostrada na página Credenciais de serviço.

## Etapa 2. Instalando o SDK
{: #install}

Instale o {{site.data.keyword.cos_short}} SDK for Node.js usando o gerenciador de pacote [npm](https://nodejs.org/) por meio da linha de comandos:
```
npm install ibm-cos-sdk
```
{: pre}

## Etapa 3. Inicializando o SDK
{: #initialize}

Depois de inicializar o SDK em seu app, é possível usar o {{site.data.keyword.cos_short}} para armazenar dados. Inicialize sua conexão fornecendo suas credenciais e fornecendo uma função de retorno de chamada para executar quando tudo estiver pronto.

1. Carregue a biblioteca do cliente incluindo as definições de `require` a seguir para seu arquivo `server.js`.
  ```js
  var objectStore = require ('ibm-cos-sdk');
  ```
  {: codeblock}

2. Inicialize a biblioteca do cliente fornecendo suas credenciais.
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

  Se precisar de ajuda para localizar os valores da chave de credencial para seu app, verifique a *etapa 4* da seção [Antes de iniciar](object_storage.html#before) para obter detalhes sobre sua localização.
  {: tip}

3. Inclua o código a seguir no arquivo `server.js`.
  ```js
  var cos = new objectStore (config);
  ```
  {: codeblock}

### Gerenciando dados com operações básicas
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### Criando um depósito
```js
function doCreateBucket () {
    console.log('Creating bucket');
    return cos.createBucket({
        Bucket: 'my-bucket',
        CreateBucketConfiguration: {
          LocationConstraint: 'us-standard'
        },
    }).promise();
}
```
{: codeblock}

#### Criando / Fazendo Upload ou sobrescrevendo um objeto
```js
function doCreateObject () {
    console.log('Creating object');
    return cos.putObject({
        Bucket: 'my-bucket',
        Key: 'foo',
        Body: 'bar'
    }) .promessa (); }
```
{: codeblock}

#### Fazendo download de um objeto
<!-- Verify this snippet with Nick when he returns from vacation -->
```js
function doGetObject () {
 console.log('Getting object');
 return cos.getObject({
   Bucket: 'my-bucket',
   Key: 'foo'
 } ).createReadStream ().pipe (fs.createWriteStream ('./MyObject ')); }
```
{: codeblock}

#### Excluindo um Objeto
```js
function doDeleteObject () {
    console.log('Deleting object');
    return cos.deleteObject({
        Bucket: 'my-bucket',
        Key: 'foo'
    }) .promessa (); }
```
{: codeblock}

Verifique a [documentação completa](/docs/services/cloud-object-storage/libraries/node.html#using-node-js) para uploads com múltiplas partes, recursos de segurança e outras operações.

## Etapa 4. Testando o app
{: #test}

Está tudo configurado corretamente? Teste-o para fora!

1. Execute o aplicativo, certificando-se de começar a inicialização e as respectivas operações, tais como criar um depósito e incluir dados nele.
2. Retorne para a instância de serviço do {{site.data.keyword.cos_short}} criada anteriormente no navegador da web e abra o painel de serviço.
3. Selecione o depósito usado e você verá os objetos recém-criados no painel.

Tendo problemas? Verifique a [Referência de API do {{site.data.keyword.cos_short}}](/docs/services/cloud-object-storage/api-reference/about-api.html){:new_window}.

## Etapas seguintes
{: #next notoc}

Ótimo trabalho! Você incluiu um nível de persistência segura em seu app. Tente uma das opções a seguir para manter o ritmo:

* Visualize o código-fonte do [{{site.data.keyword.cos_short}} SDK for Node.js ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/ibm/ibm-cos-sdk-js){:new_window}.
* Verifique o [código de exemplo para operações de depósito e de objeto ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/ibm/ibm-cos-sdk-js#example-code){:new_window}.
* Os Kits de iniciador são uma das maneiras mais rápidas de usar os recursos do {{site.data.keyword.cloud_notm}}. Visualize os kits de iniciador disponíveis no [Painel do desenvolvedor de dispositivos móveis ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://console.bluemix.net/developer/mobile/dashboard){:new_window}. Faça download do código. Execute o app!
* Para saber mais e aproveitar todos os recursos oferecidos pelo {{site.data.keyword.cos_short}}, [verifique os docs](/docs/services/cloud-object-storage/about-cos.html)!
