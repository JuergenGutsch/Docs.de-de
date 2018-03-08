---
title: "Hinzufügen eines neuen Felds"
author: rick-anderson
description: 
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 9c872a48aba4974ddac2e49ca40c944da356f0e0
ms.sourcegitcommit: 79bbe7481c3d1297a0db8e41dd2b635b0f778264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2017
---
# <a name="adding-a-new-field"></a>Hinzufügen eines neuen Felds

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Abschnitt verwenden Sie [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen zum Hinzufügen eines neuen Felds zum Modell und zum Migrieren dieser Änderung in die Datenbank.

Wenn Sie EF Code First verwenden, um eine Datenbank automatisch zu erstellen, fügt Code First der Datenbank eine Tabelle hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde. Wenn sie nicht synchron sind, löst EF eine Ausnahme aus. Dies erleichtert die Suche nach Problemen mit inkonsistenten Datenbanken bzw. inkonsistentem Code.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Hinzufügen einer Rating-Eigenschaft zum Movie-Modell

Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

Erstellen Sie die App (STRG+UMSCHALT+B).

Da Sie der `Movie`-Klasse ein neues Feld hinzugefügt haben, müssen Sie auch die Positivliste für die Bindung aktualisieren, damit diese neue Eigenschaft eingeschlossen wird. Aktualisieren Sie in *MoviesController.cs* das `[Bind]`-Attribut für die Aktionsmethoden `Create` und `Edit` so, dass die `Rating`-Eigenschaft eingeschlossen wird:

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Sie müssen auch die Ansichtsvorlagen aktualisieren, um die neue `Rating`-Eigenschaft in der Browseransicht anzuzeigen, zu erstellen und zu bearbeiten.

Bearbeiten Sie die Datei */Views/Movies/Index.cshtml*, und fügen Sie das Feld `Rating` hinzu:

[!code-HTML[Main](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

Aktualisieren Sie die Datei */Views/Movies/Create.cshtml* mit dem Feld `Rating`. Sie können die vorherige „Formulargruppe“ kopieren und einfügen und IntelliSense die Felder aktualisieren lassen. IntelliSense nutzt [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro). Hinweis: In der RTM-Version von Visual Studio 2017 müssen Sie die [Razor-Sprachdienste](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices) für Razor IntelliSense installieren. Dies wird in der nächsten Version behoben.

![Der Entwickler hat den Buchstaben R als Attributwert von „asp-for“ im zweiten „label“-Element der Ansicht eingegeben. Ein Intellisense-Kontextmenü mit den verfügbaren Feldern wird angezeigt, einschließlich „Rating“, das in der Liste automatisch hervorgehoben wird. Wenn der Entwickler auf das Feld klickt oder auf der Tastatur die EINGABETASTE drückt, wird der Wert auf „Rating“ festgelegt.](new-field/_static/cr.png)

Die App funktioniert erst, nachdem wir die Datenbank mit dem neuen Feld aktualisiert haben. Wenn Sie sie jetzt ausführen, erhalten Sie die folgende `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Dieser Fehler wird gemeldet, da die aktualisierte Modellklasse „Movie“ sich vom Schema der Tabelle „Movie“ der vorhandenen Datenbank unterscheidet. (Die Datenbanktabelle enthält nicht die Spalte „Rating“.)

Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:

1. Lassen Sie die Datenbank von Entity Framework automatisch löschen und basierend auf dem neuen Modellklassenschema neu erstellen. Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch, wenn die aktive Entwicklung in einer Testdatenbank erfolgt. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell weiterzuentwickeln. Der Nachteil ist jedoch, dass in der Datenbank vorhandene Daten verloren gehen. Deshalb eignet sich dieser Ansatz nicht für eine Produktionsdatenbank! Das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer Anwendung.

2. Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt. Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten. Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.

3. Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.

Für dieses Tutorial verwenden wir Code First-Migrationen.

Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt. Eine Beispieländerung wird nachstehend gezeigt, aber Sie sollten diese Änderung für jedes `new Movie`-Element vornehmen.

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Erstellen Sie die Projektmappe.

Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.

  ![PMC-Menü](adding-model/_static/pmc.png)

Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration Rating
Update-Database
```

Der Befehl `Add-Migration` weist das Migrationsframework an, das aktuelle `Movie`-Modell mit dem aktuellen `Movie`-Datenbankschema zu untersuchen und den notwendigen Code zu erstellen, um die Datenbank in das neue Modell zu migrieren. Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen. Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.

Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu. Dies ist über die Links „Löschen“ im Browser oder SSOX möglich.

Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können. Sie müssen das Feld `Rating` auch den Ansichtsvorlagen `Edit`, `Details` und `Delete` hinzufügen.

>[!div class="step-by-step"]
[Zurück](search.md)
[Weiter](validation.md)  
