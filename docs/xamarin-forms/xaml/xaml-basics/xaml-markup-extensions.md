---
title: Parte 3. Extensiones de marcado XAML
description: Las extensiones de marcado XAML constituyen una característica importante de XAML que permite que las propiedades se establezca en objetos o valores que se hace referencia indirectamente desde otros orígenes. Las extensiones de marcado XAML son especialmente importantes para uso compartido de objetos y hacer referencia a las constantes se utilizan en toda una aplicación, pero encuentra su mayor utilidad enlaces de datos.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: charlespetzold
ms.author: chape
ms.date: 3/27/2018
ms.openlocfilehash: c110223eae2bb06f64adf3e09977d97cc7b5d71b
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Extensiones de marcado XAML

_Las extensiones de marcado XAML constituyen una característica importante de XAML que permite que las propiedades se establezca en objetos o valores que se hace referencia indirectamente desde otros orígenes. Las extensiones de marcado XAML son especialmente importantes para uso compartido de objetos y hacer referencia a las constantes se utilizan en toda una aplicación, pero encuentra su mayor utilidad enlaces de datos._

## <a name="xaml-markup-extensions"></a>Extensiones de marcado XAML

En general, utilizar XAML para establecer las propiedades de un objeto por valores explícitos, como una cadena, un número, un miembro de enumeración o una cadena que se convierte en un valor en segundo plano.

Sin embargo, en ocasiones, propiedades en su lugar, deben hacer referencia a valores definidos en alguna parte else o que pueden requerir un procesamiento poco código en tiempo de ejecución. Para estos fines, XAML *las extensiones de marcado* están disponibles.

Estas extensiones de marcado XAML no son extensiones de XML. XAML es XML completamente válido. Se denominan "extensiones" debido a están respaldados por el código en las clases que implementan `IMarkupExtension`. Puede escribir sus propias extensiones de marcado personalizada.

En muchos casos, las extensiones de marcado XAML son reconocibles al instante en archivos XAML puesto que aparecen como valores de atributo delimitados por llaves: {y}, pero a veces las extensiones de marcado aparecen en el marcado como elementos convencionales.

## <a name="shared-resources"></a>Recursos compartidos

Algunas páginas XAML contienen varias vistas con propiedades establecidas en los mismos valores. Por ejemplo, muchos de los valores de propiedad para estos `Button` objetos son iguales:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

Si una de estas propiedades debe cambiarse, es preferible realizar el cambio de una sola vez en lugar de tres veces. Si se tratara de código, que es probable que va a utilizar constantes y los objetos estáticos de solo lectura para mantener estos valores coherente y modificar fácilmente.

En XAML, una solución popular consiste en almacenar estos valores u objetos en una *diccionario de recursos*. El `VisualElement` clase define una propiedad denominada `Resources` de tipo `ResourceDictionary`, que es un diccionario con claves de tipo `string` y valores de tipo `object`. Puede poner un objeto en el diccionario y, a continuación, hacer referencia a ellos desde el marcado, todo ello en XAML.

Para utilizar un diccionario de recursos en una página, incluya un par de `Resources` etiquetas de elemento de propiedad. Es más conveniente colocar este elemento en la parte superior de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

También es necesario incluir explícitamente `ResourceDictionary` etiquetas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Ahora los objetos y los valores de distintos tipos pueden agregarse al diccionario de recursos. Estos tipos deben ser instanciables. No pueden ser clases abstractas, por ejemplo. Estos tipos también deben tener un constructor público sin parámetros. Cada elemento necesita una clave de diccionario especificada con el `x:Key` atributo. Por ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Estos dos elementos son valores de tipo de estructura `LayoutOptions`y cada una tiene una clave única y una o dos propiedades establecidas. En el código y marcado, es mucho más habitual utilizar los campos estáticos de `LayoutOptions`, pero aquí es más cómodo establecer las propiedades.

Ahora es necesario establecer la `HorizontalOptions` y `VerticalOptions` propiedades de estos botones para estos recursos, y que se realiza con el `StaticResource` extensión de marcado XAML:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

El `StaticResource` extensión de marcado siempre se delimita con llaves e incluye la clave de diccionario.

El nombre `StaticResource` distingue desde `DynamicResource`, que también es compatible con la Xamarin.Forms. `DynamicResource` es para las claves del diccionario asociadas con los valores que podrían cambiar en tiempo de ejecución, mientras que `StaticResource` tiene acceso a los elementos del diccionario de solo una vez cuando se crean los elementos en la página.

