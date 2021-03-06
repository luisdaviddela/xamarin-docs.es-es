---
title: Marcado de Json MonoTouch.Dialog
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 02c95f06571a3c242481769846d7cab51450f2ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="monotouchdialog-json-markup"></a>Marcado de Json MonoTouch.Dialog

Esta página describe el marcado de Json que acepta del MonoTouch.Dialog [JsonElement](https://developer.xamarin.com/api/type/MonoTouch.Dialog.JsonElement/)

Comencemos con un ejemplo. El siguiente es un archivo Json completo que se puede pasar a JsonElement.

```csharp
{     
  "title": "Json Sample",
  "sections": [ 
      {
          "header": "Booleans",
          "footer": "Slider or image-based",
          "id": "first-section",
          "elements": [
              { 
                  "type" : "boolean",
                  "caption" : "Demo of a Boolean",
                  "value"   : true
              }, {
                  "type": "boolean",
                  "caption" : "Boolean using images",
                  "value"   : false,
                  "on"      : "favorite.png",
                  "off"     : "~/favorited.png"
              }, {
                      "type": "root",
                      "title": "Tap for nested controller",
                      "sections": [ {
                         "header": "Nested view!",
                         "elements": [
                           {
                             "type": "boolean",
                             "caption": "Just a boolean",
                             "id": "the-boolean",
                             "value": false
                           },
                           {
                             "type": "string",
                             "caption": "Welcome to the nested controller"
                           }
                         ]
                       }
                     ]
                   }
          ]
      }, {
          "header": "Entries",
          "elements" : [
              {
                  "type": "entry",
                  "caption": "Username",
                  "value": "",
                  "placeholder": "Your account username"
              }
          ]
      }
  ]
}
```

El marcado anterior produce la interfaz de usuario siguiente:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "La interfaz de usuario creado por el marcado dado")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Cada elemento en el árbol puede contener la propiedad `"id"`. Es posible en tiempo de ejecución hacer referencia a secciones individuales o elementos mediante el indizador JsonElement. Así:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Sintaxis del elemento raíz

El elemento raíz contiene los siguientes valores:

-  `title`
-  `sections` (opcional)


El elemento raíz puede aparecer dentro de una sección como un elemento que se va a crear un controlador anidado. En ese caso, la propiedad adicional `"type"` debe establecerse en `"root"`

 <a name="url" />


### <a name="url"></a>url

Si el `"url"` propiedad está establecida, si el usuario puntea en este RootElement, el código solicitará un archivo de la dirección url especificada y hará que el contenido de la nueva información de muestra. Puede utilizar esto para crear extender la interfaz de usuario desde el servidor basado en el usuario puntea.

 <a name="group" />


### <a name="group"></a>grupo

Si establece esta opción, se establece el nombre de grupo para el elemento raíz. Nombres de los grupos se utilizan para seleccionar un resumen que se muestra como el valor del elemento raíz de uno de los elementos anidados en el elemento. Este es el valor de una casilla de verificación o el valor de un botón de radio.

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Identifica el elemento de radio está seleccionado en elementos anidados

 <a name="title" />


### <a name="title"></a>título

Si está presente, será el título usado para RootElement

 <a name="type" />


### <a name="type"></a>type

Debe establecerse en `"root"` cuando aparece en una sección (Esto se usa para anidar controladores).

 <a name="sections" />


### <a name="sections"></a>secciones

Se trata de una matriz Json con secciones individuales

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>Sintaxis de sección

La sección contiene:

-  `header` (opcional)
-  `footer` (opcional)
-  Matriz `elements`


 <a name="header" />


### <a name="header"></a>header

Si está presente, se muestra el texto del encabezado como un título de la sección.

 <a name="footer" />


### <a name="footer"></a>Pie de página

Si está presente, el pie de página se muestra en la parte inferior de la sección.

 <a name="elements" />


### <a name="elements"></a>elementos

Se trata de una matriz de elementos. Cada elemento debe contener al menos una clave, el `"type"` clave que se usa para identificar el tipo de elemento que se va a crear.
Algunos de los elementos comparten algunas propiedades comunes como `"caption"` y `"value"`. Se trata de la lista de elementos compatibles:

-  `string` elementos (tanto con y sin un estilo)
-  `entry` líneas (normal o contraseña)
-  `boolean` valores (mediante conmutadores o imágenes)


Elementos de cadena se pueden utilizar como botones proporcionando un método que se invoca cuando el usuario puntea en la celda o el descriptor de acceso

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Representación de elementos

La representación de elementos se basa en el C# StringElement y StyledStringElement y puede representar la información de varias formas y es posible hacer de varias maneras. Se pueden crear los elementos más simples similar al siguiente:

```csharp
{
        "type": "string",
        "caption": "Json Serializer",
}
```

