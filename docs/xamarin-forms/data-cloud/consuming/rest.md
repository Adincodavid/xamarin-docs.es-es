---
title: Consumir un servicio Web RESTful
description: Integrar un servicio web en una aplicación es un escenario común. Este artículo demuestra cómo consumir un servicio web RESTful desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 48b81c5beb1643501c69e5de1ea4f4197d587001
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-a-restful-web-service"></a>Consumir un servicio Web RESTful

_Integrar un servicio web en una aplicación es un escenario común. Este artículo demuestra cómo consumir un servicio web RESTful desde una aplicación de Xamarin.Forms._

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se utilizan para recuperar las páginas web y para enviar datos a los servidores. Los verbos son:

- **OBTENER** : esta operación se usa para recuperar datos desde el servicio web.
- **POST** : esta operación se utiliza para crear un nuevo elemento de datos en el servicio web.
- **COLOCAR** : esta operación se utiliza para actualizar un elemento de datos en el servicio web.
- **REVISIÓN** : esta operación se utiliza para actualizar un elemento de datos en el servicio web mediante la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. Este verbo no se utiliza en la aplicación de ejemplo.
- **ELIMINAR** : esta operación se usa para eliminar un elemento de datos en el servicio web.

Se llama a las API de REST API que se ajustan al resto del servicio Web y se definen mediante:

- Un identificador URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Tipo de medio de los datos, como JavaScript Object Notation (JSON).

