---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-10"

keywords: cos nodejs, object storage nodejs, nodejs data, file storage nodejs, ibm-cos-sdk nodejs, creating object nodejs, downloading object nodejs, static nodejs

subcollection: nodejs

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Almacenamiento de contenido estático en Object Storage
{: #object-storage}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://cloud.ibm.com/docs/services/cloud-object-storage/libraries?topic=cloud-object-storage-node -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://cloud.ibm.com/docs/services/cloud-object-storage?topic=cloud-object-storage-about -->

{{site.data.keyword.cos_full_notm}} es un componente fundamental de la computación en la nube y proporciona potentes funciones a los desarrolladores de Apple y sus aplicaciones. A diferencia del almacenamiento de información en una jerarquía de archivos (por ejemplo, almacenamiento en bloque o archivo), un almacén de objetos consta únicamente de los archivos y sus metadatos. Estos archivos están almacenados en colecciones conocidas como grupos (buckets). Por definición, estos objetos son inmutables, lo que los convierte en perfectos para datos como imágenes, vídeos y otros documentos estáticos. Para los datos que cambian con frecuencia o que son relacionales, puede utilizar el servicio de base de datos [{{site.data.keyword.cloudant_short_notm}}](/docs/node?topic=nodejs-cloudant).

{{site.data.keyword.cos_short}} (COS) es un sistema de almacenamiento que se puede utilizar para almacenar datos no estructurados que son flexibles, rentables y escalables. Se puede acceder a los datos a través de SDK o mediante la interfaz de usuario de IBM. Puede utilizar {{site.data.keyword.cos_short}} para acceder a los datos no estructurados a través de un portal de autoservicio respaldado por las API y los SDK RESTful.

## Antes de empezar
{: #prereqs-cos}

Asegúrese de que dispone de los siguientes requisitos previos listos para utilizar:
1. Debe tener una [cuenta de {{site.data.keyword.cloud}}](https://cloud.ibm.com/registration){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
2. Debe tener el [SDK de {{site.data.keyword.cos_short}} para Node.js](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
3. Debe tener Node 4.x +.
4. Localice los valores de clave de credenciales que se utilizarán posteriormente para la inicialización del SDK:

    * _**endpoint**_: Punto final público para Cloud Object Storage. El punto final está disponible en el [panel de control de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/resources){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
    * _**api-key**_: La clave de API que se genera cuando se crean las credenciales de servicio. Es necesario tener acceso de escritura para crear y suprimir ejemplos.
    * _**resource-instance-id**_: el ID de recurso para Cloud Object Storage. El ID de recurso está disponible a través de la [CLI de {{site.data.keyword.cloud_notm}}](/docs/cli?topic=cloud-cli-getting-started) o del [Panel de control de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/resources){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").

## Paso 1. Creación de una instancia de {{site.data.keyword.cos_short}}
{: #create-instance-cos}

1. En el [catálogo de {{site.data.keyword.cloud_notm}}](https://cloud.ibm.com/catalog){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"), seleccione la categoría **Almacenamiento** y pulse {{site.data.keyword.cos_short}}. Se abre la página de configuración del servicio.
2. Dé un nombre a la instancia de servicio, o utilice el nombre preestablecido.
3. Seleccione el plan de precios y pulse **Crear**. Se abrirá la página de la instancia de Object Storage instance.
4. En el menú de navegación, seleccione **Credenciales de servicio**.
5. En la página Credenciales de servicio, pulse **Nueva credencial**.
6. En la página Añadir nueva credencial, asegúrese de que el rol esté establecido en **Escritor** y, a continuación, pulse **Añadir**. La nueva credencial se crea y se muestra en la página Credenciales de servicio.

## Paso 2. Instalación del SDK
{: #install-cos}

Instale el SDK de {{site.data.keyword.cos_short}} para Node.js mediante el gestor de paquetes [npm](https://nodejs.org/en/){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") desde la línea de mandatos:
```
npm install ibm-cos-sdk
```
{: pre}

## Paso 3. Inicialización del SDK
{: #initialize-cos}

Después de inicializar el SDK en la app, puede utilizar {{site.data.keyword.cos_short}} para almacenar datos. Inicialice la conexión suministrando las credenciales y proporcionando una función de devolución de llamada para que se ejecute cuando todo esté listo.

1. Cargue la biblioteca de cliente añadiendo las siguientes definiciones de `require` en el archivo `server.js`.
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. Inicialice la biblioteca de cliente suministrando las credenciales.
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

  Si necesita ayuda para encontrar los valores de clave de credenciales para la app, consulte el *paso 4* de la sección [Antes de empezar](#prereqs-cos) para obtener detalles sobre dónde encontrarlos. Consulte también [Credenciales de servicio para {{site.data.keyword.cos_short}}](/docs/services/cloud-object-storage/iam?topic=cloud-object-storage-service-credentials).
  {: tip}

3. Añada el código siguiente al archivo `server.js`.
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

### Gestión de datos con operaciones básicas
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### Creación de un grupo
```js
function doCreateBucket() {
    console.log('Creating bucket');
    return cos.createBucket({
        Bucket: 'my-bucket',
        CreateBucketConfiguration: {
          LocationConstraint: 'us-standard'
        },
    }).promise();
}
```
{: codeblock}

#### Creación, carga y modificación de un objeto
```js
function doCreateObject() {
    console.log('Creating object');
    return cos.putObject({
        Bucket: 'my-bucket',
        Key: 'foo',
        Body: 'bar'
    }).promise();
}
```
{: codeblock}

#### Descarga de un objeto
<!-- Verify this snippet with Nick when he returns from vacation -->
```js
function doGetObject() {
 console.log('Getting object');
 return cos.getObject({
   Bucket: 'my-bucket',
   Key: 'foo'
 }).createReadStream().pipe(fs.createWriteStream('./MyObject'));
}
```
{: codeblock}

#### Supresión de un objeto
```js
function doDeleteObject() {
    console.log('Deleting object');
    return cos.deleteObject({
        Bucket: 'my-bucket',
        Key: 'foo'
    }).promise();
}
```
{: codeblock}

Consulte la [documentación completa](/docs/services/cloud-object-storage?topic=cloud-object-storage-node) para las cargas de varias partes, las características de seguridad y otras operaciones.

## Paso 4. Comprobación de la app
{: #test-cos}

¿Todo se ha configurado correctamente? Pruébelo.

1. Ejecute la aplicación, asegurándose de empezar la inicialización y las operaciones respectivas, como por ejemplo crear un grupo y añadir datos al grupo.
2. Vuelva a la instancia de servicio de {{site.data.keyword.cos_short}} que ha creado anteriormente en el navegador web y abra el panel de control del servicio.
3. Seleccione el grupo que se utiliza, y verá los objetos recién creados en el panel de control.

¿Tiene problemas? Consulte la [Referencia de la API de {{site.data.keyword.cos_short}}](/docs/services/cloud-object-storage?topic=cloud-object-storage-compatibility-api).

## Pasos siguientes
{: #next-cos notoc}

¡Buen trabajo! Ha añadido un nivel de persistencia segura a la app. Mantenga el ritmo probando una de las opciones siguientes:

* Visualice el código fuente de [{{site.data.keyword.cos_short}} SDK for Node.js](https://github.com/ibm/ibm-cos-sdk-js){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
* Consulte el [código de ejemplo para las operaciones de grupo y de objeto](https://github.com/ibm/ibm-cos-sdk-js#example-code){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo").
* Los kits de inicio son una de las formas más rápidas de utilizar las prestaciones de {{site.data.keyword.cloud_notm}}. Vea los kits de inicio disponibles en el [panel de control de desarrollador de Mobile](https://cloud.ibm.com/developer/mobile/dashboard){: new_window} ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo"). Descargue el código. Ejecute la app.
* Para obtener más información y aprovechar todas las características que {{site.data.keyword.cos_short}} ofrece, [consulte la documentación](/docs/services/cloud-object-storage?topic=cloud-object-storage-about).
