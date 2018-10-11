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

# Usando a Verificação de funcionamento em apps Node.js
{: #healthcheck}

As verificações de funcionamento fornecem um mecanismo simples para determinar se um aplicativo do lado do servidor está se comportando corretamente. Muitos ambientes de implementação, como o [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry) e o [Kubernetes](https://www.ibm.com/cloud/container-service), podem ser configurados para pesquisar terminais de funcionamento periodicamente para determinar se uma instância de seu serviço está pronta para aceitar tráfego.

## Visão geral da verificação de
{: #overview}

As verificações de funcionamento são geralmente acessadas por meio de `HTTP` e usam códigos de retorno padrão para indicar o status `UP` ou `DOWN`. Os exemplos incluem o retorno de `200` para `UP` e `5xx` para `DOWN`. Por exemplo, um código de retorno `503` será usado quando o aplicativo não puder manipular solicitações e um código de retorno `500` será usado quando o servidor apresentar uma condição de erro. O valor de retorno de uma verificação de funcionamento é variável, mas uma resposta JSON mínima, como `{“status”: “UP”}` fornece consistência.

O Kubernetes define as análises de [atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) e [prontidão](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/):

* A análise de atividade permite que um aplicativo indique se o processo pode ser reiniciado. Um subconjunto de considerações se aplica: uma verificação de `liveness` poderá falhar se um limite de memória for atingido, por exemplo. Se o seu app estiver em execução no Kubernetes, considere incluir um terminal `liviness` para assegurar que seu processo seja reiniciado quando necessário.

* A análise de prontidão é usada para decisões de roteamento automáticas. O sucesso ou falha indica se o aplicativo pode receber novo trabalho. Esse terminal pode incluir considerações para os serviços de recebimento de dados necessários. Considere armazenar em cache o resultado de verificações de serviço de recebimento de dados para minimizar a carga geral no sistema (por exemplo, a conectividade do banco de dados de teste no máximo uma vez por segundo).

O Cloud Foundry usa apenas um terminal de funcionamento. Se essa verificação falhar, o Cloud Foundry reiniciará o processo. Mas se for bem-sucedido, o Cloud Foundry assumirá que o processo pode manipular um novo trabalho. Essa verificação de funcionamento torna o único terminal uma combinação entre atividade e prontidão.

## Incluindo verificação de funcionamento em um app Node.js existente
{: #add-healthcheck-existing}

Para incluir um terminal de verificação de funcionamento em um app existente, comece introduzindo uma nova rota, conforme mostrado no exemplo a seguir:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

A inclusão de critérios significativos aqui assegura que uma resposta bem-sucedida do terminal `/health` corretamente indica que o serviço está pronto para manipular solicitações.

## Acessando a verificação de funcionamento de apps Node.js Starter Kit
{: #healthcheck-starterkit}

Por padrão, quando você gera um app Node.js usando um Kit de Iniciador, um terminal de verificação de funcionamento básico (não autorizado) está disponível em `/health` para verificar o status do app (UP/DOWN).

O código do terminal de verificação de funcionamento é fornecido pelo arquivo `/server/routers/health.js` a seguir:
```js
var express = require('express');

module.exports = function (app) {
  var router = express.Router ();

  router.get('/', function (req, res, next) {
    res.json ({); });

  app.use ("/health", router); }
```
{: codeblock}

É possível customizar a verificação para assegurar-se de que ela seja retornada com sucesso apenas quando o aplicativo estiver pronto para aceitar tráfego.
{: tip}
