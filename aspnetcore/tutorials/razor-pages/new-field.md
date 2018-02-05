---
title: "Hinzufügen eines neuen Felds zu einer Razor-Page"
author: rick-anderson
description: "Veranschaulicht das Hinzufügen ein neues Felds zu einer Razor-Page mit Entity Framework Core"
manager: wpickett
ms.author: riande
ms.date: 08/07/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 36412e9d1f3143f0d1999d0e754e6627f0984ad5
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="adding-a-new-field-to-a-razor-page"></a>Hinzufügen eines neuen Felds zu einer Razor-Page

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Abschnitt verwenden Sie [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen zum Hinzufügen eines neuen Felds zum Modell und zum Migrieren dieser Änderung in die Datenbank.

Wenn Sie EF Code First verwenden, um eine Datenbank automatisch zu erstellen, fügt Code First der Datenbank eine Tabelle hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde. Wenn sie nicht synchron sind, löst EF eine Ausnahme aus. Dies erleichtert die Suche nach Problemen mit inkonsistenten Datenbanken bzw. inkonsistentem Code.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Hinzufügen einer Rating-Eigenschaft zum Movie-Modell

Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

Erstellen Sie die App (STRG+UMSCHALT+B).

Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen ein `Rating`-Feld hinzu:

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=40-42,61-63)]

Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.

Aktualisieren Sie die Datei *Create.cshtml* mit einem `Rating`-Feld. Sie können das vorherige `<div>`-Element kopieren und einfügen, und IntelliSense kann Ihnen beim Aktualisieren der Felder helfen. IntelliSense nutzt [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro).

![Der Entwickler hat den Buchstaben R als Attributwert von „asp-for“ im zweiten „label“-Element der Ansicht eingegeben. Ein Intellisense-Kontextmenü mit den verfügbaren Feldern wird angezeigt, einschließlich „Rating“, das in der Liste automatisch hervorgehoben wird. Wenn der Entwickler auf das Feld klickt oder auf der Tastatur die EINGABETASTE drückt, wird der Wert auf „Rating“ festgelegt.](new-field/_static/cr.png)

Der folgende Code zeigt *Create.cshtml* mit einem `Rating`-Feld:

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?highlight=36-40)]

Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.

Die App funktioniert erst, nachdem die Datenbank mit dem neuen Feld aktualisiert wurde. Wenn sie jetzt ausgeführt wird, löst die App eine `SqlException` aus:

```
SqlException: Invalid column name 'Rating'.
```

Dieser Fehler wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet. (Die Datenbanktabelle enthält nicht die Spalte `Rating`.)

Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:

1. Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen. Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln. Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen. Sie sollten diesen Ansatz nicht in einer Produktionsdatenbank verwenden! Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.

2. Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt. Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten. Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.

3. Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.

Verwenden Sie für dieses Tutorial Code First-Migrationen.

Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt. Eine Beispieländerung ist nachstehend gezeigt, aber Sie sollten diese Änderung für jeden `new Movie`-Block vornehmen.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/SeedDataRating.cs).

Erstellen Sie die Projektmappe.

<a name="pmc"></a> Wählen Sie im Menü **Tools** **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.
Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration Rating
Update-Database
```

Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:

* Das `Movie`-Modell mit dem Schema der `Movie`-Datenbank vergleichen.
* Code erstellen, um das Schema der Datenbank in das neue Modell zu migrieren.

Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen. Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.

<a name="ssox"></a> Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu. Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich. So löschen Sie die Datenbank aus SSOX

* Wählen Sie die Datenbank in SSOX aus.
* Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie *Löschen* aus.
* Aktivieren Sie **Vorhandene Verbindungen schließen**.
* Klicken Sie auf **OK**.
* Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):

  ```powershell
  Update-Database
  ```

Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können. Wenn für die Datenbank kein Seed ausgeführt wird, beenden Sie IIS Express, und führen Sie dann die App aus.

>[!div class="step-by-step"]
[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
[Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)
