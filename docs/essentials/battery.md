---
title: Batería Xamarin.Essentials
description: La clase de la batería le permite comprobar información sobre la batería del dispositivo y supervise los cambios realizados.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 984f6f5eeeba3d5c8162ab6ce0c83dbf03981b5f
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-battery"></a>Batería Xamarin.Essentials

![La versión preliminar de NuGet](~/media/shared/pre-release.png)

El **batería** clase le permite comprobar el dispositivo información sobre la batería y supervise los cambios realizados.

## <a name="getting-started"></a>Introducción

Para tener acceso a la **batería** se requiere la siguiente configuración específica de plataforma de funcionalidad.

# <a name="androidtabandroid"></a>[Android](#tab/android)

El `Battery` permiso es necesario y debe configurarse en el proyecto de Android. Esto puede agregarse de las maneras siguientes:

Abra la **AssemblyInfo.cs** de archivos en el **propiedades** carpeta y agregar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

O bien, actualice el manifiesto de Android:

Abra la **AndroidManifest.xml** de archivos en el **propiedades** carpeta y agregue lo siguiente dentro de la **manifiesto** nodo.

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

O haga clic con el botón secundario en el proyecto de Android y abra las propiedades del proyecto. En **manifiesto Android** buscar el **los permisos necesarios:** área y compruebe el **batería** permiso. Esto actualizará automáticamente el **AndroidManifest.xml** archivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

No se necesita ninguna instalación adicional.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

No se necesita ninguna instalación adicional.

-----

## <a name="using-battery"></a>Uso de la batería

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Compruebe la información actual de la batería:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or -1.0 if unable to determine.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.Ac:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

Cada vez que se desencadena cualquiera de un evento de cambios de propiedades de la batería:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>Diferencias entre las plataformas

| Plataforma | Diferencia |
| --- | --- |
| iOS | Dispositivo debe utilizarse para probar las API. |
| iOS | Solo devolverá corriente alterna o batería para PowerSource. |
| UWP | Solo devolverá corriente alterna o batería para PowerSource. |

## <a name="api"></a>API

- [Código fuente de batería](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentación de la API de batería](xref:Xamarin.Essentials.Battery)
