---
title: Razor-Pages Autorisierung Konventionen in ASP.NET Core
author: guardrex
description: "Informationen Sie zum Zugriff auf den Seiten mit den Konventionen beim Start zu steuern, die Autorisierung von Benutzern und ermöglichen anonyme Benutzern den Zugriff auf einzelne Seiten oder Ordner von Seiten."
manager: wpickett
ms.author: riande
ms.date: 10/27/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 2bad6e1cc654b972206af03f99160628f81e026f
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor-Pages Autorisierung Konventionen in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Eine Möglichkeit zum Steuern des Zugriffs in Ihrer app Razor-Pages ist Autorisierung Konventionen beim Start verwendet. Diese Konventionen können Sie zum Autorisieren von Benutzern und Zulassen von anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner von Seiten. Die Konventionen, die automatisch in diesem Thema beschriebenen gelten [Autorisierungsfilter](xref:mvc/controllers/filters#authorization-filters) zum Steuern des Zugriffs.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="require-authorization-to-access-a-page"></a>Autorisierung zum Zugriff auf einer Seite erforderlich

Verwenden der [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) Konvention über [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) Hinzufügen einer [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf der Seite unter dem angegebenen Pfad:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,4)]

Der angegebene Pfad ist Ansichtsmodul-Pfads, der Razor-Pages Stamm relativen Pfad ohne Erweiterung und enthält nur Schrägstriche ist.

Ein [AuthorizePage Überladung](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_AuthorizePage_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_String_System_String_) ist verfügbar, wenn Sie eine Autorisierungsrichtlinie angeben müssen.

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Benötigen Sie die Autorisierung zum Zugriff auf eines Ordners von Seiten

Verwenden der [AuthorizeFolder](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) Konvention über [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) Hinzufügen einer [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) für alle Seiten in einem Ordner unter dem angegebenen Pfad:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,5)]

Der angegebene Pfad ist Ansichtsmodul-Pfads, der relative Razor-Pages Stammpfad ist.

Ein [AuthorizeFolder Überladung](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_AuthorizeFolder_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_String_System_String_) ist verfügbar, wenn Sie eine Autorisierungsrichtlinie angeben müssen.

## <a name="allow-anonymous-access-to-a-page"></a>Anonymen Zugriff auf eine Seite zulassen

Verwenden der [AllowAnonymousToPage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) Konvention über [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) Hinzufügen einer [AllowAnonymousFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) zu einer Seite im angegebenen Pfad:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,6)]

Der angegebene Pfad ist Ansichtsmodul-Pfads, der Razor-Pages Stamm relativen Pfad ohne Erweiterung und enthält nur Schrägstriche ist.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Anonymen Zugriff auf einen Ordner der Seiten zulassen

Verwenden der [AllowAnonymousToFolder](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) Konvention über [AddRazorPagesOptions](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.addrazorpagesoptions) Hinzufügen einer [AllowAnonymousFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) für alle Seiten in einem Ordner unter dem angegebenen Pfad:

[!code-csharp[Main](razor-pages-authorization/sample/Startup.cs?name=snippet1&highlight=2,7)]

Der angegebene Pfad ist Ansichtsmodul-Pfads, der relative Razor-Pages Stammpfad ist.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Hinweis zur Kombination von autorisiert und anonymem Zugriff

Es ist perfekt geeignet, um anzugeben, dass einen Ordner der Seiten, die Autorisierung erfordern, und gibt an, dass eine Seite in diesem Ordner den anonymen Zugriff zulässt:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Das Gegenteil ist jedoch nicht "true". Sie können nicht deklarieren einen Ordner der Seiten, die für den anonymen Zugriff, und geben Sie die Seite für die Autorisierung in:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private") 
```

Die notwendige Autorisierung auf der Seite "Private" funktioniert nicht, da bei der sowohl die `AllowAnonymousFilter` und `AuthorizeFilter` Filter werden angewendet, um die Seite die `AllowAnonymousFilter` gewinnt, und steuert den Zugriff.

## <a name="see-also"></a>Siehe auch

* [Benutzerdefinierte Routen- und Seitenmodellanbieter für Razor-Pages](xref:mvc/razor-pages/razor-pages-convention-features)
* [PageConventionCollection](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection) Klasse
