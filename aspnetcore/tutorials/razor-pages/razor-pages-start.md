---
title: Erste Schritte mit Razor-Seiten mit ASP.NET Core
author: rick-anderson
description: Erste Schritte mit Razor-Seiten mit ASP.NET Core
keywords: ASP.NET Core, Razor-Seiten, Razor, MVC
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 9d647657f21dd7e952808e5fe020f7a9e8767cd8
ms.sourcegitcommit: 3ba32b2b6425ed94604cb0f681db0d5bb5f8ad58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2017
---
# <a name="get-started-with-razor-pages-in-aspnet-core"></a>Erste Schritte mit Razor-Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor-Pages. Razor-Pages sind der empfohlene Weg für die Erstellung von Benutzeroberflächen für Web-Apps in ASP.NET Core.

Es gibt drei Versionen dieses Tutorials:

* Windows: Dieses Tutorial
* macOS: [Erste Schritte mit Razor-Pages mit Visual Studio für Mac](xref:tutorials/razor-pages-mac/razor-pages-start)
* macOS, Linux und Windows: [Erste Schritte mit Razor-Pages in ASP.NET Core mit Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Komponenten

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="create-a-razor-web-app"></a>Erstellen einer Razor-Web-App

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Erstellen Sie eine neue ASP.NET Core-Webanwendung. Nennen Sie das Projekt **RazorPagesMovie**. Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren/einfügen.
  ![neue ASP.NET Core-Webanwendung](../../mvc/razor-pages/index/_static/np.png)
* Wählen Sie in der Dropdownliste **ASP.NET Core 2.0** aus, und klicken Sie anschließend auf **Webanwendung**.

  [!INCLUDE[install 2.0](../../includes/dotnetcore-on-dotnetfx-vs.md)]

Die Visual Studio-Vorlage erstellt ein Startprojekt:

![Projektmappen-Explorer](razor-pages-start/_static/se.png)

Drücken Sie **F5**, um die App im Debugmodus auszuführen, oder **STRG+F5** zur Ausführung ohne Anfügen des Debuggers.

![Start- oder Indexseite](razor-pages-start/_static/home.png)

* Visual Studio startet [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt Ihre App aus. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für Ihren lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet. In der vorherigen Abbildung ist die Nummer des Ports 5000. Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.
* Das Starten der App mit **STRG+F5** (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

>[!div class="step-by-step"]
[Nächstes Tutorial: Adding a model to a Razor Pages app in ASP.NET Core with Visual Studio for Mac (Hinzufügen eines Modells zu einer App mit Razor-Seiten in ASP.NET Core mit Visual Studio für Mac)](xref:tutorials/razor-pages/model)
