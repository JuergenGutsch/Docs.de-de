---
title: "Microsoft.AspNetCore.All Metapackage für ASP.NET Core 2.x und höher"
author: Rick-Anderson
description: "Die Microsoft.AspNetCore.All Metapackage enthält alle unterstützten ASP.NET Core und Entity Framework Core-Pakete, zusammen mit ihren Abhängigkeiten."
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: ff25d80be907994f7ac3d64a8ffa39ae53278ba6
ms.sourcegitcommit: 73bf6b222474d9f1f6aba3feaca4e191069d2121
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a>Microsoft.AspNetCore.All Metapackage für ASP.NET Core 2.x

Diese Funktion erfordert, dass ASP.NET Core 2.x Zielgruppenadressierung für .NET Core 2.x.

Das Metapaket [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) für ASP.NET Core enthält:

* alle unterstützten Pakete des ASP.NET Core-Teams
* alle unterstützten Pakete von Entity Framework Core 
* interne und Drittanbieterabhängigkeiten, die von ASP.NET Core und Entity Framework Core verwendet werden 

Alle Funktionen von ASP.NET Core 2.x und Entity Framework Core 2.x befinden sich die `Microsoft.AspNetCore.All` Paket. Die Standard-Projektvorlagen verwenden dieses Paket an.

Die Versionsnummer der `Microsoft.AspNetCore.All` Metapackage darstellt, die ASP.NET Core und Entity Framework Core-Version (mit der Version von .NET Core ausgerichtet).

Anwendungen, die die `Microsoft.AspNetCore.All` Metapackage automatisch nutzen die [.NET Core-Laufzeit Store](https://docs.microsoft.com/dotnet/core/deploying/runtime-store). Der Common Language Runtime-Speicher enthält alle Common Language Runtime-Objekte, die zum Ausführen von ASP.NET Core 2.x-Clientanwendungen erforderlich sind. Bei Verwendung der `Microsoft.AspNetCore.All` Metapackage, **keine** Objekte aus der referenzierten ASP.NET Core NuGet-Pakete werden mit der Anwendung bereitgestellt &mdash; .NET Core-Runtime-Store enthält diese Ressourcen. Die Ressourcen in der Common Language Runtime Speicher vorkompiliert um Anwendungsstartzeit zu verbessern.

Sie können das Trimming gliedert verwenden, so entfernen Sie Pakete, die Sie nicht verwenden. Zugeschnittene Pakete werden in der veröffentlichten Anwendungsausgabe ausgeschlossen.

Die folgenden *csproj* Dateiverweise der `Microsoft.AspNetCore.All` Metapackage für ASP.NET Core:

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
