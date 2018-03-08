---
title: Ersetzen von `<machineKey>` in ASP.NET
author: rick-anderson
description: Ersetzen von `<machineKey>` in ASP.NET
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2c2f6e04c42d547592cc1c2e6e66da7133b8bbef
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="replacing-machinekey-in-aspnet"></a>Ersetzen von `<machineKey>` in ASP.NET

<a name="compatibility-replacing-machinekey"></a>

Die Implementierung der `<machineKey>` Element in ASP.NET [ist ersetzbare](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Dadurch werden die meisten Aufrufe von ASP.NET kryptografischen Routinen über eine Ersetzung Daten Schutzmechanismus einschließlich der neuen Datenschutzsystem geleitet werden.

## <a name="package-installation"></a>Paketinstallation

> [!NOTE]
> Die neue Datenschutzsystem kann nur in einer vorhandenen ASP.NET-Anwendung mit der Zielversion .NET 4.5.1 installiert oder höher sein. Installation wird fehlschlagen, wenn die Anwendung als Ziel .NET 4.5 verwendet oder zu verringern.

Um die neue Datenschutzsystem in einem vorhandenen 4.5.1+ ASP.NET-Projekt zu installieren, installieren Sie das Paket Microsoft.AspNetCore.DataProtection.SystemWeb. Dies wird instanziiert, die Schutz System mithilfe der [Standardkonfiguration](xref:security/data-protection/configuration/default-settings) Einstellungen.

Wenn Sie das Paket installieren, fügt eine Zeile in *"Web.config"* , informiert, dass ASP.NET für die Verwendung [am Kryptografievorgänge](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), einschließlich Formularauthentifizierung, Ansichtszustand und Aufrufe an MachineKey.Protect. Die Zeile, die eingefügt wird, lautet wie folgt.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Sie können feststellen, ob das neue Data Protection System aktiv ist, durch Überprüfen von Feldern wie etwa `__VIEWSTATE`, die mit "CfDJ8" beginnen soll, wie im folgenden Beispiel. "CfDJ8" wird die base64-Darstellung des Headers Magic "09 d0 C9 d0", die eine Nutzlast, die durch die Datenschutzsystem geschützt identifiziert.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a>Paketkonfiguration

Die Datenschutzsystem instanziiert wird mit einer Standardkonfiguration für die Einrichtung von 0 (null). Jedoch, da standardmäßig Schlüssel im lokalen Dateisystem beibehalten werden, dies für Anwendungen funktioniert nicht, die in einer Farm bereitgestellt werden. Zum Beheben dieses Problems können Sie die Konfiguration durch Erstellen eines Typs welche Unterklassen DataProtectionStartup bereitstellen und überschreibt die ConfigureServices-Methode.

Im folgenden finden Sie ein Beispiel für eine benutzerdefinierte Daten Schutz Starttyp konfiguriert sowohl, auf dem Schlüssel gespeichert sind und wie sie im Ruhezustand verschlüsselt sind. Dabei wird auch die Standardrichtlinie für app-Isolation, durch die Bereitstellung der eigenen Anwendungsnamens.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> Sie können auch `<machineKey applicationName="my-app" ... />` anstelle eines expliziten Aufrufs von SetApplicationName. Hierbei handelt es sich um eine benutzerfreundliche-Mechanismus zum Vermeiden des Entwicklers einen DataProtectionStartup-abgeleiteten Typ zu erstellen, wenn wurde alles, was sie verwenden wollten, konfigurieren Sie den Namen der Anwendung festlegen.

Um diese benutzerdefinierte Konfiguration zu aktivieren, wechseln Sie zurück zur Datei "Web.config", und suchen Sie nach der `<appSettings>` Element, das das Paket zu installieren, auf die Datei "App.config" hinzugefügt. Es sieht wie das folgende Markup:

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

Füllen Sie die leeren Wert festgelegt und die Assembly qualifizierte Name des Typs DataProtectionStartup abgeleitet, die Sie gerade erstellt haben. Wenn der Name der Anwendung DataProtectionDemo ist, würde dies wie folgt aussehen die unten.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Die neu konfigurierte Datenschutzsystem kann jetzt zur Verwendung in der Anwendung.