Esto mostrará una cadena simple con todos los valores predeterminados: fuente, fondo, color de texto y decoraciones. Es posible enlazar acciones a estos elementos y hacen que se comporten como botones estableciendo la `"ontap"` propiedad o `"onaccessorytap"` propiedades:

```csharp
{
    "type":    "string",
        "caption": "View Photos",
        "ontap:    "Acme.PhotoLibrary.ShowPhotos"
}
```

Los pasos anteriores llamará el método "ShowPhotos" en la clase "Acme.PhotoLibrary". El `"onaccessorytap"` es similar, pero solo se invocará si el usuario puntea en el descriptor de acceso en lugar de puntear en la celda. Para habilitar esta opción, también debe establecer el descriptor de acceso:

```csharp
{
    "type":     "string",
        "caption":  "View Photos",
        "ontap:     "Acme.PhotoLibrary.ShowPhotos",
        "accessory: "detail-disclosure",
        "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Representar elementos puede mostrar dos cadenas a la vez, uno es el título y otro es el valor. Cómo se representan estas cadenas dependen el estilo, se pueden establecer mediante el `"style"` propiedad. El valor predeterminado mostrará el título a la izquierda y el valor de la derecha. Vea la sección sobre el estilo para obtener más detalles. Colores se codifican utilizando el símbolo "#" seguido de números hexadecimales que representan los valores para los valores de rojos, verdes, azul y quizá alfabéticos. El contenido puede codificarse en forma abreviada (3 o 4 dígitos hexadecimales) que representa los valores RGB o RGBA. O el formato largo (6 u 8 dígitos) que representan los valores RGB o RGBA. La versión abreviada es un método abreviado para escribir dos veces el mismo dígito hexadecimal. Por lo que la constante "#1bc" es interpretará como rojo = 0 x 11, verde = 0xbb y azul = 0xcc. Si el valor alfa no está presente, el color es opaco. Algunos ejemplos:

```csharp
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>accesorio

Determina el tipo de descriptor de acceso que se mostrará en el elemento de representación, los valores posibles son:

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Si el valor no está presente, no se muestra ningún accesorio

 <a name="background" />


### <a name="background"></a>fondo

La propiedad background establece el color de fondo de la celda. El valor es una dirección URL a una imagen (en este caso, se invocará el descargador de imagen asincrónica y el fondo se actualizará cuando se descarga la imagen) o puede ser un color especificado utilizando la sintaxis de color.

 <a name="caption" />


### <a name="caption"></a>Título

La cadena principal se mostrará en el elemento de representación. La fuente y color pueden personalizarse estableciendo la `"textcolor"` y `"font"` propiedades. El estilo de representación viene determinado por la `"style"` propiedad.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>color y detailcolor

El color que se utilizará para el texto principal o el texto detallado.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>DetailFont y fuente

La fuente que se va a utilizar para el título o el texto de detalle. El formato de una especificación de fuente es el nombre de fuente puede ir seguido de un guión y el tamaño de punto.
Éstas son las especificaciones de una fuente válida:

-  "Helvetica"
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

Determina cómo se dividen las líneas. Los valores posibles son:

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


Ambos `character-wrap` y `word-wrap` puede utilizarse junto con el `"lines"` propiedad establecida en cero para convertir el elemento de representación en un elemento de varias líneas.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ONTAP y onaccessorytap

Estas propiedades deben apuntar a un nombre de método estático en la aplicación que toma un objeto como un parámetro. Cuando se crea la jerarquía mediante los métodos JsonDialog.FromFile o JsonDialog.FromJson puede pasar un valor de objeto opcional. Este valor de objeto, a continuación, se pasa a los métodos. Ya puede utilizarla para pasar algún contexto al método estático. Por ejemplo:

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines" />


### <a name="lines"></a>líneas

Si se establece en cero, lo hará que el tamaño automático de elemento según el contenido de las cadenas contenidas. Para que funcione, debe establecer también la `"linebreak"` propiedad `"character-wrap"` o `"word-wrap"`.

 <a name="style" />


### <a name="style"></a>estilo

El estilo determina el tipo de estilo de celda que se usará para representar el contenido y se corresponden con los valores de enumeración de UITableViewCellStyle.
Los valores posibles son:

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` : texto con un subtítulo.


 <a name="subtitle" />


### <a name="subtitle"></a>Subtítulo

El valor que se usará para el subtítulo. Se trata de un acceso directo para establecer el estilo en `"subtitle"` y establezca el `"value"` propiedad en una cadena.
Esto lo hará con una única entrada.

 <a name="textcolor" />


### <a name="textcolor"></a>TextColor

El color que se utilizará para el texto.

 <a name="value" />


### <a name="value"></a>value

El valor secundario que se mostrará en el elemento de representación. El diseño de este se ve afectado por la `"style"` configuración. La fuente y color pueden personalizarse estableciendo la `"detailfont"` y `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Elementos booleanos

