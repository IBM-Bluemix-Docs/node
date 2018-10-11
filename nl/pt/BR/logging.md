---

copyright:
  years: 2018
lastupdated: "2018-09-18"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Efetuando login no Node.js
{: #logging_nodejs}

Mensagens de log são sequências com informações contextuais sobre o estado e a atividade do microsserviço no momento em que a entrada de log é feita. Os logs são necessários para diagnosticar como e por que os serviços falham e desempenham uma função de suporte para [appmetrics](appmetrics.html) no funcionamento do aplicativo de monitoramento.

Dada a natureza temporária de processos em ambientes de nuvem, os logs devem ser coletados e enviados em outro lugar, geralmente para um local centralizado para análise. A maneira mais consistente de efetuar login em ambientes de nuvem é enviar entradas de log para fluxos de saída e erro padrão, deixando a infraestrutura manipular o restante.

## Incluindo suporte do Log4js no app Node.js
{: #add_log4j}

[Log4js](https://github.com/log4js-node/log4js-node) é uma estrutura de criação de log popular para Node.js e fornece muitos benefícios nativos, que incluem: 
* Registrando em `stdout` ou `stderr`
* Várias opções de anexação
* Layout e padrões configuráveis de mensagem de log
* Usando Níveis de log para diferentes categorias de log

1. Para usar Log4js, execute o comando [npm](https://nodejs.org/) a seguir no diretório-raiz de seu aplicativo, que instala o pacote e o inclui em seu arquivo `package.json`.
  ```bash
  npm install -- save log4js
  ```
  {: codeblock}

2. Para usá-lo em seu aplicativo, inclua as linhas de código a seguir:
  ```javascript
  var log4js = require('log4js');
  var log = log4js.getLogger();
  log.level = 'debug';
  log.debug("My Debug message");
  ```
  {: codeblock}

  ** Saída stdout **:
  ```
  [2018-07-21 10:23:57.881] [DEBUG] [default] - My Debug message
  ```
  {: screen}

Para obter mais informações sobre a customização de mensagens de log com anexadores, Níveis de log e detalhes de configuração, consulte a [documentação do nó log4js](https://log4js-node.github.io/log4js-node/) oficial.

## Monitorando Logs com o App Service
{: #monitoring}

Os apps Node.js criados usando o {{site.data.keyword.cloud_notm}} [App Service](https://console.bluemix.net/developer/appservice/dashboard) são fornecidos com o Log4js por padrão. Executar o app nativamente ou em um ambiente de nuvem produz saída como: `2018-07-26 12:40:15.121] [INFO] MyAppName - MyAppName listening on http://localhost:3000`. É possível visualizar a saída como a seguir:
* Use  ` stdout `  quando você executar localmente.
* Nos logs para implementações do [CloudFoundry](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs) e do [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/), que são acessados por `ibmcloud app logs --recent <APP_NAME>`  e  ` logs kubectl <deployment name>`.

No arquivo `server/server.js`, é possível ver o código a seguir:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Por padrão, o Nível de log é configurado como `INFO` e pode ser substituído pela variável de ambiente **LOG_LEVEL** do aplicativo.
{: tip}

## Próximas Etapas
{: #next_steps notoc}

Saiba mais sobre como visualizar os logs em cada um dos nossos ambientes de implementação:
* [ Logs de Kubernetes ](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [ Logs do Cloud Foundry ](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#ibmcloud_app_logs)
* [ {{site.data.keyword.openwhisk}}  Logs & Monitoring ](https://console.bluemix.net/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Usando um agregador de log:
* [ {{site.data.keyword.cloud_notm}}  Análise do Log ](https://console.bluemix.net/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [ {{site.data.keyword.cloud_notm}}  Pilha ELK privada ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html)
