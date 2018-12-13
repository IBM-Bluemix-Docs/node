---

copyright:
  years: 2018
lastupdated: "2018-10-08"

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

Para obter mais informações sobre todas as maneiras que é possível usar o {{site.data.keyword.appid_short_notm}} e as informações de arquitetura, consulte [Sobre o {{site.data.keyword.appid_short_notm}}](/docs/services/appid/about.html).

## Antes de começar
{: #before}

Verifique se os pré-requisitos a seguir estão prontos:
1. Deve-se ter uma conta do [{{site.data.keyword.cloud}} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}.
2. Instale a [CLI do {{site.data.keyword.cloud_notm}}](../cli/index.html).
3. Instale o suporte de gerenciamento de pacote [npm ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://nodejs.org/){: new_window}.
4. Implemente o servidor Node.js com a [Estrutura do Express ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](http://expressjs.com/){: new_window}. Para instalar a estrutura Express, use a linha de comandos para abrir o diretório com o aplicativo Node.js e execute o comando a seguir:
  ```
  npm install -- save express
  ```
  {: codeblock}

5. Instale o Passport. Use a linha de comandos para abrir o diretório com o aplicativo Node.js e execute o comando a seguir:
  ```
  npm install -- save passport
  ```
  {: codeblock}

  **Nota**: outras estruturas usam estruturas `Express`, como LoopBack. É possível usar o SDK do servidor
{{site.data.keyword.appid_short_notm}} com qualquer uma dessas estruturas.

6. Localize os valores da chave de credencial a serem usados posteriormente para inicialização do SDK:

    * _**tenantID**_ - O ID do locatário é um identificador exclusivo usado para inicializar seu app. É possível localizar o valor no painel de serviço do {{site.data.keyword.appid_short_notm}} clicando em **Visualizar credenciais** na guia **Credenciais de serviço**.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_ - É possível localizar esses valores clicando em **Visualizar credenciais** na guia **Credenciais de serviço** de seu painel de serviço.
    * _**CALLBACK_URL**_ - A URL da página que os usuários veem depois de efetuar login.
    * _**redirectUri**_ - O valor `redirectUri` pode ser fornecido de três maneiras:
      * Manualmente no novo `WebAppStrategy({redirectUri: "...."})`
      * Como uma variável de ambiente chamada  ` redirectUri `. 
      * Se o valor `redirectUri` não for fornecido, o SDK do ID do App recuperará o `application_uri` do aplicativo que está em execução no {{site.data.keyword.cloud_notm}} e anexará o sufixo padrão `/ibm/cloud/appid/callback`.

## Etapa 1. Criando uma instância do  {{site.data.keyword.appid_short_notm}}
{: #create-instance}

** Provisão de uma instância do serviço **
1. No [Catálogo do {{site.data.keyword.cloud_notm}}](https://console.bluemix.net/catalog/), selecione a categoria **Web e dispositivo móvel** e clique em {{site.data.keyword.appid_short_notm}}. A página de configuração de serviço é aberta.
2. Dê um nome à sua instância de serviço ou use o nome de pré-configuração.
3. Selecione o seu plano de precificação e clique em **Criar**.

## Etapa 2. Instalando o SDK
{: #install}

1. Usando a linha de comandos, abra o diretório como seu app Node.js.
2. Instale o serviço do  {{site.data.keyword.appid_short_notm}} .
  ```
  npm install -- save ibmcloud-appid
  ```
  {: codeblock}

## Etapa 3. Inicializando o SDK
{: #initialize}

1. Inclua as definições `require` no arquivo `server.js`.
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. Configurar seu app express para usar o middleware express-session. **Nota**: deve-se configurar o middleware com o armazenamento de sessão adequado para ambientes de produção. Para obter mais informações, consulte os [docs de expressjs ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/expressjs/session){: new_window}.
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

    Se precisar de ajuda para localizar os valores da chave de credencial para seu app, verifique a *etapa 5* da seção [Antes de iniciar](app_id.html#before) para obter detalhes sobre onde localizá-los. 
    {: tip}

4. Configure o passaporte com serialização e desserialização. Essa etapa de configuração é necessária para a persistência de sessão autenticada nas solicitações de HTTP. Para obter mais informações, consulte os [docs de passaporte ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](http://passportjs.org/docs){: new_window}.
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

  **Nota**: o serviço redireciona na ordem a seguir:
  1. A URL original da solicitação que acionou o processo de autenticação é persistida na sessão HTTP sob a chave `WebAppStrategy.ORIGINAL_URL`.
  2. Um redirecionamento bem-sucedido, conforme especificado em `passport.authenticate(name, {successRedirect: "...."})`.
  3. O diretório raiz do aplicativo ("/").

Para obter mais informações, consulte o [Repositório GitHub Node.js do {{site.data.keyword.appid_short_notm}} ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window}.

## Etapa 4. Gerenciando a experiência de conexão
{: #manage-sign-in}

<!--The following content is similar to https://console.stage1.bluemix.net/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

O {{site.data.keyword.appid_full}} fornece um widget de login para que você dê a seus usuários opções de conexão seguras.

Quando seu app estiver configurado para usar um provedor de identidade, os visitantes de seu app serão direcionados para uma página de conexão pelo widget de login. Por padrão, quando somente um provedor é configurado como **Ativo**, os visitantes são redirecionados para a página de autenticação desse provedor de identidade. Com o widget de login, é possível exibir uma página de conexão padrão, ou com o diretório da nuvem, é possível reutilizar suas IUs existentes.

Um provedor de identidade fornece as informações sobre autenticação para seus usuários para que seja possível autorizá-los. Com o {{site.data.keyword.appid_short_notm}}, é possível usar provedores de identidade social, como o Facebook e o Google+, ou gerenciar um registro do usuário com o Cloud Directory.

É possível atualizar seu fluxo de conexão em qualquer momento, sem mudar seu código-fonte de qualquer forma!
{: tip}

O serviço usa os tipos de concessão `OAuth 2` para mapear o processo de autorização. Quando você configura provedores de identidade social, como o Facebook, o [Fluxo de concessão de autorização Oauth2 ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} é usado para chamar o widget de login. Ao exibir suas próprias páginas da IU, o [Fluxo de credenciais de senha do proprietário do recurso ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} é usado para efetuar login, obter acesso e tokens de identidade.

Depois de configurar as definições para [provedores de identidade social](/docs/services/appid/identity-providers.html) e o [Cloud Directory](/docs/services/appid/cloud-directory.html), é possível iniciar a implementação do código.

### Configurando provedores de identidade social
{: #social-identity}

Para configurar provedores de identidade social, conclua as etapas a seguir:

1. Abra o painel do {{site.data.keyword.appid_short_notm}} para **Provedores de identidade > Gerenciar**.
2. Configure os provedores de identidade que você deseja usar como **Ativados**. É possível usar qualquer combinação de provedores de identidade, mas se você quiser trazer páginas de conexão customizadas, ative o diretório de nuvem apenas.
3. Atualize a [configuração padrão](/docs/services/appid/identity-providers.html) com suas próprias credenciais. O {{site.data.keyword.appid_short_notm}} fornece credenciais da IBM que podem ser usadas para experimentar o serviço. Antes de publicar seu app, deve-se atualizar a configuração.
4. [Customize a página de conexão pré-configurada](#login-widget) para exibir a imagem e as cores de sua escolha.

### Configurando o diretório da nuvem
{: #cloud-directory}

Com o {{site.data.keyword.appid_short_notm}}, é possível gerenciar seu próprio registro de usuário chamado diretório da nuvem. O diretório da nuvem permite que os usuários se conectem e se inscrevam nos apps móveis e da web usando seu e-mail e uma senha.

Para configurar o diretório da nuvem, consulte o [diretório da nuvem](/docs/services/appid/cloud-directory.html).

### Customizando a página de conexão padrão
{: #login-widget}

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
{: #default-pages}

O {{site.data.keyword.appid_short_notm}} fornece uma página de login padrão que poderá ser chamada se você não tiver suas próprias páginas da IU para exibir.

Dependendo de sua configuração do provedor de identidade, as páginas que podem ser exibidas são diferentes. O serviço não fornece funções avançadas para provedores de identidade social porque nunca temos acesso a informações da conta de um usuário. Os usuários devem ir para o provedor de identidade para gerenciar suas informações. Por exemplo, se eles quiserem mudar a senha do Facebook, deverão acessar [https://www.facebook.com](https://www.facebook.com).

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
2. Configure seu  [ diretório e configurações de mensagem ](/docs/services/appid/cloud-directory.html).
3. Escolha as combinações de páginas de conexão que gostaria de exibir e coloque o código para chamar essas páginas em seu aplicativo.

**Conectar**
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

**Inscrever**
1. Configure **Permitir que os usuários se inscrevam e reconfigurem sua senha** como **Ativado** nas configurações do Cloud Directory. Se configurado para "não", o processo termina sem recuperar os tokens de acesso e de identidade.
2. Passe a propriedade de WebAppStrategy `show` e configure-a para `WebAppStrategy.SIGN_UP`.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

**Esqueci a senha**
1. Configure **Permitir que os usuários se inscrevam e reconfigurem sua senha** e **E-mail de Esqueci a senha** para **LIGADO** nas configurações do diretório da nuvem. Se configurado para "não", o processo termina sem recuperar os tokens de acesso e de identidade.
2. Passe a propriedade de WebAppStrategy `show` e configure-a para `WebAppStrategy.FORGOT_PASSWORD`.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

**Alterar Senha**
1. Configure **Permitir que os usuários se inscrevam e reconfigurem sua senha** como **Ativado** nas configurações do Cloud Directory.
2. Passe a propriedade de WebAppStrategy `show` e configurá-a para `WebAppStrategy.CHANGE_PASSWORD`.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

**Detalhes da conta**
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

Deseja fornecer aos seus usuários uma experiência customizada? Verifique os [docs do {{site.data.keyword.appid_short_notm}}](/docs/services/appid/login-widget.html#branding) para ver como é possível exibir suas próprias páginas da IU.
{: tip}

## Etapa 5. Testando seu aplicativo
{: #test}

Está tudo configurado corretamente? Você pode testá-lo!

1. Instale as dependências e inicie seu servidor de aplicativos.
2. Usando a GUI, percorra o processo de assinatura em seu aplicativo. Se você configurou o diretório da nuvem, certifique-se de que todas as suas páginas estejam sendo exibidas do modo pretendido.
3. Atualize os provedores de identidade ou a página do widget de login no painel do {{site.data.keyword.appid_short_notm}}. Clique em **Revisar atividade** para ver os eventos de autenticação que ocorreram.
4. Repita as etapas 1 e 2 para ver se as mudanças são implementadas imediatamente. Não são necessárias atualizações para o código do app.

Tendo problemas? Efetue o registro de saída de  [ resolução de problemas  {{site.data.keyword.appid_short_notm}} ](/docs/services/appid/ts_index.html).

## Etapas seguintes
{: #next notoc}

Ótimo trabalho! Você incluiu uma etapa de autenticação em seu app. Tente uma das opções a seguir para manter o ritmo:

* Para saber mais e aproveitar todos os recursos oferecidos pelo {{site.data.keyword.appid_short_notm}}, [verifique os docs](/docs/services/appid/index.html)!
* Os Kits de iniciador são uma das maneiras mais rápidas de usar os recursos do {{site.data.keyword.cloud}}. Visualize os kits de iniciador disponíveis no [Painel do desenvolvedor de dispositivos móveis ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://console.bluemix.net/developer/mobile/dashboard){: new_window}. Faça download do código. Execute o app!
