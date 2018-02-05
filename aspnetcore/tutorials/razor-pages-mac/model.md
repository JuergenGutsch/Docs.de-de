---
title: "Hinzufügen eines Modells zu einer Razor-Pages-App mit Visual Studio für Mac"
author: rick-anderson
description: "Hinzufügen eines Modells zu einer Razor-Pages-App in ASP.NET Core mithilfe von Visual Studio für Mac"
manager: wpickett
ms.author: riande
ms.date: 08/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages-mac/model
ms.openlocfilehash: b8e5d65e195f9824602ec15d05dc013faa2a8dc9
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="adding-a-model-to-a-razor-pages-app-in-aspnet-core-with-visual-studio-for-mac"></a>Hinzufügen eines Modells zu einer Razor-Pages-App in ASP.NET Core mit Visual Studio für Mac

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Hinzufügen eines Datenmodells

* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt**RazorPagesMovie**, und klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.
* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.
* Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:

  * Klicken Sie im linken Bereich auf **Allgemein**.
  * Klicken Sie im mittleren Bereich auf **Leere Klasse**.
  * Geben Sie der Klasse den Namen **Film**, und klicken Sie auf **Neu**.

[!INCLUDE[model 2](../../includes/RP/model2.md)]
[!INCLUDE[model 2a](../../includes/RP/model2a.md)]

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices2&highlight=3-6)]

Klicken Sie mit der rechten Maustaste auf eine rote Wellenlinie, z.B. `MovieContext` in der Zeile `services.AddDbContext<MovieContext>(options =>`. Klicken Sie auf **Quick Fix > using RazorPagesMovie.Models;** (Schnelle Problembehebung > Verwenden von RazorPagesMovie.Models;). Visual Studio fügt die „using“-Anweisung hinzu.

Erstellen Sie das Projekt, um sicherzustellen, dass keine Fehler vorliegen.

![Seite „Erstellen“](model/red.png)

### <a name="entity-framework-core-nuget-packages-for-migrations"></a>NuGet-Pakete für Migrationen in Entity Framework Core

Die EF-Tools für die Befehlszeilenschnittstelle (CLI) werden unter [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet) bereitgestellt. Klicken Sie auf den Link [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet), um die Versionsnummer abzufragen, die verwendet werden soll. Fügen Sie das Paket zum Installieren zu der `DotNetCliToolReference`-Sammlung in der *CSPROJ*-Datei hinzu. **Hinweis:** Sie müssen dieses Paket installieren, indem Sie die *CSPROJ*-Datei bearbeiten. Sie können den `install-package`-Befehl oder die Paket-Manager-GUI nicht verwenden.

So bearbeiten Sie eine *CSPROJ*-Datei:

* Klicken Sie auf **Datei**  > **Öffnen**, und wählen Sie dann die *CSPROJ*-Datei aus.
* Klicken Sie auf **Optionen**.
* Ändern Sie **Öffnen mit** zu **Quellcode-Editor**.

![Bearbeiten der CSPROJ-Datei](model/csproj.png)

Fügen Sie dann der zweiten **\<ItemGroup>** den Toolverweis `Microsoft.EntityFrameworkCore.Tools.DotNet` hinzu:

[!code-xml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_cli_sample/RazorPagesMovie/RazorPagesMovie.cli.csproj?highlight=10)]

Die Versionsnummern, die im folgenden Code angezeigt werden, waren zu dem Zeitpunkt korrekt, als der Code geschrieben wurde.

[!INCLUDE[model3](../../includes/RP/model3.md)]
[!INCLUDE[model 4x](../../includes/RP/model4x.md)]

[!INCLUDE[model 4 exit](../../includes/RP/model4exit.md)]

[!INCLUDE[model 4](../../includes/RP/model4.md)]

### <a name="add-the-pagesmovies-files-to-the-project"></a>Hinzufügen der Seiten/Filmdateien zum Projekt

* Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner *Seiten*, und klicken Sie auf **Hinzufügen > Vorhandenen Ordner hinzufügen**.
* Wählen Sie den Ordner *Filme* aus.
* Klicken Sie im Dialogfeld *Dateien zum Einschließen in das Projekt auswählen* auf **Alle einschließen**.

Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.

>[!div class="step-by-step"]
[Vorheriges Tutorial: Getting Started (Erste Schritte)](xref:tutorials/razor-pages-mac/razor-pages-start)
[Nächstes Tutorial: Scaffolded Razor Pages (Razor-Pages mit erfolgtem Gerüstbau)](xref:tutorials/razor-pages-mac/page)
