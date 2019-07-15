---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-13"

keywords: deploy nodejs app, serverless nodejs, back-end nodejs, generated sdk nodejs, cloud foundry deploy nodejs, kubernetes deploy nodejs, virtual nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Implementando e integrando apps Node.js
{: #deploy_apps-nodejs}

É possível implementar os seus apps do Node.js com uma cadeia de ferramentas ou usando a interface da linha de comandos. Uma cadeia de ferramentas é um conjunto de integrações de ferramentas. A interface da linha de comandos é uma maneira simples de implementar os seus apps e as instâncias de serviço.

Para obter mais informações, consulte  [ Implementando apps ](/docs/apps?topic=creating-apps-deploying-apps).

## Desenvolvendo apps Node.js sem servidor
{: #serverless-nodejs}

Para facilitar o desenvolvimento sem servidor, é possível usar a oferta Functions as a Service (FaaS) da IBM, {{site.data.keyword.openwhisk}}. O {{site.data.keyword.openwhisk_short}} permite que a lógica do aplicativo seja executada em resposta a eventos ou chamadas diretas de apps da web ou móveis sobre HTTP sem fornecimento ou gerenciamento de servidores.

O {{site.data.keyword.openwhisk_short}} executa administração do sistema, como auto-scaling, gerenciamento de disponibilidade e manutenção, permitindo que os desenvolvedores permaneçam focados na gravação de lógica do aplicativo.

Para obter mais informações, consulte [Desenvolvendo apps sem servidor](/docs/apps/deploying?topic=creating-apps-serverless).

## Integrando serviços de backend com um SDK gerado
{: #backend_gensdk}

O plug-in {{site.data.keyword.IBM_notm}} SDK Generator integra facilmente os serviços de backend a seu app com um SDK gerado. Quando ocorrer uma mudança em uma API de REST, será possível gerar novamente o SDK e substituir o antigo por um upgrade de SDK sem interrupção. Também é possível integrar a CLI a um pipeline do DevOps e assegurar-se de que o SDK esteja
sempre consistente com a especificação de API toda vez que o app é construído.

Para obter mais informações, consulte [Integrando serviços de backend com um SDK gerado](/docs/swift/backend?topic=swift-sdkgen-cli).

## Implementando em um Cluster do Kubernetes
{: #deploy_kube}

É possível saber como usar o {{site.data.keyword.cloud_notm}} Kubernetes Service para implementar um app Node.js conteinerizado que usa o Watson Tone Analyzer. No cenário fornecido, uma firma PR fictícia usa o serviço {{site.data.keyword.cloud_notm}} para analisar seus press releases e receber feedback sobre o sinal de suas mensagens. Para obter mais informações, consulte o tutorial [Implementando apps em clusters Kubernetes](/docs/containers?topic=containers-cs_apps_tutorial).

## Implementando no Cloud Foundry
{: #node-deploy-cf}

Essa opção implementa o seu app nativo de nuvem sem você precisar gerenciar a infraestrutura subjacente.

Se você planeja implementar seu app no [{{site.data.keyword.cfee_full}}](/docs/cloud-foundry?topic=cloud-foundry-about), deve-se [preparar sua conta do {{site.data.keyword.cloud_notm}}](/docs/cloud-foundry?topic=cloud-foundry-prepare).

Se a sua conta tiver acesso ao {{site.data.keyword.cfee_full_notm}}, será possível selecionar um tipo de implementador do **[Public Cloud](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf)** ou do **[Enterprise Environment](/docs/cloud-foundry-public?topic=cloud-foundry-public-cfee)**, que é possível usar para criar e gerenciar ambientes isolados para hospedar aplicativos do Cloud Foundry exclusivamente para a sua empresa.

## Implementando em um Virtual Server
{: #virtual_deploy}

Um serviço virtual entrega um grau mais alto de transparência, de previsibilidade e de automação para todos os tipos de carga de trabalho. O Terraform é usado para construir, mudar e criar versões de sua infraestrutura de maneira segura e eficiente.

  A implementação do VSI está disponível para alguns kits do iniciador. Para usar esse recurso, acesse o [painel do {{site.data.keyword.cloud_notm}}](https://{DomainName}) e clique em **Criar um app** no ladrilho **Apps**.
  {: note} 

Para obter mais informações, consulte [Implementando em um Virtual Server](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server).
