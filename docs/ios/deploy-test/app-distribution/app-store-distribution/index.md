---
title: Distribución a través del App Store
description: En este documento se habla de los requisitos para la distribución en el App Store de Apple.
ms.prod: xamarin
ms.assetid: B07E2C1F-A6DF-43CB-BFB0-0252A5558467
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/23/2017
ms.openlocfilehash: 7a38c77dde7a66f2db194cd8888a2c32a3529a9a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="app-store-distribution"></a>Distribución a través del App Store

_En este documento se habla de los requisitos para la distribución en el App Store de Apple._

Una vez que se ha desarrollado una aplicación de Xamarin.iOS, el siguiente paso del ciclo de vida de desarrollo de software es distribuirla a los usuarios mediante iTunes App Store. Es la forma más habitual de distribuir aplicaciones. Al publicar una aplicación en el App Store de Apple, se puede poner a disposición de los consumidores de todo el mundo.

> [!IMPORTANT]
> Es **importante** tener en cuenta que para usar iTunes Connect y, por lo tanto, publicar una aplicación en App Store, **debe** formar parte de un programa para desarrolladores individual o empresarial de Apple. Si es miembro de un programa para desarrolladores **empresariales** de Apple no podrá seguir los pasos de esta página.

Distribución de una aplicación: al igual que al desarrollar una aplicación, exige que se aprovisionen las aplicaciones mediante el *perfil de aprovisionamiento* adecuado. Los perfiles de aprovisionamiento son archivos que contienen información de firma de código, así como la identidad de la aplicación y el mecanismo de distribución previsto. También contienen información sobre en qué dispositivos se puede implementar la aplicación para la distribución que no se realice a través del App Store.

<a name="provisioning" />

## <a name="provisioning-an-app-for-app-store-distribution"></a>Aprovisionamiento de una aplicación para su distribución en el App Store

Independientemente de cómo piense publicar una aplicación de Xamarin.iOS, tendrá que crear un *perfil de aprovisionamiento de distribución* específico para ella. Este perfil permite que la aplicación se firme digitalmente para su publicación de modo que se pueda instalar en un dispositivo iOS. Del mismo modo que un perfil de aprovisionamiento de desarrollo, un perfil de distribución incluirá lo siguiente:

- Un identificador de aplicación
- Un certificado de distribución

Puede seleccionar el **identificador de la aplicación** y los **dispositivos** que usó para el perfil de aprovisionamiento de desarrollo, pero si todavía no tiene uno, tendrá que crear un certificado de distribución para identificar a la organización al enviar la aplicación a la tienda de aplicaciones. En la siguiente sección se explican los pasos para crear un certificado de distribución.

> [!NOTE]
> Solo los agentes de equipo y los administradores pueden crear certificados de distribución y perfiles de aprovisionamiento.

<a name="creatingcertificate" />

## <a name="creating-a-distribution-certificate"></a>Creación de un certificado de distribución

