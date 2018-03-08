---
title: Erste Schritte mit ASP.NET Core 2.0
author: rick-anderson
description: "Kurztutorial, in dem eine einfache Hello World-App mit ASP.NET Core erstellt und ausgeführt wird."
manager: wpickett
ms.author: riande
ms.date: 10/18/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: getting-started
ms.openlocfilehash: 5cba57c23ab66475648b19cba3f9aaa02bea3dba
ms.sourcegitcommit: d56f36805ce70470775da58db63c0965d65660d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2017
---
# <a name="get-started-with-aspnet-core"></a>Erste Schritte mit ASP.NET Core

> [!NOTE]
> Diese Anweisungen gelten für die neueste Version von ASP.NET Core. Suchen Sie zum Einstieg eine frühere Version? Siehe die [Version 1.1 dieses Tutorials](xref:getting-started-1.1).

1. Installieren Sie [.NET Core](https://www.microsoft.com/net/core/).

2. Erstellen Sie ein neues .NET Core-Projekt.

   Öffnen Sie unter macOS und Linux ein Terminalfenster. Öffnen Sie unter Windows eine Eingabeaufforderung. Geben Sie folgenden Befehl ein:

    ```terminal
    dotnet new razor -o aspnetcoreapp
    ```
    
4. Führen Sie die App aus.

    Verwenden Sie die folgenden Befehle, um die App auszuführen:

    ```terminal
    cd aspnetcoreapp
    dotnet run
    ```

5. Wechseln Sie dann zu [http://localhost:5000](http://localhost:5000).

6. Öffnen Sie *Pages/About.cshtml*, und verändern Sie die Seite so, dass sie die Meldung „Hallo Welt!“ anzeigt. Die Zeit auf dem Server beträgt @DateTime.Now ":

    [!code-html[Main](getting-started/sample/getting-started/about.cshtml?highlight=9&range=1-9)]

7. Navigieren Sie zu [http://localhost:5000/About](http://localhost:5000/About), und überprüfen Sie die Änderungen.

### <a name="next-steps"></a>Nächste Schritte

Tutorials für die ersten Schritte finden Sie unter [ASP.NET Core-Tutorials](tutorials/index.md).

Eine Einführung in ASP.NET Core-Konzepte und -Architektur finden Sie unter [Einführung in ASP.NET Core](index.md) und [ASP.NET Core – Grundlagen](fundamentals/index.md).

Eine ASP.NET Core-App kann die .NET Core oder .NET Framework-Basisklassenbibliothek und -Laufzeit verwenden. Weitere Informationen finden Sie unter [Wahl zwischen .NET Core und .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).