Para el `BorderWidth` propiedad, es necesario almacenar un valor doble que en el diccionario. XAML define convenientemente etiquetas para los tipos de datos comunes como `x:Double` y `x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

No es necesario poner en tres líneas. Esta entrada del diccionario para este ángulo de giro solo toma una línea hacia arriba:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Pueden hacer referencia a esos dos recursos de la misma manera que el `LayoutOptions` valores:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Para recursos de tipo `Color`, puede usar el mismo representaciones de cadena que utilizar al asignar directamente los atributos de estos tipos. Los convertidores de tipos se invocan cuando se crea el recurso. Este es un recurso de tipo `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

A menudo, los programas conjunto un `FontSize` propiedad a un miembro de la `NamedSize` enumeración como `Large`. El `FontSizeConverter` clase funciona en segundo plano para convertirla en un valor depende de la plataforma mediante el `Device.GetNamedSized` método. Sin embargo, al definir un recurso de tamaño de fuente, tiene más sentido usar un valor numérico, se muestra aquí como un `x:Double` tipo:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Ahora todas las propiedades excepto `Text` se definen mediante la configuración de recursos:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

También es posible utilizar `OnPlatform` en el diccionario de recursos para definir valores diferentes para las plataformas. Le mostramos cómo un `OnPlatform` objeto puede formar parte del diccionario de recursos para los colores de texto diferentes:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Tenga en cuenta que `OnPlatform` obtiene tanto un `x:Key` porque es un objeto en el diccionario de atributos y un `x:TypeArguments` atributo porque es una clase genérica. El `iOS`, `Android`, y `UWP` atributos se convierten en `Color` valores cuando se inicializa el objeto.

Este es el archivo XAML completo final con tres botones de acceso a los seis valores compartidos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:String x:Key="fontSize">Large</x:String>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

Las capturas de pantalla para comprobar un estilo coherente y el estilo de depende de la plataforma:

[![](xaml-markup-extensions-images/sharedresources.png "Controles con estilo")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "controles con estilo")

Aunque es más común para definir la `Resources` colección en la parte superior de la página, tenga en cuenta que la `Resources` propiedad se define mediante `VisualElement`, y puede tener `Resources` las colecciones de otros elementos en la página. Por ejemplo, pruebe a agregar uno a la `StackLayout` en este ejemplo:

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

Descubrirá que el color del texto de los botones ahora es azul. Básicamente, siempre que el analizador XAML encuentra un `StaticResource` extensión de marcado, se busca en el árbol visual y se usa la primera `ResourceDictionary` encuentra que contiene esa clave.

Uno de los tipos más comunes de los objetos almacenados en diccionarios de recursos es el Xamarin.Forms `Style`, que define una colección de valores de propiedad. Estilos se describen en el artículo [estilos](~/xamarin-forms/user-interface/styles/index.md).

En ocasiones, los programadores nuevos en XAML preguntan si puede colocar un elemento visual como `Label` o `Button` en un `ResourceDictionary`. Aunque es seguramente posible, no tiene mucho sentido. El propósito de la `ResourceDictionary` es compartir objetos. No se puede compartir un elemento visual. La misma instancia no puede aparecer dos veces en una sola página.

## <a name="the-xstatic-markup-extension"></a>La extensión de marcado x: Static

A pesar de las similitudes de sus nombres, `x:Static` y `StaticResource` son muy diferentes. `StaticResource` Devuelve un objeto de un diccionario de recursos al `x:Static` tiene acceso a uno de los siguientes:

- un campo estático público
- una propiedad estática pública
- un campo constante público 
- un miembro de enumeración. 

El `StaticResource` extensión de marcado es compatible con las implementaciones de XAML que define un diccionario de recursos, mientras que `x:Static` es una parte intrínseca de XAML, como la `x` revela de prefijo.

Estos son algunos ejemplos que muestran cómo `x:Static` puede hacer referencia explícitamente los campos estáticos y los miembros de enumeración:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Hasta ahora, esto no es muy impresionante. Pero la `x:Static` extensión de marcado puede hacer referencia a propiedades o campos estáticos de su propio código. Por ejemplo, mostramos un `AppConstants` clase que contiene algunos campos estáticos que desea usar en varias páginas en toda una aplicación:

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;
                    
                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

Para hacer referencia a los campos estáticos de esta clase en el archivo XAML, necesitará una manera de indicar en el archivo XAML donde se encuentra este archivo. Para ello, con una declaración de espacio de nombres XML.

