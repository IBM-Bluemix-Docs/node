---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-04"

keywords: nodejs storage, nodejs cloudant, nodejs iam, initialize sdk nodejs, test nodejs app, dbaas nodejs, nodejs-cloudant, store documents nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Almacenamiento de documentos en {{site.data.keyword.cloud_notm}}
{: #cloudant}

{{site.data.keyword.cloudantfull}} es una DBaaS (Database as a Service) orientada a documentos. Almacena los datos como documentos en formato JSON. {{site.data.keyword.cloudant_short_notm}} se ha creado teniendo en cuenta la escalabilidad, la alta disponibilidad y la durabilidad, y es fácil configurarlo para utilizarlo en aplicaciones Node.js. Incluye una amplia gama de opciones de indexación, como MapReduce, {{site.data.keyword.cloudant_short_notm}} Query, indexación de texto completo e indexación geoespacial. Las capacidades de réplica permiten mantener fácilmente los datos sincronizados entre los clústeres de bases de datos, los equipos de sobremesa y los dispositivos móviles.
{:shortdesc}

Para obtener más información, consulte el apartado [Conceptos básicos de {{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant/basics?topic=cloudant-ibm-cloudant-basics#ibm-cloudant-basics).

## Antes de empezar
{: #prereqs-cloudant}

Asegúrese de que dispone de los siguientes requisitos previos listos para utilizar:
 * Biblioteca de cliente de [Nodejs-cloudant](https://github.com/cloudant/nodejs-cloudant){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") 2.3.0+.
 * Debe tener una [cuenta de {{site.data.keyword.cloud}}](https://cloud.ibm.com/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
 * Para acceder a {{site.data.keyword.cloudant_short_notm}}, debe crear un servicio en el [panel de control de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/dashboard/apps){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") y luego iniciar el panel de control de {{site.data.keyword.cloudant_short_notm}} desde esa instancia de servicio.
 * Los fragmentos de código de estas instrucciones utilizan la autenticación de IAM.
 
### Habilitación de IAM con {{site.data.keyword.cloudant_short_notm}}
{: #enable_IAM-cloudant}

Sólo se pueden utilizar las nuevas instancias de servicio de {{site.data.keyword.cloudant_short_notm}} con {{site.data.keyword.cloud_notm}} IAM.

Todas las nuevas instancias de servicio de {{site.data.keyword.cloudant_short_notm}} están habilitadas para utilizar {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM) cuando se suministran. Cuando suministre una nueva instancia desde el Catálogo de {{site.data.keyword.cloud_notm}}, seleccione el método de autenticación **Utilizar sólo IAM**. Esta modalidad significa que el enlace de servicio y la generación de credenciales sólo proporcionan las credenciales de IAM. Encontrará más información en [{{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)](/docs/services/Cloudant/guides?topic=cloudant-ibm-cloud-identity-and-access-management-iam-#ibm-cloud-identity-and-access-management-iam-).

## Paso 1. Creación de una instancia de {{site.data.keyword.cloudant_short_notm}}
{: #create-instance-cloudant}

Cuando se crea una instancia de {{site.data.keyword.cloudant_short_notm}}, también se crea la base de datos.

1. Inicie una sesión en su cuenta de {{site.data.keyword.cloud_notm}}.
2. En el [panel de control de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/dashboard/apps){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), pulse **Crear recurso**. Se abre el Catálogo de {{site.data.keyword.cloud_notm}}.
3. En el [catálogo de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/catalog/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), seleccione la categoría **Bases de datos** y luego pulse {{site.data.keyword.cloudant_short_notm}}. Se abre la página de configuración del servicio.
4. Rellene la información de los siguientes campos:
  * **Nombre de servicio**: escriba un nombre para la instancia de servicio o utilice el nombre preestablecido.
  * **Seleccione una región/ubicación de despliegue**: Seleccione una región en la que desea desplegar el servicio.
  * **Seleccione un grupo de recursos**: Seleccione un grupo de recursos o acepte el valor predeterminado.
  * **Métodos de autenticación disponibles**: Seleccione **Utilizar solo IAM** para el método de autenticación.
5. Seleccione el plan de precios y pulse **Crear**. Se abre la página de la instancia de servicio.
6. Para crear una credencial de servicio, siga estos pasos:
  1. En el menú de navegación, seleccione **Credenciales de servicio**.
  2. Pulse **Nueva credencial**. Se abre la ventana Añadir nueva credencial.
  3. En la página Añadir nueva credencial, rellene los campos y pulse **Añadir**. La nueva credencial de servicio se añade a la instancia de servicio.
  4. Si desea ver los detalles de las credenciales de servicio, pulse **Ver credenciales** en la columna **Acciones** de la nueva credencial.
7. En el menú de navegación, seleccione **Gestionar** y, a continuación, pulse **Iniciar panel de control de Cloudant**.
8. En el menú de navegación, pulse el icono **Bases de datos**.
9. Pulse **Crear base de datos**, especifique el nombre de la base de datos y pulse **Crear**. Se abre la página de la base de datos.

Si desea ver información relacionada sobre el suministro de una instancia del servicio de {{site.data.keyword.cloud_notm}}, consulte la guía de aprendizaje [Creación de una instancia de IBM Cloudant en IBM Cloud](/docs/services/Cloudant/tutorials?topic=cloudant-creating-an-ibm-cloudant-instance-on-ibm-cloud#creating-a-cloudant-nosql-db-instance-on-ibm-cloud).

## Paso 2. Instalación del SDK
{: #install-cloudant}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

Empiece con su propio proyecto de Node.js y defina este trabajo como su dependencia. En otras palabras, ponga {{site.data.keyword.cloudant_short_notm}} en las dependencias de package.json. Utilice el gestor de paquetes [npm](https://nodejs.org/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") desde la línea de mandatos para instalar el SDK:
```
npm install --save @cloudant/cloudant
```
{: codeblock}

Tenga en cuenta que el archivo `package.json` ahora muestra el paquete Cloudant.

## Paso 3. Inicialización del SDK
{: #initialize-cloudant}

Después de inicializar el SDK en la app, puede utilizar {{site.data.keyword.cloudant_short_notm}} para almacenar datos. Para inicializar la conexión, especifique sus credenciales y proporcione una función de devolución de llamada para que se ejecute cuando todo esté listo.

1. Cargue la biblioteca de cliente añadiendo la siguiente definición de `require` en el archivo `server.js`.
  ```js
  var Cloudant = require('@cloudant/cloudant');
  ```
  {: codeblock}

2. Inicialice la biblioteca de cliente suministrando las credenciales. Utilice el plug-in `iamauth` para crear un cliente de base de datos con una clave de API de IAM. 
  ```js
  var cloudant = new Cloudant({ url: 'https://examples.cloudant.com', plugins: { iamauth: { iamApiKey: 'xxxxxxxxxx' } } });
  ```
  {: codeblock}

3. Liste las bases de datos añadiendo el código siguiente al archivo `server.js`.
  ```javascript
  cloudant.db.list(function(err, body) {
  body.forEach(function(db) {
    console.log(db);
    });
  });
  ```
  {: codeblock}

`Cloudant` en mayúsculas es el paquete que se carga utilizando `require ()`. `cloudant` en minúsculas es la conexión autenticada con el servicio {{site.data.keyword.cloudant_short_notm}}.
{: tip}

### Gestión de datos con operaciones básicas
{: #basic_operations-cloudant}
<!--Borrowed from https://github.com/cloudant/nodejs-cloudant/blob/master/example/crud.js-->

Estas operaciones básicas muestran las acciones para crear, leer, actualizar y suprimir sus documentos utilizando el cliente inicializado.

#### Creación de un documento
```js
var createDocument = function(callback) {
  console.log("Creating document 'mydoc'");
  // especificar el ID del documento para que pueda actualizarlo y suprimirlo posteriormente
  db.insert({ _id: 'mydoc', a: 1, b: 'two' }, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

#### Lectura de un documento
```js
var readDocument = function(callback) {
  console.log("Reading document 'mydoc'");
  db.get('mydoc', function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // conservar una copia del documento para que conozca su señal de revisión
    doc = data;
    callback(err, data);
  });
};
```
{: codeblock}

#### Actualización de un documento
```js
var updateDocument = function(callback) {
  console.log("Updating document 'mydoc'");
  // realizar un cambio en el documento utilizando la copia que se ha conservado al leerlo de nuevo
  doc.c = true;
  db.insert(doc, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // conservar la revisión de la actualización para que podamos suprimirla
    doc._rev = data.rev;
    callback(err, data);
  });
};
```
{: codeblock}

#### Supresión de un documento
```js
var deleteDocument = function(callback) {
  console.log("Deleting document 'mydoc'");
  // proporcionar el ID y revisión que se debe suprimir
  db.destroy(doc._id, doc._rev, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

## Paso 4. Comprobación de la app
{: #test-cloudant}

¿Todo se ha configurado correctamente? Pruébelo.

1. Ejecute la aplicación, asegurándose de empezar la inicialización y las operaciones respectivas, como por ejemplo la creación de un documento.
2. En el [panel de control de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/dashboard/apps){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), pulse la instancia de servicio de {{site.data.keyword.cloudant_short_notm}} que ha creado anteriormente. Cuando se abra la instancia de servicio, pulse **Iniciar panel de control de Cloudant**.
3. En el Panel de control de {{site.data.keyword.cloudant_short_notm}}, seleccione la base de datos en la que ha creado los nuevos documentos.

¿Tiene problemas? Revise la [Consulta de API de {{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant?topic=cloudant-api-reference-overview).

## Pasos siguientes
{: #next-cloudant notoc}

¡Buen trabajo! Ha añadido un nivel de persistencia segura a la app. Mantenga el ritmo probando una de las opciones siguientes:

* Visualice el código fuente de [{{site.data.keyword.cloudant_short_notm}} SDK for Node.js](https://github.com/cloudant/nodejs-cloudant){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
* Consulte el [código de ejemplo de operaciones de base de datos y documentos](https://github.com/cloudant/nodejs-cloudant/tree/master/example){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
* Los kits de inicio son una de las formas más rápidas de utilizar las prestaciones de {{site.data.keyword.cloud}}. Vea los kits de inicio disponibles en el [panel de control de desarrollador de Mobile](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"). Descargue el código. Ejecute la app.
* Para obtener más información y aprovechar todas las características que {{site.data.keyword.cloudant_short_notm}} ofrece, [consulte la documentación](/docs/services/Cloudant?topic=cloudant-getting-started-with-cloudant#getting-started-with-cloudant).
