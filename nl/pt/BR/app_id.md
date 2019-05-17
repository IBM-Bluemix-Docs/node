---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-09"

keywords: nodejs authentication, nodejs security, nodejs identity provider, nodejs cloud directory, nodejs facebook, nodejs login, nodejs social identity, add security nodejs, nodejs user authentication

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Incluindo Autenticação do Usuário
{: #authentication}

A segurança do aplicativo pode ser muito complicada. Para a maioria dos desenvolvedores, é uma das partes mais difíceis da criação de um app. Como você pode garantir que está protegendo as informações do usuário? Ao integrar o {{site.data.keyword.appid_full}} em seus aplicativos, é possível proteger os recursos e incluir a autenticação, mesmo quando você não tem muita experiência em segurança.

Ao exigir que os usuários se conectem ao app, é possível armazenar dados do usuário, tais como preferências de app ou perfis sociais públicos. Será possível, então, usar esses dados para customizar a experiência de cada usuário dentro do app. O {{site.data.keyword.appid_short_notm}} fornece uma estrutura de login para você, mas também é possível trazer suas próprias páginas de conexão de marca para usar com o Cloud Directory.

Para obter mais informações sobre todas as maneiras que é possível usar o {{site.data.keyword.appid_short_notm}} e as informações de arquitetura, consulte [Sobre o {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-about#about).

## Antes de começar
{: #prereqs-appid}

Verifique se os pré-requisitos a seguir estão prontos:
1. Deve-se ter uma [conta do {{site.data.keyword.cloud}}](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo").
2. Instale a [CLI do {{site.data.keyword.cloud_notm}}](/docs/cli?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli).
3. Instale o suporte de gerenciamento de pacote [npm ](https://nodejs.org/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo").
4. Implemente o servidor Node.js com a [Estrutura do Express ](http://expressjs.com/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo"). Para instalar a estrutura Express, use a linha de comandos para abrir o diretório com o aplicativo Node.js e execute o comando a seguir:
  ```
  npm install -- save express
  ```
  {: codeblock}

5. Instale o Passport. Use a linha de comandos para abrir o diretório com o aplicativo Node.js e execute o comando a seguir:
  ```
  npm install -- save passport
  ```
  {: codeblock}

  Outras estruturas usam estruturas `Express`, como o LoopBack. É possível usar o SDK do servidor
{{site.data.keyword.appid_short_notm}} com qualquer uma dessas estruturas.
  {: note}

6. Localize os valores da chave de credencial a serem usados posteriormente para inicialização do SDK:

    * _**tenantID**_ - O ID do locatário é um identificador exclusivo usado para inicializar seu app. É possível localizar o valor no painel de serviço do {{site.data.keyword.appid_short_notm}} clicando em **Visualizar credenciais** na guia **Credenciais de serviço**.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_ - É possível localizar esses valores clicando em **Visualizar credenciais** na guia **Credenciais de serviço** de seu painel de serviço.
    * _**CALLBACK_URL**_ - A URL da página que os usuários veem depois de efetuar login.
    * _**redirectUri**_ - O valor `redirectUri` pode ser fornecido de três maneiras:
      * Manualmente no novo `WebAppStrategy({redirectUri: "...."})`
      * Como uma variável de ambiente chamada  ` redirectUri `. 
      * Se o valor `redirectUri` não for fornecido, o SDK do ID do App recuperará o `application_uri` do aplicativo que está em execução no {{site.data.keyword.cloud_notm}} e anexará o sufixo padrão `/ibm/cloud/appid/callback`.

## Etapa 1. Criando uma instância do  {{site.data.keyword.appid_short_notm}}
{: #create-instance-appid}

Provisem uma instância do serviço:

1. No [catálogo do {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/catalog/){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo"), selecione a categoria **Web e móvel** e clique em {{site.data.keyword.appid_short_notm}}. A página de configuração de serviço é aberta.
2. Dê um nome à sua instância de serviço ou use o nome de pré-configuração.
3. Selecione o seu plano de precificação e clique em **Criar**.

## Etapa 2. Instalando o SDK
{: #install-appid}

1. Usando a linha de comandos, abra o diretório como seu app Node.js.
2. Instale o serviço do  {{site.data.keyword.appid_short_notm}} .
  ```
  npm install -- save ibmcloud-appid
  ```
  {: codeblock}

## Etapa 3. Inicializando o SDK
{: #initialize-appid}

1. Inclua as definições `require` no arquivo `server.js`.
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. Configurar seu app express para usar o middleware express-session. **Nota**: deve-se configurar o middleware com o armazenamento de sessão adequado para ambientes de produção. Para obter mais informações, consulte os [docs de expressjs ](https://github.com/expressjs/session){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo").
    ```js
    var app = express();
    app.use(session({
        secret: "123456",
        resave: true,
        saveUninitialized: true
        }));
    app.use(passport.initialize());
    app.use(passport.session());
    ```
    {: codeblock}

3. Passe o `tenant ID` e as credenciais para inicializar o SDK.
    ```js
    passport.use(new WebAppStrategy({
        tenantId: "{tenant-id}",
        clientId: "{client-id}",
        secret: "{secret}",
        oauthServerUrl: "{oauth-server-url}",
        redirectUri: "{app-url}" + CALLBACK_URL
    }));
    ```
    {: codeblock}

    Se precisar de ajuda para localizar os valores da chave de credencial para seu app, verifique a *etapa 5* da seção [Antes de iniciar](#prereqs-appid) para obter detalhes sobre onde localizá-los. 
    {: tip}

4. Configure o passaporte com serialização e desserialização. Essa etapa de configuração é necessária para a persistência de sessão autenticada nas solicitações de HTTP. Para obter mais informações, consulte os [docs de passaporte ](http://passportjs.org/docs){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo").
  ```js
  passport.serializeUser (function (user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. Inclua o código a seguir em seu arquivo `server.js` para emitir os redirecionamentos de serviço:
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  O serviço redireciona na ordem a seguir:
  1. A URL original da solicitação que acionou o processo de autenticação é persistida na sessão HTTP sob a chave `WebAppStrategy.ORIGINAL_URL`.
  2. Um redirecionamento bem-sucedido, conforme especificado em `passport.authenticate(name, {successRedirect: "...."})`.
  3. O diretório raiz do aplicativo ("/").

Para obter mais informações, consulte o [repositório GitHub Node.js do {{site.data.keyword.appid_short_notm}} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo").

## Etapa 4. Gerenciando a experiência de conexão
{: #manage-signin-appid}

<!--The following content is similar to https://test.cloud.ibm.com/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

O {{site.data.keyword.appid_full}} fornece um widget de login para que você dê a seus usuários opções de conexão seguras.

Quando seu app estiver configurado para usar um provedor de identidade, os visitantes de seu app serão direcionados para uma página de conexão pelo widget de login. Por padrão, quando somente um provedor é configurado como **Ativo**, os visitantes são redirecionados para a página de autenticação desse provedor de identidade. Com o widget de login, é possível exibir uma página de conexão padrão, ou com o diretório da nuvem, é possível reutilizar suas IUs existentes.

Um provedor de identidade fornece as informações sobre autenticação para seus usuários para que seja possível autorizá-los. Com o {{site.data.keyword.appid_short_notm}}, é possível usar provedores de identidade social, como o Facebook e o Google+, ou gerenciar um registro do usuário com o Cloud Directory.

É possível atualizar seu fluxo de conexão em qualquer momento, sem mudar seu código-fonte de qualquer forma!
{: tip}

O serviço usa os tipos de concessão `OAuth 2` para mapear o processo de autorização. Quando você configura provedores de identidade social, como o Facebook, o [Fluxo de concessão de autorização Oauth2 ](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo") é usado para chamar o widget de login. Ao exibir suas próprias páginas da IU, o [Fluxo de credenciais de senha do proprietário do recurso ](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo") é usado para efetuar login, obter acesso e tokens de identidade.

Depois de configurar as definições para [provedores de identidade social](/docs/services/appid?topic=appid-social#social) e o [Cloud Directory](/docs/services/appid?topic=appid-cloud-directory#cloud-directory), é possível iniciar a implementação do código.

### Configurando provedores de identidade social
{: #social-identity-appid}

Para configurar provedores de identidade social, conclua as etapas a seguir:

1. Abra o painel do {{site.data.keyword.appid_short_notm}} para **Provedores de identidade > Gerenciar**.
2. Configure os provedores de identidade que você deseja usar como **Ativados**. É possível usar qualquer combinação de provedores de identidade, mas se você quiser trazer páginas de conexão customizadas, ative o diretório de nuvem apenas.
3. Atualize a [configuração padrão](/docs/services/appid?topic=appid-social#social) com suas próprias credenciais. O {{site.data.keyword.appid_short_notm}} fornece credenciais da IBM que podem ser usadas para experimentar o serviço. Antes de publicar seu app, deve-se atualizar a configuração.
4. [Customize a página de conexão pré-configurada](#login-widget) para exibir a imagem e as cores de sua escolha.

### Configurando o diretório da nuvem
{: #cloud-directory-appid}

Com o {{site.data.keyword.appid_short_notm}}, é possível gerenciar seu próprio registro de usuário chamado diretório da nuvem. O diretório da nuvem permite que os usuários se conectem e se inscrevam nos apps móveis e da web usando seu e-mail e uma senha.

Para configurar o diretório da nuvem, consulte o [diretório da nuvem](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).

### Customizando a página de conexão padrão
{: #login-widget-appid}

É possível customizar a página de conexão pré-configurada para exibir o logotipo e as cores de sua escolha.

Para customizar a página:

1. Abra o painel
de serviço {{site.data.keyword.appid_short_notm}}.
2. Selecione a seção **Customização de login**. É possível modificar a aparência do widget de login para alinhar com a marca da sua empresa.
3. Faça upload do logotipo da sua empresa selecionando um arquivo PNG ou JPG em seu sistema local. O tamanho da imagem recomendado é 320 x 320 pixels. O tamanho máximo do arquivo é 100 Kb.
4. Selecione uma cor de cabeçalho para o widget no selecionador de cor ou insira o código hexadecimal para outra cor.
5. Inspecione a área de janela de visualização e clique em **Salvar mudanças** quando estiver satisfeito com as suas customizações. Uma mensagem de confirmação é exibida.
6. Em seu navegador, atualize sua página de login para verificar suas mudanças.

### Exibindo as páginas padrão
{: #default-pages-appid}

O {{site.data.keyword.appid_short_notm}} fornece uma página de login padrão que poderá ser chamada se você não tiver suas próprias páginas da IU para exibir.

Dependendo de sua configuração do provedor de identidade, as páginas que podem ser exibidas são diferentes. O serviço não fornece funções avançadas para provedores de identidade social porque nunca temos acesso a informações da conta de um usuário. Os usuários devem ir para o provedor de identidade para gerenciar suas informações. Por exemplo, se eles quiserem mudar a senha do Facebook, deverão acessar [https://www.facebook.com](https://www.facebook.com){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo").

Verifique a tabela a seguir para ver quais páginas você pode exibir para cada tipo de provedor de identidade.

| Página de Exibição | Provedor de identidade social | Diretório da nuvem | 
|:-----|:-----|:-----|
| Efetuar sign in | <img src="images/confirm.png" width="32" alt="Recurso disponível" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="Recurso disponível" style="width:32px;" /> |
| Inscrever |  | <img src="images/confirm.png" width="32" alt="Recurso disponível" style="width:32px;" /> |
| Esqueceu a senha |  | <img src="images/confirm.png" width="32" alt="Recurso disponível" style="width:32px;" /> |
| Alterar Senha |  | <img src="images/confirm.png" width="32" alt="Recurso disponível" style="width:32px;" /> |
| Detalhes da conta |  | <img src="images/confirm.png" width="32" alt="Recurso disponível" style="width:32px;" /> |

Para exibir as páginas padrão:

1. Abra o painel do {{site.data.keyword.appid_short_notm}} na guia **Gerenciando provedores de identidade** e configure o diretório da nuvem como **Ativado**.
2. Configure seu  [ diretório e configurações de mensagem ](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).
3. Escolha as combinações de páginas de conexão que gostaria de exibir e coloque o código para chamar essas páginas em seu aplicativo.

#### Efetuar sign in

1. Configure o diretório da nuvem para **Ligado** em suas configurações de provedor de identidade e especifique um terminal de retorno de chamada.
2. Inclua uma rota de post em seu app que possa ser chamada com o nome do usuário e os parâmetros de senha e efetue login usando a senha do proprietário do recurso.
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
   	failureRedirect: ROP_LOGIN_PAGE_URL,
   	failureFlash : true // allow flash messages
   }));
  ```
  {: codeblock}

  `WebAppStrategy` permite que os usuários se conectem aos apps da web com um nome de usuário e uma senha. Após um login bem-sucedido, o token de acesso de um usuário é armazenado na sessão HTTP e fica disponível durante a sessão. Após a sessão HTTP ser destruída ou expirada, o token é invalidado.
  {: tip}

#### Inscrever

1. Configure **Permitir que os usuários se inscrevam e reconfigurem sua senha** como **Ativado** nas configurações do Cloud Directory. Se configurado para "não", o processo termina sem recuperar os tokens de acesso e de identidade.
2. Passe a propriedade de WebAppStrategy `show` e configure-a para `WebAppStrategy.SIGN_UP`.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

#### Esqueceu a senha

1. Configure **Permitir que os usuários se inscrevam e reconfigurem sua senha** e **E-mail de Esqueci a senha** para **LIGADO** nas configurações do diretório da nuvem. Se configurado para "não", o processo termina sem recuperar os tokens de acesso e de identidade.
2. Passe a propriedade de WebAppStrategy `show` e configure-a para `WebAppStrategy.FORGOT_PASSWORD`.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

#### Alterar Senha

1. Configure **Permitir que os usuários se inscrevam e reconfigurem sua senha** como **Ativado** nas configurações do Cloud Directory.
2. Passe a propriedade de WebAppStrategy `show` e configurá-a para `WebAppStrategy.CHANGE_PASSWORD`.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

#### Detalhes da conta

{: #default-account-details}
1. Configure **Permitir que os usuários se inscrevam e reconfigurem sua senha** para **Ligado** nas configurações do diretório da nuvem
2. Passe a propriedade de WebAppStrategy `show` e configure-a para `WebAppStrategy.CHANGE_DETAILS`.
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

Deseja fornecer aos seus usuários uma experiência customizada? Consulte os [docs do {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-login-widget#branding) para ver como é possível exibir suas próprias páginas da IU.
{: tip}

## Etapa 5. Testando seu aplicativo
{: #test-appid}

Está tudo configurado corretamente? Você pode testá-lo!

1. Instale as dependências e inicie seu servidor de aplicativos.
2. Usando a GUI, percorra o processo de assinatura em seu aplicativo. Se você configurou o diretório da nuvem, certifique-se de que todas as suas páginas estejam sendo exibidas do modo pretendido.
3. Atualize os provedores de identidade ou a página do widget de login no painel do {{site.data.keyword.appid_short_notm}}. Clique em **Revisar atividade** para ver os eventos de autenticação que ocorreram.
4. Repita as etapas 1 e 2 para ver se as mudanças são implementadas imediatamente. Não são necessárias atualizações para o código do app.

Tendo problemas? Efetue o registro de saída de  [ resolução de problemas  {{site.data.keyword.appid_short_notm}} ](/docs/services/appid?topic=appid-troubleshooting#troubleshooting).

## Etapas seguintes
{: #next-appid notoc}

Ótimo trabalho! Você incluiu uma etapa de autenticação em seu app. Tente uma das opções a seguir para manter o ritmo:

* Para saber mais e aproveitar todos os recursos oferecidos pelo {{site.data.keyword.appid_short_notm}}, [verifique os docs](/docs/services/appid?topic=appid-getting-started#getting-started)!
* Os kits do iniciador são uma das maneiras mais rápidas de usar os recursos do {{site.data.keyword.cloud}}. Visualize os kits de iniciador disponíveis no [Painel do desenvolvedor de dispositivos móveis ](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo"). Faça download do código. Execute o app!