Recuerde que los archivos XAML que se crea como parte de la plantilla XAML Xamarin.Forms estándar contienen dos declaraciones de espacios de nombres XML: uno para tener acceso a las clases de Xamarin.Forms y otro para hacer referencia a las etiquetas y atributos intrínsecos de XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Necesitará más declaraciones de espacios de nombres XML para tener acceso a otras clases. Cada declaración de espacio de nombres XML adicional define un nuevo prefijo. Para obtener acceso a las clases locales de la biblioteca estándar de .NET de aplicación compartida, como `AppConstants`, programadores XAML utilizan a menudo el prefijo `local`. La declaración de espacio de nombres debe indicar el nombre de espacio de nombres CLR (Common Language Runtime), también conocido como el .NET espacio de nombres, que es el nombre que aparece en C# `namespace` definición o en una `using` directiva:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

También puede definir las declaraciones de espacio de nombres XML para los espacios de nombres de .NET en cualquier ensamblado al que hace referencia a la biblioteca estándar. NET. Por ejemplo, mostramos un `sys` prefijo para el estándar de .NET `System` espacio de nombres, que se encuentra en la **mscorlib** ensamblado, que una vez superado "Biblioteca en tiempo de ejecución objeto Microsoft Common", pero ahora significa "multilingüe estándar Objeto en tiempo de ejecución biblioteca común." Dado que se trata de otro ensamblado, también debe especificar el nombre del ensamblado, en este caso **mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Tenga en cuenta que la palabra clave `clr-namespace` va seguido de dos puntos y, a continuación, el nombre de espacio de nombres. NET, seguido por un punto y coma, la palabra clave `assembly`, un signo igual y el nombre del ensamblado.

Sí, un signo de dos puntos sigue `clr-namespace` pero sigue signo igual `assembly`. La sintaxis se definió en este modo deliberadamente: las declaraciones de espacio de nombres XML más hacen referencia a un URI que se inicia como un nombre de esquema URI `http`, que siempre está seguido de dos puntos. El `clr-namespace` parte de esta cadena está diseñada para imitar esta convención.

Estas declaraciones de espacio de nombres se incluyen en el **StaticConstantsPage** ejemplo. Tenga en cuenta que la `BoxView` dimensiones se establecen en `Math.PI` y `Math.E`, pero escalado por un factor de 100:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

El tamaño de los resultantes `BoxView` con respecto a la pantalla es dependiente de la plataforma:

 [![](xaml-markup-extensions-images/staticconstants.png "Controles mediante la extensión de marcado x: Static")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "controles mediante la extensión de marcado x: Static")

## <a name="other-standard-markup-extensions"></a>Otras extensiones de marcado estándar

Varias extensiones de marcado son intrínsecas de XAML y compatibles con los archivos XAML de Xamarin.Forms. Algunos de ellos no se utilizan muy a menudo pero son esenciales cuando necesite:

-  Si tiene una propiedad no `null` valor de forma predeterminada pero desea establecerlo en `null`, establézcalo en la `{x:Null}` extensión de marcado.
-  Si una propiedad es de tipo `Type`, puede asignarla a una `Type` objeto mediante la extensión de marcado `{x:Type someClass}`.
-  Puede definir matrices en XAML con el `x:Array` extensión de marcado. Esta extensión de marcado tiene un atributo necesario denominado `Type` que indica el tipo de los elementos de la matriz.
- El `Binding` extensión de marcado se explica en [parte 4. Conceptos básicos de enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="the-constraintexpression-markup-extension"></a>La extensión de marcado ConstraintExpression

Las extensiones de marcado pueden tener propiedades, pero no se establecen como atributos XML. En una extensión de marcado, valores de las propiedades están separados por comas y comillas no aparecen dentro de las llaves.

Esto se puede ilustrar con la extensión de marcado de Xamarin.Forms denominada `ConstraintExpression`, que se usa con la `RelativeLayout` clase. Puede especificar la ubicación o el tamaño de una vista secundaria como una constante o con respecto a un elemento primario o en otra vista con nombre. La sintaxis de la `ConstraintExpression` permite establecer la posición o el tamaño de una vista con un `Factor` veces una propiedad de otra vista, además de un `Constant`. Algo más complejo que requiere el código.

A continuación se ofrece un ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

Quizás la lección más importante que debe seguir en este ejemplo se muestra la sintaxis de la extensión de marcado: comillas no deben aparecer dentro de las llaves de una extensión de marcado. Cuando escriba la extensión de marcado en un archivo XAML, es normal que desee incluir los valores de las propiedades entre comillas. ¡Resistir la tentación de recurrir!

Este es el programa que se ejecuta:

[![](xaml-markup-extensions-images/relativelayout.png "Diseño relativa con restricciones")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "relativa diseño usando las restricciones")

## <a name="summary"></a>Resumen

Las extensiones de marcado XAML que se muestra a continuación proporcionan compatibilidad importante para los archivos XAML. Pero quizás sea la extensión de marcado XAML más valiosa `Binding`, que se trata en la siguiente parte de esta serie, [parte 4. Conceptos básicos de enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).



## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
