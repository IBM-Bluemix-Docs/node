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

# Usando uma verificação de funcionamento nos apps Node.js
{: #healthcheck}

As verificações de funcionamento fornecem um mecanismo simples para determinar se um aplicativo do lado do servidor está se comportando corretamente. Ambientes em nuvem, como o [Kubernetes](https://www.ibm.com/cloud/container-service) e o [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry), podem ser configurados para pesquisar terminais de funcionamento periodicamente para determinar se uma instância do serviço está pronta para aceitar o tráfego.

## Visão geral da verificação de
{: #overview}

As verificações de funcionamento fornecem um mecanismo simples para determinar se um aplicativo do lado do servidor está se comportando corretamente. Elas geralmente são consumidas por meio de HTTP e usam códigos de retorno padrão para indicar o status de UP ou DOWN. O valor de retorno de uma verificação de funcionamento é variável, mas uma resposta JSON mínima, como `{"status": "UP"}`, é típica.

O Kubernetes tem uma noção diferenciada do funcionamento do processo. Ele define duas análises:

- Uma análise de _**prontidão**_ é usada para indicar se o processo pode manipular solicitações (é roteável).

  O Kubernetes não roteia trabalho para um contêiner com uma análise de prontidão falha. Uma análise de prontidão poderá falhar se um serviço não tiver terminado de iniciar ou estiver ocupado, sobrecarregado ou for incapaz de processar solicitações.

- Uma análise de _**vivacidade**_ é usada para indicar se o processo deve ser reiniciado.

  O Kubernetes para e reinicia um contêiner com uma análise `liveness` com falha. Use as análises de vivacidade para limpar processos em um estado irrecuperável, por exemplo, se a memória estiver esgotada ou se o contêiner não foi parado corretamente após a falha de um processo interno.

Como uma nota para comparação, o Cloud Foundry usa um terminal de funcionamento. Se essa verificação falhar, o processo será reiniciado, mas se ela for bem-sucedida, as solicitações serão roteadas para ele. Neste ambiente, o terminal é minimamente bem-sucedido quando o processo está ativo. Um atraso inicial é configurado para adiar a verificação de funcionamento até que o serviço termine a inicialização para evitar ciclos de reinicialização.

A seguinte tabela fornece diretrizes sobre as respostas que os terminais de prontidão, vivacidade e funcionamento singular devem fornecer.

| Estado    | Prontidão                   | Vivacidade                   | Saúde                    |
|----------|-----------------------------|----------------------------|---------------------------|
|          | Não OK causa nenhum carregamento       | Não OK causa reinicialização      | Não OK causa reinicialização     |
| Iniciando | 503 - Indisponível           | 200 - OK                   | Use o atraso para evitar o teste   |
| Ativo       | 200 - OK                    | 200 - OK                   | 200 - OK                  |
| Parando | 503 - Indisponível           | 200 - OK                   | 503 - Indisponível         |
| Inativo     | 503 - Indisponível           | 503 - Indisponível          | 503 - Indisponível         |
| Com erro  | 500 - Erro do servidor          | 500 - Erro do servidor         | 500 - Erro do servidor        |

## Incluindo uma verificação de funcionamento em um app Node.js existente
{: #add-healthcheck-existing}

Inclua uma verificação mínima de funcionamento ou de vivacidade em um aplicativo existente introduzindo uma nova rota, conforme mostrado no exemplo a seguir:
```js
router.get('/', function (req, res, next) {
    res.json({status: 'UP'});
  });
app.use("/health", router);
```
{: codeblock}

Verifique o status do app com um navegador, acessando o terminal `/health`.

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

## Recomendações para as análises de prontidão e vivacidade

As análises de prontidão podem incluir a viabilidade de conexões com os serviços de recebimento de dados em seu resultado quando não houver um fallback aceitável se o serviço de recebimento de dados estiver indisponível. Isso não significa chamar a verificação de funcionamento que é fornecida pelo serviço de recebimento de dados diretamente, pois a infraestrutura verifica isso para você. Em vez disso, considere verificar o funcionamento das referências existentes que seu aplicativo tem para os serviços de recebimento de dados: essa pode ser uma conexão JMS com o WebSphere MQ ou um consumidor ou produtor Kafka inicializado. Se você verificar a viabilidade de referências internas para os serviços de recebimento de dados, armazene em cache o resultado para minimizar o impacto que a verificação de funcionamento tem no aplicativo.

Uma análise de vivacidade, por contraste, é deliberada sobre o que é verificado, uma vez que uma falha resulta em término imediato do processo. Um terminal HTTP simples que sempre retorna `{"status": "UP"}` com o código de status `200` é uma opção razoável.

### Incluindo suporte para prontidão e vivacidade do Kubernetes

A biblioteca [`cloud-health-connect`](https://github.com/CloudNativeJS/cloud-health-connect) de [CloudNativeJS], fornece uma estrutura para definir terminais de vivacidade e de prontidão separados no Node que permite a composição de origens para o estado de cada terminal.

## Configurando as análises de prontidão e de vivacidade no Kubernetes

Declare as análises de vivacidade e de prontidão juntamente com a implementação do Kubernetes. As duas análises usam os mesmos parâmetros de configuração:

* O kubelet aguarda por `initialDelaySeconds` antes da primeira análise.

* O kubelet analisa o serviço a cada `periodSeconds` segundos. O padrão é 1.

* A análise atinge o tempo limite após `timeoutSeconds` segundos. O valor padrão e o mínimo é 1.

* A análise será bem-sucedida se obtiver êxito `successThreshold` vezes após uma falha. O valor padrão e o mínimo é 1. O valor deve ser 1 para as análises de vivacidade.

* Quando um pod é iniciado e a análise falha, o Kubernetes tenta `failureThreshold` vezes para reiniciar o pod e, em seguida, desiste. O valor mínimo é 1 e o valor padrão é 3.
    - Para uma análise de vivacidade, "desistir" significa reiniciar o pod.
    - Para uma análise de prontidão, "desistir" significa marcar o pod como `Unready`.

Para evitar os ciclos de reinicialização, configure `livenessProbe.initialDelaySeconds` para ser mais longo, com segurança, que a inicialização do serviço. Em seguida, é possível usar um valor mais curto para `readinessProbe.initialDelaySeconds` para rotear as solicitações para o serviço assim que ele estiver pronto.

Consulte a configuração de exemplo a seguir:
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```
{: codeblock}

Para obter mais informações, consulte como [Configurar análises de prontidão e vivacidade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).
