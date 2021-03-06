---
title: Consumir una base de datos del documento de Cosmos Azure DB
description: Una base de datos de documentos de base de datos de Azure Cosmos es una base de datos NoSQL que proporciona acceso de latencia baja a documentos JSON, ofrecen un servicio de base de datos rápida, altamente disponible y escalable para aplicaciones que requieren replicación global y escala sin problemas. En este artículo se explica cómo utilizar la biblioteca de cliente estándar de .NET de base de datos de Azure Cosmos para integrar una base de datos de documentos de base de datos de Azure Cosmos en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: a4656b5eb9a8159f1b3dc39c8cf62509101d219e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-an-azure-cosmos-db-document-database"></a>Consumir una base de datos del documento de Cosmos Azure DB

_Una base de datos de documentos de base de datos de Azure Cosmos es una base de datos NoSQL que proporciona acceso de latencia baja a documentos JSON, ofrecen un servicio de base de datos rápida, altamente disponible y escalable para aplicaciones que requieren replicación global y escala sin problemas. En este artículo se explica cómo utilizar la biblioteca de cliente estándar de .NET de base de datos de Azure Cosmos para integrar una base de datos de documentos de base de datos de Azure Cosmos en una aplicación de Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB, por [Universidad de Xamarin](https://university.xamarin.com/)**

Se pueden aprovisionar una cuenta de base de datos de documentos de base de datos de Azure Cosmos con una suscripción de Azure. Cada cuenta de base de datos puede tener cero o más bases de datos. Una base de datos de documentos en la base de datos de Azure Cosmos es un contenedor lógico para las colecciones de documentos y los usuarios.

Una base de datos de documentos de base de datos de Azure Cosmos puede contener cero o más colecciones de documento. Cada colección de documento puede tener un nivel de rendimiento diferentes, lo que permite más rendimiento que se especifique para las colecciones que se accede con frecuencia y el menor rendimiento para las colecciones que se accede con poca frecuencia.

Cada colección de documento consta de cero o más documentos JSON. Documentos en una colección están libres de esquema y, por lo que no es necesario compartir la misma estructura o campos. Cuando se agregan documentos a una colección de documentos, Cosmos DB indiza automáticamente ellos y que estén disponibles para consulta.

Para fines de desarrollo, también se puede utilizar una base de datos de documentos en un emulador. Mediante el emulador, las aplicaciones se pueden desarrollar y probadas localmente, sin crear una suscripción de Azure o incurrir en los costos. Para obtener más información sobre el emulador, consulte [desarrollar localmente con el emulador de base de datos de Azure Cosmos](/azure/cosmos-db/local-emulator/).

En este artículo y que acompaña a la aplicación de ejemplo, se muestra una aplicación de lista de tareas donde las tareas se almacenan en una base de datos de documentos de base de datos de Azure Cosmos. Para obtener más información acerca de la aplicación de ejemplo, vea [comprender el ejemplo](~/xamarin-forms/data-cloud/walkthrough.md).

Para obtener más información acerca de la base de datos de Azure Cosmos, consulte el [documentación de base de datos de Azure Cosmos](/azure/cosmos-db/).

## <a name="setup"></a>Programa de instalación

El proceso para integrar una base de datos de documentos de base de datos de Azure Cosmos en una aplicación de Xamarin.Forms es como sigue:

1. Cree una cuenta de base de datos de Cosmos. Para obtener más información, consulte [crear una cuenta de base de datos de Azure Cosmos](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Agregar el [biblioteca de cliente estándar de .NET de base de datos de Azure Cosmos](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) paquete NuGet para los proyectos de plataforma en la solución de Xamarin.Forms.
1. Agregar `using` directivas para la `Microsoft.Azure.Documents`, `Microsoft.Azure.Documents.Client`, y `Microsoft.Azure.Documents.Linq` los espacios de nombres para las clases que tendrá acceso a la cuenta de base de datos de Cosmos.

Después de realizar estos pasos, la biblioteca estándar de .NET de base de datos de Azure Cosmos de cliente se puede utilizar para configurar y ejecutar solicitudes en la base de datos del documento.

> [!NOTE]
> La biblioteca estándar de .NET de base de datos de Azure Cosmos de cliente solo puede instalarse en los proyectos de plataforma y no en un proyecto de biblioteca de clases portables (PCL). Por lo tanto, la aplicación de ejemplo es un proyecto de acceso compartido (SAP) para evitar la duplicación de código. Sin embargo, la `DependencyService` clase puede usarse en un proyecto PCL para llamar a código de biblioteca estándar de .NET de base de datos de Azure Cosmos cliente incluido en proyectos específicos de plataforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Consumir la cuenta de base de datos de Azure Cosmos

El `DocumentClient` tipo encapsula el punto de conexión, las credenciales y directiva de conexión utilizada para tener acceso a la cuenta de base de datos de Azure Cosmos y se usa para configurar y ejecutar solicitudes en la cuenta. En el ejemplo de código siguiente se muestra cómo crear una instancia de esta clase:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

El Uri de la base de datos de Cosmos y la clave principal deben proporcionarse en el `DocumentClient` constructor. Éstos se pueden obtener desde el Portal de Azure. Para obtener más información, consulte [conectar a una cuenta de base de datos de Azure Cosmos](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Crear una base de datos

Una base de datos de documento es un contenedor lógico para las colecciones de documentos y usuarios y puede ser creado en el Portal de Azure, o utilizando mediante programación el `DocumentClient.CreateDatabaseIfNotExistsAsync` método:

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

El `CreateDatabaseIfNotExistsAsync` método especifica un `Database` objeto como argumento, con el `Database` objeto que especifica el nombre de la base de datos como su `Id` propiedad. El `CreateDatabaseIfNotExistsAsync` método crea la base de datos si no existe, o devuelve la base de datos si ya existe. Sin embargo, la aplicación de ejemplo omite los datos devueltos por la `CreateDatabaseIfNotExistsAsync` método.

> [!NOTE]
> El `CreateDatabaseIfNotExistsAsync` método devuelve un `Task<ResourceResponse<Database>>` objeto y el código de estado de la respuesta se pueden comprobar para determinar si se creó una base de datos, o si se devolvió una base de datos existente.

### <a name="creating-a-document-collection"></a>Crear una colección de documentos

Una colección de documentos es un contenedor de documentos JSON y puede ser creado en el Portal de Azure, o utilizando mediante programación el `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` método:

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

El `CreateDocumentCollectionIfNotExistsAsync` método requiere dos argumentos obligatorios: especifica un nombre de base de datos como un `Uri`y un `DocumentCollection` objeto. El `DocumentCollection` objeto representa una colección de documento cuyo nombre se especifica con el `Id` propiedad. El `CreateDocumentCollectionIfNotExistsAsync` método crea la colección de documentos si no existe, o devuelve la colección de documentos si ya existe. Sin embargo, la aplicación de ejemplo omite los datos devueltos por la `CreateDocumentCollectionIfNotExistsAsync` método.

> [!NOTE]
> El `CreateDocumentCollectionIfNotExistsAsync` método devuelve un `Task<ResourceResponse<DocumentCollection>>` objeto y el código de estado de la respuesta se pueden comprobar para determinar si una colección de documentos se creó o se devuelve una colección de documento existente.

Si lo desea, el `CreateDocumentCollectionIfNotExistsAsync` método también puede especificar un `RequestOptions` objeto, que encapsula las opciones que se pueden especificar para las solicitudes emitidas en la cuenta de base de datos de Cosmos. El `RequestOptions.OfferThroughput` propiedad se utiliza para definir el nivel de rendimiento de la colección de documentos y, en el ejemplo de aplicación, se establece en 400 unidades de solicitud por segundo. Este valor se debe incrementar o disminuir dependiendo de si la colección se con frecuencia o con poca frecuencia accederá.

> [!IMPORTANT]
> Tenga en cuenta que el `CreateDocumentCollectionIfNotExistsAsync` método creará una nueva colección con un rendimiento reservado, que tiene implicaciones de precios.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Recuperación de documentos de la colección de documentos

El contenido de una colección de documentos se puede recuperar mediante la creación y ejecución de una consulta de documento. Se crea una consulta de documento con el `DocumentClient.CreateDocumentQuery` método:

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

Esta consulta recupera todos los documentos de la colección especificada asincrónicamente y coloca los documentos en un `List<TodoItem>` colección para su presentación.

El `CreateDocumentQuery<T>` método especifica un `Uri` argumento que representa la colección que se debe consultar para los documentos. En este ejemplo, el `collectionLink` variable es un campo de nivel de clase que especifica la `Uri` que representa la colección de documentos para recuperar los documentos de:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

El `CreateDocumentQuery<T>` método crea una consulta que se ejecuta de forma sincrónica y devuelve un `IQueryable<T>` objeto. Sin embargo, el `AsDocumentQuery` método convierte el `IQueryable<T>` el objeto a un `IDocumentQuery<T>` objeto que se puede ejecutar de forma asincrónica. Se ejecuta la consulta asincrónica con el `IDocumentQuery<T>.ExecuteNextAsync` método, que recupera la siguiente página de resultados de la base de datos del documento, con el `IDocumentQuery<T>.HasMoreResults` propiedad que indica si hay resultados adicionales que se devuelven de la consulta.

Los documentos pueden ser filtrado del servidor mediante la inclusión de un `Where` cláusula de la consulta, que se aplica un predicado de filtrado a la consulta en la colección de documentos:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Esta consulta recupera todos los documentos de la colección cuya `Done` propiedad es igual a `false`.

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Insertar un documento en una colección de documentos

Documentos son el contenido JSON definidos por el usuario y se pueden insertar en una colección de documentos con el `DocumentClient.CreateDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

El `CreateDocumentAsync` método especifica un `Uri` argumento que representa la colección que se debe insertar el documento en, y un `object` argumento que representa el documento que se va a insertar.

### <a name="replacing-a-document-in-a-document-collection"></a>Reemplazar un documento en una colección de documentos

Documentos se pueden sustituir en una colección de documentos con el `DocumentClient.ReplaceDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

El `ReplaceDocumentAsync` método especifica un `Uri` argumento que representa el documento de la colección que se debe reemplazar, y un `object` argumento que representa los datos del documento actualizado.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Eliminar un documento de una colección de documentos

Se puede eliminar un documento de una colección de documentos con el `DocumentClient.DeleteDocumentAsync` método:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

El `DeleteDocumentAsync` método especifica un `Uri` argumento que representa el documento de la colección que se debe eliminar.

### <a name="deleting-a-document-collection"></a>Eliminar una colección de documentos

Una colección de documentos se puede eliminar de una base de datos con el `DocumentClient.DeleteDocumentCollectionAsync` método:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

El `DeleteDocumentCollectionAsync` método especifica un `Uri` argumento que representa la colección de documentos que va a eliminar. Tenga en cuenta que invocar este método, también se eliminarán los documentos almacenados en la colección.

### <a name="deleting-a-database"></a>Eliminar una base de datos

Se puede eliminar una base de datos de una cuenta de base de datos de la base de datos de Cosmos con el `DocumentClient.DeleteDatabaesAsync` método:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

El `DeleteDatabaseAsync` método especifica un `Uri` argumento que representa la base de datos va a eliminar. Tenga en cuenta que invocar este método eliminará también las colecciones de documentos almacenadas en la base de datos y los documentos almacenados en las colecciones de documento.

## <a name="summary"></a>Resumen

Este artículo explica cómo usar la biblioteca de cliente estándar de .NET de base de datos de Azure Cosmos para integrar una base de datos de documentos de base de datos de Azure Cosmos en una aplicación de Xamarin.Forms. Una base de datos de documentos de base de datos de Azure Cosmos es una base de datos NoSQL que proporciona acceso de latencia baja a documentos JSON, ofrecen un servicio de base de datos rápida, altamente disponible y escalable para aplicaciones que requieren replicación global y escala sin problemas.


## <a name="related-links"></a>Vínculos relacionados

- [Base de datos de lista de tareas Azure Cosmos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)
- [Documentación de Cosmos Azure DB](/azure/cosmos-db/)
- [Biblioteca de cliente .NET de base de datos de Cosmos estándar de Azure](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API de base de datos de Azure Cosmos](https://docs.microsoft.com/en-us/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
