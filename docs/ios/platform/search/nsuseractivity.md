---
title: Búsqueda con NSUserActivity
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 803fcce359bbe27ea19901afa766f5b7f4692e0c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="search-with-nsuseractivity"></a>Búsqueda con NSUserActivity

`NSUserActivity` se introdujo en iOS 8 y se utiliza para proporcionar los datos para la entrega.
Permite crear actividades en determinadas partes de la aplicación que se puede pasar a otra instancia de la aplicación que se ejecuta en un dispositivo iOS diferentes. El dispositivo receptor, a continuación, puede seguir la actividad que se inicia en el dispositivo anterior, recoger derecha donde se dejó el usuario. Para obtener más información sobre el uso de entrega, vea nuestra [Introducción a la entrega](~/ios/platform/handoff.md) documentación.

Familiarizado con iOS 9, `NSUserActivity` se puedan indizar (pública y privada) y buscar de la búsqueda de Spotlight y Safari. Al marcar un `NSUserActivity` como metadatos y indizable agregando y permite realizar búsquedos, la actividad puede aparecer en los resultados de búsqueda en el dispositivo iOS.

[![](nsuseractivity-images/apphistory01.png "La información general de historial de la aplicación")](nsuseractivity-images/apphistory01.png#lightbox)

Si el usuario selecciona un resultado de búsqueda que pertenece a una actividad desde la aplicación, la aplicación se iniciará y la actividad descrito por el `NSUserActivity` se reinicia y se presentan al usuario.

Las siguientes propiedades de `NSUserActivity` se utilizan para admitir la búsqueda de la aplicación:

 - `EligibleForHandoff` – If `true`, esta actividad se puede utilizar en una operación de entrega.
 - `EligibleForSearch` – If `true`, esta actividad se agregará al índice en el dispositivo y se presentan en los resultados de la búsqueda.
 - `EligibleForPublicIndexing` – If `true`, esta actividad se agregan al índice de basado en la nube de Apple y se presentan a los usuarios (a través de la búsqueda) que aún no ha instalado la aplicación en su dispositivo iOS. Consulte la [público indización de búsqueda](#Public-Search-Indexing) sección para obtener más detalles.
 - `Title` : Proporciona un título para la actividad y se muestra en los resultados de búsqueda. Los usuarios también pueden buscar el texto del título de la propia.
 - `Keywords` : Es una matriz de cadenas que se usan para describir la actividad que se indizan y realiza la búsqueda por el usuario final.
 - `ContentAttributeSet` : Es una `CSSearchableItemAttributeSet` utilizado para describir su actividad en detalle y ofrecer contenido enriquecido en los resultados de búsqueda de aún más.
 - `ExpirationDate` – Si desea que una actividad solo se muestra a una fecha determinada, puede proporcionar esa fecha aquí.
 - `WebpageURL` : Si la actividad puede verse en la web o vínculos profundos de Safari que admita la aplicación, puede establecer el vínculo para visitar aquí.

## <a name="nsuseractivity-quickstart"></a>Inicio rápido de NSUserActivity

Siga estas instrucciones para implementar una búsqueda `NSUserActivity` en la aplicación:

- [Creación de identificadores de tipo de actividad](#creatingtypeid)
- [Creación de una actividad](#createactivity)
- [Responder a una actividad](#respondactivity)
- [Indización de búsqueda pública](#indexing)

Hay algunos [ventajas adicionales](#benefits) al uso de `NSUserActivity` para que se pueda buscar su contenido.

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>Creación de identificadores de tipo de actividad

Para poder crear una actividad de búsqueda, debe crear un _identificador de tipo de actividad_ para identificarlo. El identificador de tipo de actividad es una cadena corta que se agrega a la `NSUserActivityTypes` matriz de la aplicación **Info.plist** archivo usado para identificar de forma única un determinado tipo de actividad de usuario. Habrá una entrada en la matriz por cada actividad que es compatible con la aplicación y se expone en la búsqueda de la aplicación. 

Apple sugiere utilizando una notación de estilo de DNS inversa para el identificador de tipo de actividad para evitar conflictos. Por ejemplo: `com.company-name.appname.activity` para aplicaciones específicas basadas en las actividades o `com.company-name.activity` para las actividades que se pueden ejecutar en varias aplicaciones.

El identificador de tipo de actividad se usa al crear un `NSUserActivity` instancia para identificar el tipo de actividad. Cuando una actividad se sigue como resultado del usuario al puntear en un resultado de búsqueda, el tipo de actividad (junto con el identificador de equipo de la aplicación) determina qué aplicación para iniciar para continuar con la actividad.

Para crear los identificadores de tipo de actividad necesaria para admitir este comportamiento, edite el **Info.plist** archivo y cambie a la **origen** vista. Agregar un `NSUserActivityTypes` clave y crear identificadores en el formato siguiente:

[![](nsuseractivity-images/type01.png "La clave de NSUserActivityTypes y los identificadores necesarios en el editor de plist")](nsuseractivity-images/type01.png#lightbox)

En el ejemplo anterior, creamos un nuevo identificador de tipo de actividad para la actividad de búsqueda (`com.xamarin.platform`). Al crear sus propias aplicaciones, reemplace el contenido de la `NSUserActivityTypes` de matriz con los identificadores de tipo de actividad específico a las actividades de la aplicación es compatible con.

<a name="createactivity" />

## <a name="creating-an-activity"></a>Creación de una actividad

El siguiente es un ejemplo de creación de una actividad para realizar una búsqueda de índice en el dispositivo hospedado:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

Podríamos agregar más detalles estableciendo la `ContentAttributeSet` propiedad de nuestro `NSUserActivity` como se indica a continuación:

[![](nsuseractivity-images/apphistory02.png "Información general de detalles de la búsqueda de adición")](nsuseractivity-images/apphistory02.png#lightbox)

Mediante el uso de un `ContentAttributeSet` puede crear los resultados de búsqueda que llevan al usuario final para interactuar con ellos.

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>Responder a una actividad

Para responder al usuario puntee en un resultado de búsqueda (`NSUserActivity`) para nuestra aplicación, editar la **AppDelegate.cs** archivo e invalide el `ContinueUserActivity` método. Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

Tenga en cuenta que se trata de la misma invalidación del método utilizada para responder a las solicitudes de entrega. Ahora si el usuario hace clic en un vínculo desde nuestra aplicación en los resultados de la búsqueda de Spotlight, nuestra aplicación se pone en primer plano (o se inicia si no se está ejecutando) y se mostrará el contenido, la exploración o la característica representado por ese vínculo:

[![](nsuseractivity-images/apphistory03.png "Restaurar el estado anterior de búsqueda")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>Indización de búsqueda pública

Tal y como se ha explicado anteriormente, iOS 9 facilita proporcionar acceso de búsqueda para el contenido de las aplicaciones y características que el usuario ya ha detectado y se usa en un dispositivo iOS determinado. Con la indización de público, iOS 9 proporciona una manera para que los usuarios que aún no lo ha descubierto contenido o características aún (o incluso que no ha instalado la aplicación) para obtener los resultados de sus búsquedas demasiado.

Indización pública funciona de la manera siguiente:

1. Cuando se crea una actividad para la aplicación puede marcarlo como público.
2. Actividades públicas se envía a Apple y se indizan en la nube.
3. Como más usuarios interactúan con la aplicación en un dispositivo y usar la característica concreta o el contenido representado por la actividad, aumenta en el rango.
4. Resultados públicas populares estará disponibles para otros usuarios, incluso si no tienen instalada la aplicación.
5. Estos resultados públicos se mostrarán en la búsqueda de Spotlight y Safari (si la actividad incluye una dirección URL).

Podemos realizar la actividad de búsqueda privada que hemos creado anteriormente y expandirlo para que sea público:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

Solo porque se ha establecido una actividad para la indización pública estableciendo `EligibleForPublicIndexing = true`, no significa que, se agregará automáticamente al índice de nube pública de Apple. Las siguientes condiciones deben cumplirse en primer lugar:

1. Debe aparecer en los resultados de búsqueda y seleccionarse por muchos usuarios. Los resultados permanecen privados hasta que se ha alcanzado un umbral de interacción de actividad.
2. Aprovisionamiento de aplicaciones, se evita que los datos específicos del usuario se indizan y se hizo público.

<a name="benefits" />

## <a name="additional-benefits"></a>Ventajas adicionales

Mediante el uso de búsqueda de la aplicación a través de `NSUserActivity` en la aplicación, también obtendrá las siguientes características:

- **Entrega** : puesto que la búsqueda de la aplicación está exponiendo contenido, navegación o características mediante el mismo mecanismo que entrega (`NSUserActivity`), puede permitir fácilmente que los usuarios de la aplicación iniciar una actividad en un dispositivo y continuar en otra.
- **Sugerencias de Siri** -junto con las sugerencias estándares que normalmente hace que Siri sugerencias, actives desde su aplicación pueden se sugieren automáticamente.
- **Recordatorios inteligente Siri** -los usuarios podrán solicitar Siri para ellos recordar acerca de las actividades de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de la aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Ejemplo de & entrada de Blog](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
