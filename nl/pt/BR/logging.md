---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Efetuando login no Node.js
{: #logging_nodejs}

Mensagens de log são sequências com informações contextuais sobre o estado e a atividade do microsserviço no momento em que a entrada de log é feita. Os logs são necessários para diagnosticar como e por que os serviços falham e desempenham uma função de suporte para [appmetrics](/docs/node/appmetrics.html#metrics) no funcionamento do aplicativo de monitoramento.

Dada a natureza transitória de processos em ambientes de nuvem, os logs devem ser coletados e enviados para outros lugares, geralmente para um local centralizado para análise. A maneira mais consistente de efetuar login em ambientes de nuvem é enviar entradas de log para fluxos de saída e erro padrão, deixando a infraestrutura manipular o restante.

É possível usar o [Log4js](https://github.com/log4js-node/log4js-node){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo"), que é uma estrutura de criação de log popular para Node.js e fornece muitos benefícios nativos, que incluem: 
* Registrando em `stdout` ou `stderr`
* Várias opções de anexação
* Layout e padrões configuráveis de mensagem de log
* Usando os `Log Levels` para diferentes categorias de log

## Incluindo o suporte do Log4js no app Node.js existente
{: #add_log4j}

1. Primeiro, instale o `log4js` executando o comando [npm](https://nodejs.org/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo") a seguir no diretório-raiz, que instala o pacote e o inclui em seu arquivo `package.json`.
  ```bash
  npm install -- save log4js
  ```
  {: codeblock}

2. Para usá-lo em seu aplicativo, inclua as linhas de código a seguir em seu app:
  ```js
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

3. Para gravar os eventos de log no fluxo de erros padrão, é possível configurar um anexador como no exemplo a seguir:
  ```js
  var log4js = require('log4js');
  
  log4js.configure({
    appenders: { err: { type: 'stderr' } },
    categories: { default: { appenders: ['err'], level: 'ERROR' } }
  });
  ```
  {: codeblock}

  Para obter mais informações sobre a customização de mensagens de log com anexadores, os `Log Levels` e os detalhes de configuração, consulte a [documentação
do log4js-node](https://log4js-node.github.io/log4js-node/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo") oficial.

## Monitorando com os apps do App Service
{: #monitoring}

Os apps Node.js criados usando o {{site.data.keyword.cloud_notm}} [App Service](https://cloud.ibm.com/developer/appservice/dashboard) são fornecidos com o Log4js por padrão. É possível abrir o arquivo `server/server.js` para ver o código Log4js a seguir:
```js
var logger = log4js.getLogger(appName);
var app = express();
app.use(log4js.connectLogger(logger, { level: process.env.LOG_LEVEL || 'info' }));
```
{: codeblock}

Por padrão, o `Log Level` é configurado como `OFF` para que possa ser usado com segurança em bibliotecas, mas ele pode ser substituído pela variável de ambiente **LOG_LEVEL** do aplicativo.
{: tip}

### Visualizando a saída de log
{: #node-view-log-output}

Veja a saída de log de amostra a seguir gerada pela execução do app de forma nativa ou em um ambiente de nuvem:
```
2018-07-26 12:40:15.121 [INFO] MyAppName - MyAppName listening on http://localhost:3000
```
{: screen}

É possível visualizar a saída de log usando os métodos a seguir:
* Para os ambientes locais, use `stdout`.
* Para as implementações do [Cloud Foundry](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html), é possível acessar os logs executando:
  ```
  ibmcloud app logs --recent <APP_NAME>
  ```
  {: codeblock}

* Para as implementações do [Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo"), é possível acessar os logs executando:
  ```
  kubectl logs <deployment name>
  ```
  {: codeblock}

## Próximas Etapas
{: #next_steps-logging notoc}

Saiba mais sobre a visualização de logs em cada ambiente de implementação:
* [Logs do Kubernetes](https://kubernetes.io/docs/concepts/cluster-administration/logging/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")
* [ Logs do Cloud Foundry ](/docs/services/CloudLogAnalysis/cfapps/logging_cf_apps.html#logging_cf_apps)
* [Logs e monitoramento do {{site.data.keyword.openwhisk}} ](/docs/openwhisk/openwhisk_logs.html#openwhisk_logs)

Usando um agregador de log:
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis/log_analysis_ov.html#log_analysis_ov)
* [Pilha ELK do {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")
