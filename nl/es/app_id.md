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

# Adición de autenticación de usuario
{: #authentication}

La seguridad de aplicación se puede complicar de forma increíble. Para la mayoría de los desarrolladores, es una de las partes más difíciles de la creación de una app. ¿Cómo puede estar seguro de que está protegiendo la información de los usuarios? Al integrar {{site.data.keyword.appid_full}} en sus apps, puede proteger recursos y añadir autenticación, incluso aunque no tenga mucha experiencia en seguridad.

Al exigir a los usuarios que inicien sesión en la app, puede almacenar datos de usuario, como preferencias de la app o los perfiles sociales públicos. Luego puede utilizar esos datos para personalizar la experiencia de cada usuario en la app. {{site.data.keyword.appid_short_notm}} proporciona una infraestructura de inicio de sesión, pero también puede utilizar las páginas de inicio de sesión de su propia marca para utilizarlas con el directorio en la nube.

Para obtener más información sobre todas las formas en que puede utilizar {{site.data.keyword.appid_short_notm}} y la información de arquitectura, consulte [Acerca de {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-about#about).

## Antes de empezar
{: #prereqs-appid}

Asegúrese de que dispone de los siguientes requisitos previos listos para utilizar:
1. Debe tener una [cuenta de {{site.data.keyword.cloud}}](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
2. Instale la [CLI de {{site.data.keyword.cloud_notm}}](/docs/cli?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli).
3. Instale el soporte de gestión de paquetes [npm](https://nodejs.org/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
4. Implemente el servidor de Node.js con la [infraestructura Express](http://expressjs.com/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"). Para instalar la infraestructura Express, utilice la línea de mandatos para abrir el directorio con la app Node.js y ejecute el mandato siguiente:
  ```
  npm install --save express
  ```
  {: codeblock}

5. Instale Passport. Utilice la línea de mandatos para abrir el directorio con la app Node.js y ejecute el mandato siguiente:
  ```
  npm install --save passport
  ```
  {: codeblock}

  Otras infraestructuras utilizan infraestructuras `Express`, como LoopBack. Puede utilizar el SDK del servidor de {{site.data.keyword.appid_short_notm}} con cualquiera de estas infraestructuras.
  {: note}

6. Localice los valores de clave de credenciales que se utilizarán posteriormente para la inicialización del SDK:

    * _**tenantID**_: El ID de arrendatario es un identificador exclusivo que se utiliza para inicializar la app. Puede encontrar el valor en el panel de control de servicio de {{site.data.keyword.appid_short_notm}} pulsando **Ver credenciales** en el separador **Credenciales de servicio**.
    * _**clientID**_, _**secret**_, _**oauth-server-url**_: Puede encontrar estos valores pulsando **Ver credenciales** en el separador **Credenciales de servicio** del panel de control del servicio.
    * _**CALLBACK_URL**_: El URL de la página que ven los usuarios después de iniciar sesión.
    * _**redirectUri**_: El valor de `redirectUri` se puede suministrar de tres maneras:
      * Manualmente en un nuevo `WebAppStrategy({redirectUri: "...."})`
      * Como una variable de entorno denominada `redirectUri`. 
      * Si no se proporciona el valor de `redirectUri`, el SDK de ID de app recupera el `application_uri` de la aplicación que se ejecuta en {{site.data.keyword.cloud_notm}} y añade el sufijo predeterminado `/ibm/cloud/appid/callback`.

## Paso 1. Creación de una instancia de {{site.data.keyword.appid_short_notm}}
{: #create-instance-appid}

Suministre una instancia del servicio:

1. En el [catálogo de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/catalog/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), seleccione la categoría **Web y móvil** y pulse {{site.data.keyword.appid_short_notm}}. Se abre la página de configuración del servicio.
2. Dé un nombre a la instancia de servicio, o utilice el nombre preestablecido.
3. Seleccione el plan de precios y pulse **Crear**.

## Paso 2. Instalación del SDK
{: #install-appid}

1. Desde la línea de mandatos, abra el directorio con su app Node.js.
2. Instale el servicio {{site.data.keyword.appid_short_notm}}.
  ```
  npm install --save ibmcloud-appid
  ```
  {: codeblock}

## Paso 3. Inicialización del SDK
{: #initialize-appid}

1. Añada las siguientes definiciones de `require` al archivo `server.js`.
    ```js
    var express = require('express');
    var session = require('express-session')
    var passport = require('passport');
    var WebAppStrategy = require("ibmcloud-appid").WebAppStrategy;
    var CALLBACK_URL = "/ibm/cloud/appid/callback";
    ```
    {: codeblock}

2. Configurar la app express para utilizar el middleware de sesión express. **Nota**: debe configurar el middleware con el almacenamiento de sesión adecuado para entornos de producción. Para obtener más información, consulte la [documentación de expressjs](https://github.com/expressjs/session){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
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

3. Pase el `ID de arrendatario` y las credenciales para inicializar el SDK.
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

    Si necesita ayuda para encontrar los valores de clave de credenciales para la app, consulte el *paso 5* de la sección [Antes de empezar](#prereqs-appid) para obtener detalles sobre dónde encontrarlos. 
    {: tip}

4. Configurar passport con serialización y deserialización. Este paso de configuración es necesario para la persistencia de la sesión autenticada en las solicitudes HTTP. Para obtener más información, consulte la [documentación de passport](http://passportjs.org/docs){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
  ```js
  passport.serializeUser(function(user, cb) {
    cb(null, user);
    });
    
  passport.deserializeUser(function(obj, cb) {
    cb(null, obj);
    });
  ```
  {: codeblock}

5. Añada el código siguiente al archivo `server.js` para emitir las redirecciones de servicio:
  ```js
  app.get(CALLBACK_URL, passport.authenticate(WebAppStrategy.STRATEGY_NAME));
  app.get("/protected", passport.authenticate(WebAppStrategy.STRATEGY_NAME)), function(req, res)
  res.json(req.user);
  }); 
  ```
  {: codeblock}

  El servicio se redirige en el orden siguiente:
  1. El URL original de la solicitud que ha desencadenado el proceso de autenticación persiste en la sesión HTTP en la clave `WebAppStrategy.ORIGINAL_URL`.
  2. Una redirección correcta, tal como se especifica en `passport.authenticate(name, {successRedirect: "...."})`.
  3. El directorio raíz de la aplicación ("/").

Para obtener más información, consulte el [repositorio GitHub de Node.js de {{site.data.keyword.appid_short_notm}}![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://github.com/ibm-cloud-security/appid-serversdk-nodejs){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").

## Paso 4. Gestión de la experiencia de inicio de sesión
{: #manage-signin-appid}

<!--The following content is similar to https://test.cloud.ibm.com/docs/services/appid/login-widget.html#managing-the-sign-in-experience -->

{{site.data.keyword.appid_full}} proporciona un widget de inicio de sesión para dar a los usuarios opciones de inicio de sesión seguras.

Cuando la app está configurada para utilizar un proveedor de identidad, el widget de inicio de sesión dirigirá a los visitantes de su app a una página de inicio de sesión. De forma predeterminada, cuando solo se establece en **Activado** un proveedor, los visitantes serán redirigidos a dicha página de autenticación del proveedor de identidad. Con el widget de inicio de sesión puede mostrar una página de inicio de sesión predeterminada o, con el directorio en la nube, puede reutilizar las IU existentes.

Un proveedor de identidad proporciona la información de autenticación para los usuarios para que pueda autorizarlos. Con {{site.data.keyword.appid_short_notm}}, puede utilizar los proveedores de identidad social como Facebook y Google+ o puede gestionar un registro de usuarios con el directorio en la nube.

Puede actualizar su flujo de inicio de sesión en cualquier momento, sin cambiar el código fuente de ninguna forma.
{: tip}

El servicio utiliza tipos de concesión `OAuth 2` para correlacionar el proceso de autorización. Cuando configura proveedores de identidad social como Facebook, se utiliza el [flujo de otorgamiento de autorización de Oauth2](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/authcode.html){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") para llamar al widget de inicio de sesión. Cuando muestre sus propias pantallas de interfaz de usuario, se utiliza el [flujo de ROPC (credenciales de contraseña de propietario de recurso)](https://oauthlib.readthedocs.io/en/stable/oauth2/grants/password.html){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") para iniciar la sesión y obtener acceso y señales de identidad.

Después de configurar los valores para los [proveedores de identidad social](/docs/services/appid?topic=appid-social#social) y el [Directorio en la nube](/docs/services/appid?topic=appid-cloud-directory#cloud-directory), puede empezar a implementar el código.

### Configuración de los proveedores de identidad social
{: #social-identity-appid}

Para configurar proveedores de identidad social, lleve a cabo los pasos siguientes:

1. Abra el panel de control de {{site.data.keyword.appid_short_notm}} en **Proveedores de identidad > Gestionar**.
2. Establezca los proveedores de identidad que desee utilizar en **Activado**. Puede utilizar cualquier combinación de proveedores de identidad, pero si desea utilizar páginas de inicio de sesión personalizadas, habilite solo el directorio en la nube.
3. Actualice la [configuración predeterminada](/docs/services/appid?topic=appid-social#social) a sus propias credenciales. {{site.data.keyword.appid_short_notm}} proporciona las credenciales de IBM que puede utilizar para probar el servicio. Antes de publicar la app, debe actualizar la configuración.
4. [Personalice la página de inicio de sesión preconfigurada](#login-widget) para mostrar la imagen y los colores de su elección.

### Configuración del directorio en la nube
{: #cloud-directory-appid}

Con {{site.data.keyword.appid_short_notm}}, puede gestionar su propio registro de usuarios denominado directorio en la nube. El directorio en la nube permite a los usuarios registrarse e iniciar sesión en sus apps móviles y web utilizando su correo electrónico y una contraseña.

Para configurar el directorio en la nube, consulte [directorio en la nube](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).

### Personalización de la página de inicio de sesión predeterminada
{: #login-widget-appid}

Puede personalizar la página de inicio de sesión preconfigurada para mostrar el logotipo y los colores de su elección.

Para personalizar la página:

1. Abra el panel de control del servicio de {{site.data.keyword.appid_short_notm}}.
2. Seleccione la sección **Personalización de inicio de sesión**. Puede modificar el aspecto del widget de inicio de sesión para alinearlo con la marca de su empresa.
3. Suba el logotipo de su empresa seleccionando un archivo PNG o JPG del sistema local. El tamaño de imagen recomendado es de 320 x 320 píxeles. El tamaño de archivo máximo es 100 Kb.
4. Seleccione un color de cabecera para el widget desde el selector de color, o especifique el código hexadecimal para otro color.
5. Inspeccione el panel de vista previa y pulse **Guardar cambios** cuando esté satisfecho con las personalizaciones. Aparecerá un mensaje de confirmación.
6. En el navegador, renueve la página de inicio de sesión para verificar los cambios.

### Visualización de las páginas predeterminadas
{: #default-pages-appid}

{{site.data.keyword.appid_short_notm}} proporciona una página de inicio de sesión predeterminada que puede llamar si no tiene sus propias páginas de IU para mostrar.

Dependiendo de la configuración del proveedor de identidad, las páginas que puede visualizar difieren. El servicio no proporciona funciones avanzadas para proveedores de identidad social porque no tenemos acceso nunca a la información de la cuenta de un usuario. Los usuarios deben ir al proveedor de identidad para gestionar su información. Por ejemplo, si desean cambiar su contraseña de Facebook, deben ir a [https://www.facebook.com](https://www.facebook.com){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").

Consulte la tabla siguiente para ver qué páginas puede visualizar para cada tipo de proveedor de identidad.

| Página visualizada | Proveedor de identidad social | Directorio en la nube | 
|:-----|:-----|:-----|
| Iniciar sesión | <img src="images/confirm.png" width="32" alt="Característica disponible" style="width:32px;" /> | <img src="images/confirm.png" width="32" alt="Característica disponible" style="width:32px;" /> |
| Registro |  | <img src="images/confirm.png" width="32" alt="Característica disponible" style="width:32px;" /> |
| Contraseña olvidada |  | <img src="images/confirm.png" width="32" alt="Característica disponible" style="width:32px;" /> |
| Cambio de contraseña |  | <img src="images/confirm.png" width="32" alt="Característica disponible" style="width:32px;" /> |
| Detalles de la cuenta |  | <img src="images/confirm.png" width="32" alt="Característica disponible" style="width:32px;" /> |

Para mostrar las páginas predeterminadas:

1. Abra el panel de control de {{site.data.keyword.appid_short_notm}} en el separador **Gestión de proveedores de identidad** y establezca el directorio en la nube en **Activado**.
2. Configure los [valores de directorios y mensajes](/docs/services/appid?topic=appid-cloud-directory#cloud-directory).
3. Elija las combinaciones de páginas de inicio de sesión que desee mostrar y coloque el código para llamar a dichas páginas en la aplicación.

#### Iniciar sesión

1. Establezca el directorio en la nube en **Activado** en los valores de proveedor de identidad y especifique un punto final de devolución de llamada.
2. Añada una ruta de envío a su app a la que pueda llamarse con los parámetros de nombre de usuario y contraseña e inicie la sesión utilizando la contraseña del propietario del recurso.
  ```js
  app.post("/form/submit", bodyParser.urlencoded({extended: false}), passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
   	successRedirect: LANDING_PAGE_URL,
  	failureRedirect: ROP_LOGIN_PAGE_URL,
  	failureFlash : true //permitir mensajes flash
  }));
  ```
  {: codeblock}

  `WebAppStrategy` permite a los usuarios iniciar sesión en sus apps web con un nombre de usuario y una contraseña. Después de un inicio de sesión satisfactorio, la señal de acceso de un usuario se almacena en la sesión HTTP y está disponible durante la sesión. Una vez que la sesión HTTP se ha destruido o ha caducado, la señal deja de ser válida.
  {: tip}

#### Registro

1. Establezca **Permitir a los usuarios registrarse y restablecer su contraseña** en **Activado** en los valores del directorio en la nube. Si se establece en no, el proceso finaliza sin recuperar las señales de acceso e identidad.
2. Pase la propiedad `show` de WebAppStrategy y establézcala en `WebAppStrategy.SIGN_UP`.
  ```js
  app.get("/sign_up", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.SIGN_UP
  }));
  ```
  {: codeblock}

#### Contraseña olvidada

1. Establezca **Permitir a los usuarios registrarse y restablecer su contraseña** y **Correo electrónico de contraseña olvidada** en **Activado** en los valores del directorio en la nube. Si se establece en no, el proceso finaliza sin recuperar las señales de acceso e identidad.
2. Pase la propiedad `show` de WebAppStrategy y establézcala en `WebAppStrategy.FORGOT_PASSWORD`.
  ```js
  app.get("/forgot_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.FORGOT_PASSWORD
  }));
  ```
  {: codeblock}

#### Cambio de contraseña

1. Establezca **Permitir a los usuarios registrarse y restablecer su contraseña** en **Activado** en los valores del directorio en la nube.
2. Pase la propiedad `show` de WebAppStrategy y establézcala en `WebAppStrategy.CHANGE_PASSWORD`.
  ```js
  app.get("/change_password", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_PASSWORD
  }));
  ```
  {: codeblock}

#### Detalles de la cuenta

{: #default-account-details}
1. Establezca **Permitir a los usuarios registrarse y restablecer su contraseña** en **Activado** en los valores del directorio en la nube.
2. Pase la propiedad `show` de WebAppStrategy y establézcala en `WebAppStrategy.CHANGE_DETAILS`.
  ```js
  app.get("/change_details", passport.authenticate(WebAppStrategy.STRATEGY_NAME, {
  	successRedirect: LANDING_PAGE_URL,
  	show: WebAppStrategy.CHANGE_DETAILS
  }));
  ```
  {: codeblock}

¿Desea proporcionar a sus usuarios una experiencia personalizada? Consulte la [documentación de {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-login-widget#branding) para ver cómo puede visualizar sus propias páginas de interfaz de usuario.
{: tip}

## Paso 5. Comprobación de la app
{: #test-appid}

¿Todo se ha configurado correctamente? Puede probarlo ahora.

1. Instale las dependencias e inicie el servidor de aplicaciones.
2. Mediante la GUI, vaya a través del proceso de inicio de sesión en la aplicación. Si ha configurado el directorio en la nube, asegúrese de que todas las páginas se visualicen como tiene previsto.
3. Actualice los proveedores de identidades o la página del widget de inicio de sesión en el panel de control de {{site.data.keyword.appid_short_notm}}. Pulse **Revisar actividad** para ver los sucesos de autenticación que se han producido.
4. Repita los pasos 1 y 2 para ver que los cambios se implementan inmediatamente. No es necesario realizar actualizaciones en el código de la app.

¿Tiene problemas? Consulte [resolución de problemas de {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-troubleshooting#troubleshooting).

## Pasos siguientes
{: #next-appid notoc}

¡Buen trabajo! Ha añadido un paso de autenticación a la app. Mantenga el ritmo probando una de las opciones siguientes:

* Para obtener más información y aproveche todas las características que {{site.data.keyword.appid_short_notm}} ofrece, [consulte los documentos](/docs/services/appid?topic=appid-getting-started#getting-started).
* Los kits de inicio son una de las formas más rápidas de utilizar las prestaciones de {{site.data.keyword.cloud}}. Vea los kits de inicio disponibles en el [panel de control de desarrollador de Mobile](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"). Descargue el código. Ejecute la app.