Servicios web rESTful normalmente utilizan mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera compact cargas, lo que hace que reducción los requisitos de ancho de banda al enviar datos. La aplicación de ejemplo usa el código abierto [NewtonSoft JSON.NET biblioteca](http://www.newtonsoft.com/json) para serializar y deserializar los mensajes.

La sencillez de REST ha ayudado a hacer el método principal para tener acceso a servicios web en aplicaciones móviles.

Las instrucciones sobre cómo configurar el servicio REST se pueden encontrar en el archivo Léame que acompaña a la aplicación de ejemplo. Sin embargo, si se ejecuta la aplicación de ejemplo, se conectará a un servicio hospedado en Xamarin REST que proporciona acceso de solo lectura a los datos, como se muestra en la siguiente captura de pantalla:

![](rest-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
>Se pueden alta ATS fuera de si no es posible utilizar el **HTTPS** protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizar el servicio Web

El servicio REST se escribe utilizando ASP.NET Core y proporciona las siguientes operaciones:

|Operación|Método HTTP|URI relativo|Parámetros|
|--- |--- |--- |--- |
|Obtener una lista de elementos pendientes|GET|/api/todoitems/|
|Crear un nuevo elemento de tarea|EXPONER|/api/todoitems/|TodoItem en formato JSON|
|Actualizar un elemento de tarea|PUT|/api/todoitems/|TodoItem en formato JSON|
|Eliminar un elemento de tarea|SUPRIMIR|/api/todoitems/{id}|

La mayoría de los URI incluyen el `TodoItem` ID en la ruta de acceso. Por ejemplo, para eliminar la `TodoItem` cuyo identificador es `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, el cliente envía una solicitud de eliminación para `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Para obtener más información acerca del modelo de datos usado en la aplicación de ejemplo, vea [modelar los datos](~/xamarin-forms/data-cloud/walkthrough.md).

Cuando el marco Web API recibe una solicitud, enruta la solicitud a una acción. Estas acciones son métodos públicos simplemente en el `TodoItemsController` clase. El marco de trabajo utiliza una tabla de enrutamiento para determinar qué acción va a invocar en respuesta a una solicitud, que se muestra en el ejemplo de código siguiente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabla de enrutamiento contiene una plantilla de ruta, y cuando el marco Web API recibe una solicitud HTTP, intenta buscar coincidencias con el URI en la plantilla de ruta en la tabla de enrutamiento. Si una coincidencia de ruta no se encuentra que el cliente recibe un error 404 (no encontrado). Si se encuentra una ruta coincidente, API Web selecciona el controlador y la acción, como se indica a continuación:

- Para buscar el controlador, API Web agrega "controller" en el valor de la *{controller}* variable.
- Para encontrar la acción, API Web examina el método HTTP y se examinan las acciones de controlador se decora con el mismo método HTTP como un atributo.
- El *{id}* variable de marcador de posición se asigna a un parámetro de acción.

El servicio REST utiliza la autenticación básica. Para obtener más información, consulte [autenticar un servicio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obtener más información sobre el enrutamiento de ASP.NET Web API, consulte [enrutamiento de ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) en el sitio Web ASP.NET. Para obtener más información acerca de cómo crear el servicio REST mediante ASP.NET Core, vea [crear servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/).

> [!NOTE]
> La aplicación de ejemplo consume el servicio hospedado en Xamarin REST que proporciona acceso de solo lectura para el servicio web. Por lo tanto, las operaciones que crean, actualización y eliminan datos no modificará los datos que se consume en la aplicación. Sin embargo, está disponible en una versión del servicio REST hospedable el **TodoRESTService** carpeta en la que lo acompaña [código de ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/).
> Si hospeda el servicio REST, completa permite crear, actualizar, leer y eliminar el acceso a los datos.

La `HttpClient` clase se utiliza para enviar y recibir solicitudes a través de HTTP. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP desde un URI identifica el recurso. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, vea [Introducción al soporte técnico Async](~/cross-platform/platform/async.md).

La `HttpResponseMessage` clase representa un mensaje de respuesta HTTP recibido desde el servicio web después de que se ha realizado una solicitud HTTP. Contiene información acerca de la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. El `HttpContent` clase representa el cuerpo HTTP y encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer utilizando cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, dependiendo del formato de los datos.

### <a name="creating-the-httpclient-object"></a>Crear el objeto de HTTPClient

El `HttpClient` instancia se declara en el nivel de clase para que se encuentra el objeto para siempre y cuando la aplicación necesita realizar solicitudes HTTP, como se muestra en el ejemplo de código siguiente:

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    client.MaxResponseContentBufferSize = 256000;
  }
  ...
}
```

El `HttpClient.MaxResponseContentBufferSize` propiedad se utiliza para especificar el número máximo de bytes que se va a almacenar en búfer al leer el contenido en el mensaje de respuesta HTTP. El tamaño predeterminado de esta propiedad es el tamaño máximo de un entero. Por lo tanto, la propiedad se establece en un valor inferior, como medida de seguridad, para limitar la cantidad de datos que la aplicación aceptará como una respuesta del servicio web.

### <a name="retrieving-data"></a>Recuperar datos

El `HttpClient.GetAsync` método se usa para enviar la solicitud GET al servicio web especificado por el URI y, a continuación, recibir la respuesta del servicio web, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));
  ...
  var response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode) {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Para realizar esta operación el resto servicio envía el código de estado HTTP 200 (OK) en la respuesta, lo que indica que la solicitud es correcta y que la información solicitada en la respuesta.

Si la operación HTTP fue correcta, se lee el contenido de la respuesta, para su presentación. El `HttpResponseMessage.Content` propiedad representa el contenido de la respuesta HTTP y el `HttpContent.ReadAsStringAsync` método escribe el contenido HTTP de forma asincrónica en una cadena. Este contenido se convierte de JSON para un `List` de `TodoItem` instancias.

### <a name="creating-data"></a>Creación de datos

El `HttpClient.PostAsync` método se usa para enviar la solicitud POST al servicio web especificado por el identificador URI y, a continuación, para recibir la respuesta del servicio web, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem) {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"             TodoItem successfully saved.");

  }
  ...
}
```

El `TodoItem` instancia se convierte en una carga JSON para enviar al servicio web. Esta carga se incrusta en el cuerpo de contenido HTTP que se enviará al servicio web antes de que la solicitud se realiza con el `PostAsync` método.

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas para esta operación comunes son:

- **201 (creado)** : la solicitud generó un nuevo recurso que se va a crear antes de que se envió la respuesta.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **409 (conflicto)** : la solicitud no puede llevarse a cabo debido a un conflicto en el servidor.

### <a name="updating-data"></a>Actualizar datos

El `HttpClient.PutAsync` método se usa para enviar la solicitud PUT al servicio web especificado por el URI y, a continuación, recibir la respuesta del servicio web, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
La operación de la `PutAsync` método es idéntico a la `PostAsync` método que se usa para crear los datos en el servicio web. Sin embargo, se diferencian las posibles respuestas enviadas desde el servicio web.

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas para esta operación comunes son:

- **204 (sin contenido)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

### <a name="deleting-data"></a>Eliminar datos

El `HttpClient.DeleteAsync` método se usa para enviar la solicitud de eliminación para el servicio web especificado por el URI y, a continuación, recibir la respuesta del servicio web, tal como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"             TodoItem successfully deleted.");
  }
  ...
}
```

El servicio REST envía un código de estado HTTP en el `HttpResponseMessage.IsSuccessStatusCode` propiedad para indicar si la solicitud HTTP se realizó correctamente o no. Las respuestas para esta operación comunes son:

- **204 (sin contenido)** : la solicitud se ha procesado correctamente y la respuesta está en blanco intencionadamente.
- **400 (solicitud incorrecta)** : el servidor no entiende la solicitud.
- **404 (no encontrado)** : el recurso solicitado no existe en el servidor.

## <a name="summary"></a>Resumen

Este artículo examina cómo consumir un servicio web RESTful desde una aplicación de Xamarin.Forms, usando la `HttpClient` clase. La sencillez de REST ha ayudado a hacer el método principal para tener acceso a servicios web en aplicaciones móviles.


## <a name="related-links"></a>Vínculos relacionados

- [Creación de servicios back-end para aplicaciones móviles nativas](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
