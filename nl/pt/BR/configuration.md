---

copyright:
  years: 2018
lastupdated: "2018-09-20"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configurando o ambiente Node.js

Ao implementar princípios nativos da nuvem, um aplicativo Node.js pode ser movido de um ambiente para outro, do teste para a produção, sem mudar o código ou utilizar de outra forma caminhos de código não testados.

O problema surge quando existem diferenças significativas na maneira como a configuração é apresentada, dependendo do ambiente de desenvolvimento. Por exemplo, o CloudFoundry, que usa objetos JSON em sequência versus o Kubernetes que usa valores simples ou objetos JSON em sequência. O desenvolvimento local, além do Kubernetes, também tem considerações diferentes. As credenciais podem ser apresentadas de forma diferente em público e privado, o que dificulta ainda mais que não haja mudança dos apps nos ambientes.

Se você precisa incluir o suporte do {{site.data.keyword.cloud}} em aplicativos existentes ou criar apps com Kits de iniciador, o objetivo é fornecer portabilidade para apps Node.js em qualquer plataforma de desenvolvimento.

## Incluindo a configuração do {{site.data.keyword.cloud_notm}} em aplicativos Node.js existentes
{: #addcloud-env}

O módulo [`ibm-cloud-env`](https://github.com/ibm-developer/ibm-cloud-env) agrega variáveis de ambiente de vários provedores em nuvem, como o CloudFoundry e o Kubernetes, portanto, o aplicativo é independente do ambiente.

### Instalando o módulo  ` ibm-cloud-env `
1. Instale o módulo `ibm-cloud-env` com o comando a seguir:
  ```
  npm install ibm-cloud-env
  ```
  {: codeblock}

2. Inicialize o módulo em seu código referenciando o `mappings.json` assim:
  ```js
  var IBMCloudEnv = require('ibm-cloud-env');
  IBMCloudEnv.init("/path/to/the/mappings/file/relative/to/project/root");
  ```
  {: codeblock}

  Se o caminho do arquivo de mapeamentos não for especificado em `IBMCloudEnv.init()`, o módulo tentará carregar os mapeamentos de um caminho padrão de `/server/config/mappings.json`.
  {: tip}

  Arquivo de exemplo  ` mappings.json ` :
  ```json
  {
    "service1-credentials": {
        "searchPatterns": [
            "cloudfoundry:my-service1-instance-name", 
            "env:my-service1-credentials", 
            "file:/localdev/my-service1-credentials.json" 
        ]
    },
    "service2-username": {
        "searchPatterns": [
            "cloudfoundry:$.service2[@.name=='my-service2-instance-name'].credentials.username",
            "env:my-service2-credentials:$.username",
            "file:/localdev/my-service1-credentials.json:$.username" 
        ]
    }
  }
  ```
  {: codeblock}

### Usando os valores em um app Node.js
Recupere os valores em seu aplicativo usando os comandos a seguir.

1. Recupere a variável  ` service1credentials `:
  ```js
  // this will be a dictionary
  var service1credentials = IBMCloudEnv.getDictionary("service1-credentials");
  ```
  {: codeblock}

2. Recuperar a variável  ` service2username `:
  ```js
  var service2username = IBMCloudEnv.getString("service2-username"); // this will be a string
  ```
  {: codeblock}

Agora seu aplicativo pode ser implementado em qualquer ambiente de tempo de execução, abstraindo as diferenças introduzidas por meio de diferentes provedores de cálculo em nuvem.

### Filtrando os valores para tags e rótulos
É possível filtrar credenciais que são geradas pelo módulo com base nas tags de serviço e nos rótulos de serviço, conforme mostrado no exemplo a seguir:
```js
var filtered_credentials = IBMCloudEnv.getCredentialsForServiceLabel('tag', 'label', credentials)); // returns a Json with credentials for specified service tag and label
```
{: codeblock}

## Usando o gerenciador de configuração Node.js por meio dos apps Starter Kit

Apps Node.js que são criados com [Kits de iniciador](https://console.bluemix.net/developer/appservice/starter-kits/) automaticamente são fornecidos com credenciais e configurações necessárias para execução em vários ambientes de implementação na nuvem (CF, K8s, VSI e Functions).

### Entendendo credenciais de serviço

Suas informações de configuração de aplicativo para serviços são armazenadas no arquivo `localdev-config.json` no diretório `/server/config`. O arquivo fica no diretório `.gitignore` para evitar que informações confidenciais sejam armazenadas no Git. As informações de conexão de qualquer serviço configurado que é executado localmente, como nome do usuário, senha e nome do host, são armazenadas nesse arquivo.

O aplicativo usa o gerenciador de configuração para ler as informações de conexão e de configuração do ambiente e desse arquivo. Ele usa um `mappings.json` customizado, que está localizado no diretório `server/config`, para comunicar onde as credenciais podem ser localizadas para cada serviço.

Os aplicativos em execução localmente podem se conectar aos serviços do {{site.data.keyword.cloud_notm}} usando credenciais desvinculadas que são lidas por meio do arquivo `mappings.json`. Se for necessário criar credenciais desvinculadas, será possível fazer isso por meio do console da web do {{site.data.keyword.cloud_notm}} ou usando o comando `cf create-service-key` do [CloudFoundry CLI](https://docs.cloudfoundry.org/cf-cli/).

Quando você enviar seu aplicativo por push para o {{site.data.keyword.cloud_notm}}, esses valores não serão mais usados. Em vez disso, o aplicativo se conecta automaticamente aos serviços ligados usando variáveis de ambiente.

* **Cloud Foundry**: as credenciais de serviço são obtidas da variável de ambiente `VCAP_SERVICES`.

* **Kubernetes**: as credenciais de serviço são obtidas de variáveis de ambiente individuais por serviço.

* **Serviço de contêiner do {{site.data.keyword.cloud_notm}}**: as credenciais de serviço são obtidas de VSIs ou do {{site.data.keyword.openwhisk}} (Openwhisk).


## Próximas Etapas
{: #next_steps notoc}

O `ibm-cloud-config` suporta a procura de valores usando três tipos de padrão de procura: `cloudfoundry`, `env` e `file`. Se quiser verificar outros padrões de procura suportados e exemplos de padrão de procura, verifique a seção [Uso](https://github.com/ibm-developer/ibm-cloud-env#usage).
