---
title: Limitaciones
description: Algunas limitaciones de Xamarin Live Player
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: a3aea31f32e3bf6227cce2e3340e3d04828472a0
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="limitations"></a>Limitaciones

![Característica de vista previa](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos del dispositivo
La aplicación Xamarin Live Player es compatible con los siguientes dispositivos:

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 o posterior.
- ARM-v7a, ARM v8a, ARM64-v8a, x 86 o procesador x86_64.

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 o posterior.
- Procesador ARM64.

-----

## <a name="limitations"></a>Limitaciones

Hay algunas limitaciones en lo que se puede ejecutar en Xamarin Live Player, incluidos los siguientes elementos:

### <a name="xamarinforms"></a>Xamarin.Forms
- No se admiten los representadores personalizados.
- No se admiten efectos.
- No se admiten los controles personalizados con propiedades enlazables personalizadas.
- No se admiten los recursos incrustados (p. ej., insertar imágenes u otros recursos en una PCL).
- No se admiten los marcos MVVM de otros fabricantes (p. ej., Prism, Mvvm Cross, Mvvm Light, etc.).
- No se admiten los catálogos de activos en iOS.

### <a name="other-project-types"></a>Otros tipos de proyecto
- Live Player no está destinado a proyectos nativos de Android o iOS (que usan XML de Android o guiones gráficos para la interfaz de usuario).

### <a name="misc"></a>Varios
- Compatibilidad limitada con la reflexión (actualmente, afecta a algunos NuGets populares, como SQLite y Json.NET). Puede que otros NuGets sigan siendo compatibles. Otros NuGets todavía puede ser compatible.
- No se pueden reemplazar algunas clases del sistema (por ejemplo, no puede implementar una subclase).
- Algunas características de la plataforma que requieren aprovisionamiento no funcionan en la aplicación Xamarin Live Player (en cambio, se ha configurado para operaciones comunes, como el acceso a la Galería fotográfica).
- Se omiten los destinos personalizados y los pasos de compilación. Por ejemplo, no se pueden incorporar herramientas como Fody, Refit, AutoFac y AutoMapper.
- No se admiten los proyectos de F# en Android y la compatibilidad con iOS es limitada.
- Escenarios avanzados con clases genéricas personalizadas e interfaces podrían no admitirse.

Notifique los problemas adicionales en [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Vínculos relacionados

- [Solución de problemas](~/tools/live-player/troubleshooting.md)
- [Configuración](~/tools/live-player/install.md)
