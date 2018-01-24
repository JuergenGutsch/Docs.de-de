---
title: "Hinzufügen eines neuen Felds"
author: rick-anderson
description: 
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 046e16b1a9581edb2be9a2315cf7f2677684747b
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="adding-a-new-field"></a><span data-ttu-id="f67bc-102">Hinzufügen eines neuen Felds</span><span class="sxs-lookup"><span data-stu-id="f67bc-102">Adding a New Field</span></span>

<span data-ttu-id="f67bc-103">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f67bc-103">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f67bc-104">In diesem Abschnitt verwenden Sie [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen zum Hinzufügen eines neuen Felds zum Modell und zum Migrieren dieser Änderung in die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f67bc-104">In this section you'll use [Entity Framework](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) Code First Migrations to add a new field to the model and migrate that change to the database.</span></span>

<span data-ttu-id="f67bc-105">Wenn Sie EF Code First verwenden, um eine Datenbank automatisch zu erstellen, fügt Code First der Datenbank eine Tabelle hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="f67bc-105">When you use EF Code First to automatically create a database, Code First adds a table to the database to help track whether the schema of the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="f67bc-106">Wenn sie nicht synchron sind, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="f67bc-106">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="f67bc-107">Dies erleichtert die Suche nach Problemen mit inkonsistenten Datenbanken bzw. inkonsistentem Code.</span><span class="sxs-lookup"><span data-stu-id="f67bc-107">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="f67bc-108">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="f67bc-108">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="f67bc-109">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="f67bc-109">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

<span data-ttu-id="f67bc-110">Erstellen Sie die App (STRG+UMSCHALT+B).</span><span class="sxs-lookup"><span data-stu-id="f67bc-110">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="f67bc-111">Da Sie der `Movie`-Klasse ein neues Feld hinzugefügt haben, müssen Sie auch die Positivliste für die Bindung aktualisieren, damit diese neue Eigenschaft eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="f67bc-111">Because you've added a new field to the `Movie` class, you also need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="f67bc-112">Aktualisieren Sie in *MoviesController.cs* das `[Bind]`-Attribut für die Aktionsmethoden `Create` und `Edit` so, dass die `Rating`-Eigenschaft eingeschlossen wird:</span><span class="sxs-lookup"><span data-stu-id="f67bc-112">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="f67bc-113">Sie müssen auch die Ansichtsvorlagen aktualisieren, um die neue `Rating`-Eigenschaft in der Browseransicht anzuzeigen, zu erstellen und zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="f67bc-113">You also need to update the view templates in order to display, create and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="f67bc-114">Bearbeiten Sie die Datei */Views/Movies/Index.cshtml*, und fügen Sie das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="f67bc-114">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[Main](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

<span data-ttu-id="f67bc-115">Aktualisieren Sie die Datei */Views/Movies/Create.cshtml* mit dem Feld `Rating`.</span><span class="sxs-lookup"><span data-stu-id="f67bc-115">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span> <span data-ttu-id="f67bc-116">Sie können die vorherige „Formulargruppe“ kopieren und einfügen und IntelliSense die Felder aktualisieren lassen.</span><span class="sxs-lookup"><span data-stu-id="f67bc-116">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="f67bc-117">IntelliSense nutzt [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f67bc-117">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="f67bc-118">Hinweis: In der RTM-Version von Visual Studio 2017 müssen Sie die [Razor-Sprachdienste](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices) für Razor IntelliSense installieren.</span><span class="sxs-lookup"><span data-stu-id="f67bc-118">Note: In the RTM verison of Visual Studio 2017 you need to install the [Razor Language Services](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices) for Razor intelliSense.</span></span> <span data-ttu-id="f67bc-119">Dies wird in der nächsten Version behoben.</span><span class="sxs-lookup"><span data-stu-id="f67bc-119">This will be fixed in the next release.</span></span>

![Der Entwickler hat den Buchstaben R als Attributwert von „asp-for“ im zweiten „label“-Element der Ansicht eingegeben.](new-field/_static/cr.png)

<span data-ttu-id="f67bc-123">Die App funktioniert erst, nachdem wir die Datenbank mit dem neuen Feld aktualisiert haben.</span><span class="sxs-lookup"><span data-stu-id="f67bc-123">The app won't work until we update the DB to include the new field.</span></span> <span data-ttu-id="f67bc-124">Wenn Sie sie jetzt ausführen, erhalten Sie die folgende `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="f67bc-124">If you run it now, you'll get the following `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="f67bc-125">Dieser Fehler wird gemeldet, da die aktualisierte Modellklasse „Movie“ sich vom Schema der Tabelle „Movie“ der vorhandenen Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="f67bc-125">You're seeing this error because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="f67bc-126">(Die Datenbanktabelle enthält nicht die Spalte „Rating“.)</span><span class="sxs-lookup"><span data-stu-id="f67bc-126">(There's no Rating column in the database table.)</span></span>

<span data-ttu-id="f67bc-127">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="f67bc-127">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="f67bc-128">Lassen Sie die Datenbank von Entity Framework automatisch löschen und basierend auf dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f67bc-128">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="f67bc-129">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch, wenn die aktive Entwicklung in einer Testdatenbank erfolgt. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="f67bc-129">This approach is very convenient early in the development cycle when you are doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="f67bc-130">Der Nachteil ist jedoch, dass in der Datenbank vorhandene Daten verloren gehen. Deshalb eignet sich dieser Ansatz nicht für eine Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="f67bc-130">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="f67bc-131">Das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="f67bc-131">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span>

2. <span data-ttu-id="f67bc-132">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="f67bc-132">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="f67bc-133">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="f67bc-133">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="f67bc-134">Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="f67bc-134">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="f67bc-135">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f67bc-135">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="f67bc-136">Für dieses Tutorial verwenden wir Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="f67bc-136">For this tutorial, we'll use Code First Migrations.</span></span>

<span data-ttu-id="f67bc-137">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f67bc-137">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="f67bc-138">Eine Beispieländerung wird nachstehend gezeigt, aber Sie sollten diese Änderung für jedes `new Movie`-Element vornehmen.</span><span class="sxs-lookup"><span data-stu-id="f67bc-138">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f67bc-139">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="f67bc-139">Build the solution.</span></span>

<span data-ttu-id="f67bc-140">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="f67bc-140">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC-Menü](adding-model/_static/pmc.png)

<span data-ttu-id="f67bc-142">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="f67bc-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="f67bc-143">Der Befehl `Add-Migration` weist das Migrationsframework an, das aktuelle `Movie`-Modell mit dem aktuellen `Movie`-Datenbankschema zu untersuchen und den notwendigen Code zu erstellen, um die Datenbank in das neue Modell zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="f67bc-143">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span> <span data-ttu-id="f67bc-144">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="f67bc-144">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="f67bc-145">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="f67bc-145">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="f67bc-146">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="f67bc-146">If you delete all the records in the DB, the initialize will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="f67bc-147">Dies ist über die Links „Löschen“ im Browser oder SSOX möglich.</span><span class="sxs-lookup"><span data-stu-id="f67bc-147">You can do this with the delete links in the browser or from SSOX.</span></span>

<span data-ttu-id="f67bc-148">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="f67bc-148">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="f67bc-149">Sie müssen das Feld `Rating` auch den Ansichtsvorlagen `Edit`, `Details` und `Delete` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f67bc-149">You should also add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="f67bc-150">[Zurück](search.md)
[Weiter](validation.md)</span><span class="sxs-lookup"><span data-stu-id="f67bc-150">[Previous](search.md)
[Next](validation.md)</span></span>  