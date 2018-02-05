---
title: Erste Schritte mit Razor-Pages mit ASP.NET Core unter Mac
author: rick-anderson
description: "Erster Schritte bei Razor-Pages in ASP.NET Core mithilfe von Visual Studio für Mac"
manager: wpickett
ms.author: riande
ms.date: 07/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: 9e7d1db47e4cc9d753b1629e20345ca1f4403b2f
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-for-mac"></a>Erste Schritte mit Razor-Pages in ASP.NET Core mit Visual Studio für Mac

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor-Pages. Es wird empfohlen, zuerst das Tutorial [Einführung in Razor-Pages](xref:mvc/razor-pages/index) zu lesen, bevor Sie mit diesem Tutorial beginnen. Razor-Pages sind der empfohlene Weg für die Erstellung von Benutzeroberflächen für Webanwendungen in ASP.NET Core.

## <a name="prerequisites"></a>Erforderliche Komponenten

Installieren Sie Folgendes:

* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) oder höher
* [Visual Studio für Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-razor-web-app"></a>Erstellen einer Razor-Web-App

Führen Sie über ein Terminal die folgenden Befehle aus:

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

Diese Befehle verwenden die [.NET Core-CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet), um ein Projekt mit Razor-Pages zu erstellen und auszuführen. Öffnen Sie in einem Browser „http://localhost:5000“, um sich die Anwendung anzeigen zu lassen.

![Start- oder Indexseite](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a>Öffnen des Projekts

Drücken Sie STRG+C, um die Anwendung zu beenden.

Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.

### <a name="launch-the-app"></a>Starten der App

Klicken Sie in Visual Studio auf **Ausführen > Ohne Debugging starten**, um die App zu starten. Visual Studio startet dann [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) und einen Browser und navigiert zu `http://localhost:5000`.

Im nächsten Tutorial wird dem Projekt ein Modell hinzugefügt.

>[!div class="step-by-step"]
[Weiter: Hinzufügen eines Modells](xref:tutorials/razor-pages-mac/model)