Elementos booleanos deben establecer el tipo en `"bool"`, puede contener una `"caption"` para mostrar y el `"value"` se establece en true o false. Si el `"on"` y `"off"` se establecen las propiedades, se supone que son imágenes. Las imágenes se resuelven en relación con el directorio de trabajo actual en la aplicación. Si desea hacer referencia a archivos relativas del paquete, puede usar el `"~"` como método abreviado para representar el directorio del paquete de aplicación. Por ejemplo `"~/favorite.png"` será el favorite.png que se encuentra en el archivo de paquete. Por ejemplo:

```csharp
{ 
    "type" : "boolean",
    "caption" : "Demo of a Boolean",
    "value"   : true
},

{
    "type": "boolean",
    "caption" : "Boolean using images",
    "value"   : false,
    "on"      : "favorite.png",
    "off"     : "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>type

Tipo se puede establecer en `"boolean"` o `"checkbox"`. Si establece en un valor booleano va a usar un UISlider o imágenes (si ambos `"on"` y `"off"` se establecen). Si se establece en la casilla de verificación, se utiliza un control checkbox. El `"group"` propiedad puede utilizarse para etiquetar un elemento booleano como pertenecientes a un grupo determinado. Esto es útil si el contenedor raíz tiene también una `"group"` propiedad como raíz resumirá los resultados con un recuento de todos los valores booleanos (o casillas de verificación) que pertenecen al mismo grupo.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Elementos de entrada

Use elementos de entrada para permitir al usuario que introduzca datos. El tipo de elementos de entrada sea `"entry"` o `"password"`. El `"caption"` propiedad se establece en el texto para mostrar de la derecha y el `"value"` se establece en el valor inicial para la entrada. El `"placeholder"` se usa para mostrar una sugerencia al usuario las entradas vacías (se muestran atenuados). A continuación se muestran algunos ejemplos:

```csharp
{
        "type": "entry",
        "caption": "Username",
        "value": "",
        "placeholder": "Your account username"
}, {
        "type": "password",
        "caption": "Password",
        "value": "",
        "placeholder": "You password"
}, {
        "type": "entry",
        "caption": "Zip Code",
        "value": "01010",
        "placeholder": "your zip code",
        "keyboard": "numbers"
}, {
        "type": "entry",
        "return-key": "route",
        "caption": "Entry with 'route'",
        "placeholder": "captialization all + no corrections",
        "capitalization": "all",
        "autocorrect": "no"
}
```

 <a name="autocorrect" />


### <a name="autocorrect"></a>Autocorrección

Determina el estilo de corrección automática que se usará para la entrada. Los valores posibles son true o false (o las cadenas `"yes"` y `"no"`).

 <a name="capitalization" />


### <a name="capitalization"></a>uso de mayúsculas

El estilo de mayúsculas y minúsculas que se usará para la entrada. Los valores posibles son:

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>Título

El título que se va a utilizar para la entrada

 <a name="keyboard" />


### <a name="keyboard"></a>teclado

El tipo de teclado que se usará para la entrada de datos. Los valores posibles son:

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>Marcador de posición

El texto de sugerencia que se muestra cuando la entrada tiene un valor vacío.

 <a name="return-key" />


### <a name="return-key"></a>tecla RETORNO

La etiqueta utilizada para la tecla ENTRAR. Los valores posibles son:

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>value

El valor inicial para la entrada

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>Elementos de radio

Elementos de radio tienen tipo `"radio"`. Se ha seleccionado el elemento que está seleccionado por el `radioselected` propiedad en su elemento raíz que lo contiene.
Además, si se establece un valor para el `"group"` propiedad, este botón de opción corresponde a ese grupo.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Fecha y hora elementos

Los tipos de elemento `"datetime"`, `"date"` y `"time"` se utilizan para representar las fechas con horas, fechas u horas. Estos elementos toman como parámetros de un título y un valor. El valor puede escribirse en cualquier formato admitido por la función DateTime.Parse. NET. Ejemplo:

```csharp
"header": "Dates and Times",
"elements": [
        {
                "type": "datetime",
                "caption": "Date and Time",
                "value": "Sat, 01 Nov 2008 19:35:00 GMT"
        }, {
                "type": "date",
                "caption": "Date",
                "value": "10/10"
        }, {
                "type": "time",
                "caption": "Time",
                "value": "11:23"
                }                       
]
```

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>Elemento de HTML/Web

Puede crear una celda que cuando puntea incrustará un UIWebView que representa el contenido de una dirección URL especificada, ya sea local o remoto mediante el `"html"` tipo. Son las dos únicas propiedades para este elemento `"caption"` y `"url"`:

```csharp
{
        "type": "html",
        "caption": "Miguel's blog",
        "url": "http://tirania.org/blog" 
}
```