1. Vaya a la sección *Certificates, Identifiers & Profiles* (Certificados, identificadores y perfiles) del Centro de usuarios registrados de Apple Developer.
2. En *Certificates* (Certificados), seleccione **Production** (Producción).
3. Haga clic en el botón **+** para crear un nuevo certificado.
4. En el encabezado *Producción*, seleccione **App Store and Ad Hoc** (App Store y ad hoc):

    [![](images/createcertmanually01.png "Selección de App Store y ad hoc")](images/createcertmanually01.png#lightbox)
5. Haga clic en **Continuar** y siga las instrucciones para crear una solicitud de firma de certificado (CSR) a través de Acceso a llaves:

    [![](images/createcertmanually02.png "Creación de una solicitud de firma de certificado a través de Acceso a llaves")](images/createcertmanually02.png#lightbox)
6. Una vez que haya creado la CSR tal como se indica, haga clic en **Continuar** y cárguela en el Centro de usuarios registrados:

    [![](images/createcertmanually03.png "Carga de la CSR en el Centro de usuarios registrados")](images/createcertmanually03.png#lightbox)

7. Haga clic en **Generar** para crear el certificado.
8. Por último, **descargue** el certificado completado y haga doble clic en el archivo para instalarlo.
9. En este punto el certificado debería estar instalado en el equipo, aunque es posible que tenga que [actualizar sus perfiles](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) para asegurarse de que sean visibles en Xcode.

Como alternativa, se puede solicitar un certificado mediante el cuadro de diálogo Preferencias de Xcode. Para ello, siga estos pasos:

1.   Seleccione el equipo y haga clic en **Manage Certificates…** (Administrar certificados): [![](images/selectteam.png "Seleccionar el equipo y ver los detalles")](images/selectteam.png#lightbox).

2.   A continuación, haga clic en el botón **Create** (Crear) junto a **iOS Distribution Certificate** (Certificado de distribución de iOS): [![](images/selectcert.png "Crear un certificado de distribución de iOS")](images/selectcert.png#lightbox).

3.   Según los privilegios del equipo, se generará la identidad de firma, tal como se muestra a continuación, o puede que tenga que esperar hasta que un administrador o agente de equipo la apruebe: [![](images/generated.png "se genera la identidad de firma y se muestra un cuadro de diálogo")](images/generated.png#lightbox).


<a name="creatingprofile" />

## <a name="creating-a-distribution-profile"></a>Creación de un perfil de distribución

<a name="creatingappid" />

### <a name="creating-an-app-id"></a>Creación de un identificador de aplicación

Al igual que con cualquier otro perfil de aprovisionamiento que cree, se necesita un identificador de aplicación para identificar la aplicación que va a distribuir al dispositivo del usuario. Si aún no lo ha creado, siga estos pasos para hacerlo:


1. En el [Centro de usuarios registrados de Apple Developer](https://developer.apple.com/account/overview.action), vaya a la sección *Certificate, Identifiers and Profiles* (Certificado, identificadores y perfiles). Seleccione **App IDs** (Identificadores de aplicación) en **Identifiers** (Identificadores).
2. Haga clic en el botón **+** y proporcione un **Nombre** que la identifique en el portal.
3. El prefijo de la aplicación ya debería estar establecido como el identificador del equipo y no se puede cambiar. Seleccione un identificador de aplicación explícito o comodín y especifique un identificador de paquete en un formato DNS inverso como:
    - **Explícito**: com.[nombreDeDominio].[nombreDeAplicación]
    - **Comodín**: com.[nombreDeDominio].*
4. Seleccione los [App Services](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices) que la aplicación necesite.
5. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el identificador de aplicación.


### <a name="creating-a-provisioning-profile"></a>Crear un perfil de aprovisionamiento

Una vez que tenga los componentes necesarios para crear un perfil de distribución, siga estos pasos para crearlo:

1. Vuelva al portal de aprovisionamiento de Apple y seleccione **Aprovisionamiento** > **Distribución**:

    [![](images/distribute01.png "Selección de Aprovisionamiento > Distribución")](images/distribute01.png#lightbox)

2. Haga clic en el botón **+** y seleccione el tipo de perfil de distribución que quiere crear como **App Store**:

    [![](images/distribute02.png "Creación de un perfil de distribución de App Store")](images/distribute02.png#lightbox)

3. Haga clic en el botón **Continuar** y seleccione el identificador de la aplicación en la lista desplegable para la que quiere crear un perfil de distribución:

    [![](images/distribute03.png "Selección de Id. de aplicación en la lista desplegable")](images/distribute03.png#lightbox)

4. Haga clic en el botón **Continuar** y seleccione el certificado necesario para firmar la aplicación:

    [![](images/distribute04.png "Selección del certificado necesario para firmar la aplicación")](images/distribute04.png#lightbox)

5. Haga clic en el botón **Continuar** y seleccione los dispositivos iOS en los que se podrá ejecutar la aplicación de Xamarin.iOS:

    [![](images/distribute05.png "Selección de los dispositivos iOS en los que se puede ejecutar la aplicación")](images/distribute05.png#lightbox)

6. Haga clic en el botón **Continuar** y escriba un **Nombre** para el nuevo perfil de distribución:

    [![](images/distribute06.png "Escribir un nombre para el nuevo perfil de distribución")](images/distribute06.png#lightbox)

7. Haga clic en el botón **Generar** para crear el nuevo perfil y finalizar el proceso.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 Es posible que tenga que salir de Visual Studio para Mac y hacer que Xcode actualice su lista de identidades de firma y perfiles de aprovisionamiento disponibles (con las instrucciones de la sección [Solicitud de identidades de firma](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) para que un nuevo perfil de distribución esté disponible en Visual Studio para Mac.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 Es posible que tenga que salir de Visual Studio y hacer que Xcode (en el equipo Mac del host de compilación) actualice su lista de identidades de firma y perfiles de aprovisionamiento disponibles (con las instrucciones de la sección [Solicitud de identidades de firma](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) para que un nuevo perfil de distribución esté disponible en Visual Studio.

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Selección de un perfil de distribución en un proyecto de Xamarin.iOS

Cuando esté listo para realizar una compilación final de una aplicación de Xamarin.iOS para su venta en iTunes App Store, seleccione el perfil de distribución que creó arriba.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 En Visual Studio para Mac, haga lo siguiente:

1. Haga doble clic en el nombre del proyecto en el **Explorador de soluciones** para abrirlo para su edición.
2. Seleccione **Firma del lote de iOS** y **Versión | iPhone** en el elemento desplegable **Configuración**:

    ![](images/releasexs01.png "Seleccione Versión | iPhone en la lista desplegable Configuración")
3. En la mayoría de los casos, la **Identidad de firma** y el **Perfil de aprovisionamiento** pueden dejarse en su valor predeterminado **Automático** y Visual Studio para Mac elegirá el perfil correcto según el identificador de paquete de Info.plist:

    ![](images/releasexs02.png "Identidad de firma y perfil de aprovisionamiento establecidos en los valores predeterminados de Automático")
4. Si fuera necesario, seleccione la identidad de firma y el perfil de distribución (creado anteriormente) en las listas desplegables:

    ![](images/releasexs03.png "Seleccione la identidad de firma y los perfiles de distribución")
5. Haga clic en el botón **Aceptar** para guardar los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 En Visual Studio, haga lo siguiente:

1. Haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones** y seleccione **Propiedades** para abrirlo para su edición.
2. Seleccione **Firma del lote de iOS** y **Versión | iPhone** en el elemento desplegable **Configuración**:

    ![](images/releasevs01.png "Seleccione Versión | iPhone en la lista desplegable Configuración")
3. En la mayoría de los casos, los campos **Identidad de firma** y el **Perfil de aprovisionamiento** pueden dejarse en su valor predeterminado **Automático** y Visual Studio elegirá el perfil correcto según el identificador de paquete de Info.plist.

    ![](images/releasevs02.png "Identidad de firma y perfil de aprovisionamiento establecidos en los valores predeterminados de Automático")
4. Si fuera necesario, seleccione la identidad de firma y el perfil de distribución (creado anteriormente) en las listas desplegables:

    ![](images/releasevs03.png "Seleccione la identidad de firma y el perfil de distribución")
5. Guarde los cambios en las propiedades del proyecto.

-----

<a name="itunesconnect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configuración de la aplicación en iTunes Connect

Una vez que la aplicación se ha aprovisionado correctamente, el siguiente paso es configurar las aplicaciones en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa), que es un conjunto de herramientas basadas en web para, entre otras cosas, administrar aplicaciones de iOS en el App Store.

La aplicación de Xamarin.iOS deberá estar correctamente instalada y configurada en iTunes Connect para que se pueda enviar a Apple para su revisión y publicación como una aplicación gratuita o para su venta en el App Store.

Para obtener información más detallada, vea la documentación [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

<a name="submitting" />

## <a name="submitting-an-app-to-itunes-connect"></a>Envío de una aplicación a iTunes Connect

Una vez que la aplicación está firmada con el perfil de aprovisionamiento de distribución y creada en iTunes Connect, su archivo binario se carga en Apple para su revisión. Tras una revisión correcta por parte de Apple, se publica en el App Store.

Para más información sobre la publicación de aplicaciones en el App Store, vea [Publicación en el App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md).

<a name="windows" />

## <a name="automatically-copy-app-bundles-back-to-windows"></a>Copia automática de lotes de aplicaciones .app en Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="summary"></a>Resumen

En este artículo se ha hablado de los componentes fundamentales para la preparación de una aplicación de Xamarin.iOS para su distribución en el App Store.

## <a name="related-links"></a>Vínculos relacionados

- [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Archivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Compatibilidad con IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
