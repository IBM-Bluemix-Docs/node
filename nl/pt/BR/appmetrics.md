---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-11"

keywords: nodejs metrics, application metrics nodejs, node appmetrics, nodejs autoscaling, nodejs dash, appmetrics-dashs nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Usando métricas do aplicativo com apps Node.js
{: #metrics}

Saiba como instalar, acessar e entender as métricas do aplicativo Node.js. É possível monitorar os apps Node.js com o [Node Application Metrics](https://developer.ibm.com/open/projects/node-application-metrics/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo") Dashboard para visualizar o desempenho de seu aplicativo Node.js exibindo as métricas em um front-end baseado na web.
{: shortdesc}

## Identificando problemas visualmente
{: #identify-problems}

Métricas de aplicativo são importantes para monitorar o desempenho de seu aplicativo. Ter uma visualização em tempo real de métricas, como as de CPU, de Memória, de Latência e HTTP, é essencial para assegurar que seu aplicativo seja executado efetivamente ao longo do tempo. É possível usar um serviço de nuvem como o [autoscaling](/docs/services/Auto-Scaling?topic=Auto-Scaling) do Cloud Foundry que depende de métricas para escalar dinamicamente instâncias para corresponder à carga de trabalho atual. Ao usar métricas do aplicativo, você é informado com precisão quando aumentar a escala, reduzir a escala ou limpar instâncias que não são mais necessárias para manter os custos baixos.

As métricas do aplicativo são capturadas como dados de série temporal. A agregação e a visualização de métricas capturadas podem ajudar a identificar problemas comuns de desempenho, como:

* Tempos de resposta lentos de HTTP em algumas ou em todas as rotas
* Rendimento insatisfatório no aplicativo
* Espiões na demanda que causam desaceleração
* Uso de CPU maior que o esperado
* Uso de memória alto ou crescente (potencial fuga de memória)

O Application Metrics Dashboard ([`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window}![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")) integrado também inclui um gráfico para 'Outras solicitações', que mostra a duração da solicitação do banco de dados para os bancos de dados suportados (MongoDB, MySQL, Postgres, LevelDB e Redis), o Socket.IO e os eventos do Riak.

Um Relatório de nó ou uma Captura instantânea de heap podem ser gerados por meio do painel para ativar uma análise mais detalhada.

## Incluindo Métricas em apps Node.js existentes
{: #add-appmetrics-existing}

Inclua recursos de monitoramento em aplicativos Express existentes com o construtor [`appmetrics-dash`](https://github.com/RuntimeTools/appmetrics-dash){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo") para passar várias opções de configuração. Por exemplo, uma das opções usa um servidor existente, em vez de deixar o `appmetrics-dash` iniciar um servidor extra.

### Instalando o painel
{: #install-appmetrics}

1. Por exemplo, use o seguinte aplicativo expresso "Hello World" simples:
  ```js
  var express = require('express')
  var app = express()
  app.get('/', function (req, res) {
      res.send( 'Hello World!')
  })
  app.escute (3000, function () {
      console.log ('Example app atendimento na porta 3000!')
  })
  ```
  {: codeblock}

2. Instale o painel `appmetrics` com o comando [npm](https://nodejs.org/en/){: new_window}![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo") a seguir:
  ```
  npm install appmetrics-dash
  ```
  {: codeblock}

3. Inclua o suporte `appmetrics-dash` em seu app existente incluindo o seguinte código:
  ```js
  var dash = require ('appmetrics-dash') .attach ()
  ```
  {: codeblock}

  Esse comando informa ao `appmetrics-dash` para usar o servidor que já foi criado e incluir um terminal `appmetrics-dash`.

  O código revisado agora é semelhante ao seguinte exemplo:
  ```js
  var express = require('express')
  var app = express()
  var dash = require('appmetrics-dash').attach()
  app.get('/', function (req, res) {
    res.send( 'Hello World!')
  })
  app.escute (3000, function () {
    console.log ('Example app atendimento na porta 3000!')
  })
  ```
  {: codeblock}

## Usando Métricas de Kits do Iniciador
{: #appmetrics-starterkits}

Os aplicativos Node.js criados por meio de Kits de iniciador automaticamente são fornecidos com `appmetrics-dash` e seu painel por padrão, mas devem ser ativados para uso.

O código appmetrics pode ser localizado no arquivo de origem do aplicativo gerado denominado `/app_name/server/server.js`:
```js
// Uncomment following to enable zipkin tracing, tailor to fit your network configuration:
var appzip = require('appmetrics-zipkin')({
    host: 'localhost',
    port: 9411,
    serviceName:'frontend'
});
```
{: codeblock}

## Acessando o painel
{: #access-dashboard}

Depois de iniciar seu aplicativo, acesse `http://<hostname>:<port>/appmetrics-dash` em um navegador.

Use o padrão `localhost:3001/appmetrics-dash` para apps que estão em execução localmente.
{: tip}

A IU do painel de monitoramento Application Metrics for Node.js fornece uma gama de métricas, incluindo solicitações de HTTP e latência de loop de eventos, conforme o que pode ser visualizado no vídeo [Monitorando o Metrics for Node.js](https://www.youtube.com/watch?v=7hV8gKlMYLs&feature=youtu.be){: new_window}![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo").

## Entendendo os dados
{: #understanding-data}

![Painel do Appmetrics](images/appmetricsdash-1.png "Painel do Appmetrics.")

A maioria dos dados é criada como gráficos de linha. Solicitações recebidas HTTP, Solicitações realizadas HTTP e Outras solicitações mostram a duração do evento com relação ao tempo. Rendimento de HTTP mostra as solicitações por segundo. Tempos médios de resposta mostram as cinco solicitações de HTTP recebidas mais usadas que demoraram mais tempo, em média. Os gráficos de CPU e de Memória mostram o uso do sistema e do processo ao longo do tempo. Heap mostra o tamanho máximo de heap e o tamanho de heap usado ao longo do tempo. A Latência de loop de eventos mostra as amostras de latência obtidas em intervalos do loop de eventos Node.js, com um ponto para a latência mais curta, um para a média e um para a mais longa para cada amostra obtida.

Se um gráfico tiver pontos, passar o mouse sobre eles fornecerá informações adicionais. Por exemplo, `HTTP Incoming Requests` mostra o tempo de resposta e a URL solicitada.

Um máximo de 15 minutos de dados é mostrado em todos os gráficos.

Se uma grande quantia de dados estiver sendo produzida pelo aplicativo que está sendo monitorado, o painel será iniciado automaticamente para exibir dados. Ao examinar o gráfico Solicitações de HTTP recebidas novamente, será possível ver que cada ponto mostra todas as solicitações para um período de 2 segundos. A dica de ferramenta a seguir mostra o número total de solicitações junto ao tempo médio gasto e o tempo mais longo. O tempo mais longo é o valor plotado.

![Mostrar a dica de ferramenta](images/tooltip-1.png)




