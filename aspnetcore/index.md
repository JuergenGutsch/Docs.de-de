---
title: "Einführung in ASP.NET Core"
author: rick-anderson
description: "Bietet eine Einführung in ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 12/12/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: index
ms.openlocfilehash: 42bf005a29a8723233a0d9e5532827f7522f0e75
ms.sourcegitcommit: 7a87d66cf1d01febe6635c7306f2f679434901d1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2018
---
# <a name="aspnet-core"></a>ASP.NET Core

Von [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core ist ein plattformübergreifendes, leistungsstarkes [Open-Source](https://github.com/aspnet/home)-Framework zum Erstellen moderner, cloudbasierter mit dem Internet verbundener Anwendungen. ASP.NET Core ermöglicht Folgendes:

* Erstellen von Web-Apps und -diensten, [IoT-Apps](https://www.microsoft.com/internet-of-things/) und mobilen Back-Ends.
* Verwenden Ihrer bevorzugten Entwicklungstools unter Windows, macOS und Linux
* Bereitstellen in der Cloud oder im lokalen System
* Ausführen in [.NET Core oder .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)

## <a name="why-use-aspnet-core"></a>Gründe für ASP.NET Core

Millionen von Entwicklern setzen bei der Erstellung von Web-Apps auf [ASP.NET 4.x](https://docs.microsoft.com/aspnet/overview). Bei ASP.NET Core handelt es sich um eine Neugestaltung von ASP.NET 4.x mit Änderungen an der Architektur, die ein schlankeres Framework mit größerer Modularität ergeben.

ASP.NET Core bietet die folgenden Vorteile:

* Eine einheitliche Umgebung zum Erstellen der Webbenutzeroberfläche und von Web-APIs
* Integration von [modernen clientseitigen Frameworks](xref:client-side/index) und Entwicklungsworkflows
* Ein cloudfähiges auf der Umgebung basierendes [Konfigurationssystem](xref:fundamentals/configuration/index)
* Integrierte [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)
* Eine schlanke, [leistungsstarke](https://github.com/aspnet/benchmarks) und modulare HTTP-Anforderungspipeline
* Möglichkeit des Hostens in [IIS](xref:host-and-deploy/iis/index), [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), [Docker](xref:host-and-deploy/docker/index) oder eigenständigen Hostens in Ihrem eigenen Prozess
* Parallele App-Versionsverwaltung bei Festlegung von [.NET Core](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server) als Zielversion
* Tools zum Vereinfachen einer modernen Webentwicklung
* Fähigkeit zur Erstellung und Ausführung unter Windows, macOS und Linux
* Open Source und mit [Fokus auf der Community](https://live.asp.net/)

ASP.NET Core besteht vollständig aus [NuGet](https://www.nuget.org/)-Paketen. Dadurch können Sie Ihre App so optimieren, dass nur die benötigten NuGet-Pakete enthalten sind. Tatsächlich ist für ASP.NET Core 2.x-Apps, deren Zielversionen auf .NET Core festgelegt werden soll, lediglich ein [einzelnes NuGet-Paket](xref:fundamentals/metapackage). Die Vorteile eines kleineren App-Oberflächenbereichs umfassen straffere Sicherheit, verringerte Wartungsarbeiten und verbesserte Leistung.

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Erstellen von Web-APIs und Webbenutzeroberflächen mithilfe von ASP.NET Core MVC

ASP.NET Core MVC bietet Funktionen zum Erstellen von [Web-APIs](xref:tutorials/index#build-web-apis) und [Web-Apps](xref:tutorials/index#build-web-apps):

* Das Muster [Model-View-Controller (MVC)](xref:mvc/overview) sorgt dafür, dass Ihre Web-APIs und Web-Apps [testfähig](testing/index.md) sind.
* [Razor-Pages](xref:mvc/razor-pages/index) (neu in ASP.NET Core 2.0) sind ein seitenbasiertes Programmiermodell, mit dem das Erstellen einer Webbenutzeroberfläche einfacher und produktiver wird.
* Das [Razor-Markup](xref:mvc/views/razor) bietet eine produktive Syntax für [Razor-Pages](xref:mvc/razor-pages/index) und [MVC-Ansichten](xref:mvc/views/overview).
* [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.
* Die integrierte Unterstützung für [mehrere Datenformate und Inhaltsaushandlung](mvc/models/formatting.md) ermöglicht Ihren Web-APIs das Erreichen einer breiten Palette von Clients, wie z.B. Browser und Mobilgeräte.
* Die [Modellbindung](xref:mvc/models/model-binding) ordnet Daten aus HTTP-Anforderungen automatisch Aktionsmethodenparametern zu.
* Die [Modellvalidierung](xref:mvc/models/validation) führt automatisch eine client- und serverseitige Validierung aus.

## <a name="client-side-development"></a>Clientseitige Entwicklung

ASP.NET Core integriert sich nahtlos in gängige clientseitige Frameworks und Bibliotheken, einschließlich [Angular](xref:spa/angular), [React](xref:spa/react) und [Bootstrap](xref:client-side/bootstrap). Weitere Details finden Sie unter [Clientseitige Entwicklung](xref:client-side/index).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Erste Schritte mit Razor-Pages](xref:tutorials/razor-pages/razor-pages-start)
* [ASP.NET Core-Tutorials](xref:tutorials/index)
* [ASP.NET Core – Grundlagen](xref:fundamentals/index)
* Im [wöchentlichen ASP.NET Community Standup](https://live.asp.net/) werden die Fortschritte und Pläne des Teams behandelt. Zudem werden neue Blogs und Drittanbietersoftware vorgestellt.
