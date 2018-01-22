---
title: ASP.NET Core MVC mit EF-Kern - Sortierung, Filter, Paging - 3 von 10
author: tdykstra
description: "In diesem Lernprogramm fügen Sie sortieren, Filtern und paging Funktionen zur Seite mit ASP.NET Core und Entity Framework Core."
ms.author: tdykstra
ms.date: 03/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 6da2073b18f6fff9738808c84441e59240caefe3
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-aspnet-core-mvc-tutorial-3-of-10"></a><span data-ttu-id="9d8de-103">Sortieren, filtern, paging und Gruppierung - EF-Core mit ASP.NET Core MVC-Lernprogramm (3 von 10)</span><span class="sxs-lookup"><span data-stu-id="9d8de-103">Sorting, filtering, paging, and grouping - EF Core with ASP.NET Core MVC tutorial (3 of 10)</span></span>

<span data-ttu-id="9d8de-104">Durch [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9d8de-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9d8de-105">Die Contoso-University Beispielwebanwendung veranschaulicht, wie ASP.NET Core MVC-Webanwendungen, die mit Entity Framework Core und Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9d8de-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="9d8de-106">Informationen über die Reihe von Lernprogrammen finden Sie unter [im ersten Lernprogramm, in der Reihe](intro.md).</span><span class="sxs-lookup"><span data-stu-id="9d8de-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="9d8de-107">Im vorherigen Lernprogramm implementiert Sie eine Reihe von Webseiten für grundlegende CRUD-Vorgänge für Student-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="9d8de-107">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="9d8de-108">In diesem Lernprogramm fügen Sie sortieren, Filtern und Pagingfunktionalität zur Seite Studenten Index.</span><span class="sxs-lookup"><span data-stu-id="9d8de-108">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="9d8de-109">Erstellen Sie auch eine Seite, die einfache Gruppierung ausführt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-109">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="9d8de-110">Die folgende Abbildung zeigt, wie die Seite aussehen wird, wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="9d8de-110">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="9d8de-111">Die Spaltenüberschriften sind Links, die der Benutzer klicken kann, um nach dieser Spalte zu sortieren.</span><span class="sxs-lookup"><span data-stu-id="9d8de-111">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="9d8de-112">Auf eine Spaltenüberschrift wiederholt Schaltet zwischen aufsteigender und absteigender Sortierreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="9d8de-112">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Indexseite für Studenten](sort-filter-page/_static/paging.png)

## <a name="add-column-sort-links-to-the-students-index-page"></a><span data-ttu-id="9d8de-114">Den Studenten Indexseite Spalte sortieren Links hinzufügen</span><span class="sxs-lookup"><span data-stu-id="9d8de-114">Add Column Sort Links to the Students Index Page</span></span>

<span data-ttu-id="9d8de-115">Um die Sortierung für die Student Indexseite hinzufügen, ändern Sie die `Index` -Methode des Controllers Studenten und fügen Sie Code hinzu, um die Student Indexansicht.</span><span class="sxs-lookup"><span data-stu-id="9d8de-115">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="9d8de-116">Fügen Sie die Sortierfunktionen für die Index-Methode</span><span class="sxs-lookup"><span data-stu-id="9d8de-116">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="9d8de-117">In *StudentsController.cs*, ersetzen Sie die `Index` -Methode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="9d8de-117">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="9d8de-118">Dieser Code empfängt eine `sortOrder` Parameter aus der Abfragezeichenfolge in der URL.</span><span class="sxs-lookup"><span data-stu-id="9d8de-118">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="9d8de-119">Der Wert der Abfragezeichenfolge wird als Parameter an die Aktionsmethode durch ASP.NET Core MVC bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-119">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="9d8de-120">Der Parameter wird eine Zeichenfolge sein, der entweder "Name" oder "Date", optional gefolgt von einem Unterstrich und die Zeichenfolge "Desc" in absteigender Reihenfolge anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9d8de-120">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="9d8de-121">Standardmäßig wird eine aufsteigende Sortierreihenfolge verwendet.</span><span class="sxs-lookup"><span data-stu-id="9d8de-121">The default sort order is ascending.</span></span>

<span data-ttu-id="9d8de-122">Die Indexseite angefordert wird, das zum ersten Mal ist keine Abfragezeichenfolge vorhanden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-122">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="9d8de-123">Den Studenten werden in aufsteigender Reihenfolge angezeigt, nach dem Nachnamen, die standardmäßig aktiviert ist, wie in der Fall fallen über die `switch` Anweisung.</span><span class="sxs-lookup"><span data-stu-id="9d8de-123">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="9d8de-124">Wenn der Benutzer einen Spaltenüberschrift Hyperlink der entsprechenden klickt `sortOrder` Wert in der Abfragezeichenfolge angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="9d8de-124">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="9d8de-125">Die beiden `ViewData` Elemente (NameSortParm und DateSortParm) werden von der Sicht verwendet, so konfigurieren Sie die Spaltenüberschrift Hyperlinks mit den entsprechenden Abfragezeichenfolgen-Werte.</span><span class="sxs-lookup"><span data-stu-id="9d8de-125">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="9d8de-126">Hierbei handelt es sich um ternäre-Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-126">These are ternary statements.</span></span> <span data-ttu-id="9d8de-127">Das erste Schema gibt an, dass bei der `sortOrder` -Parameter ist null oder leer ist, NameSortParm sollte auf "Name_desc" festgelegt werden; andernfalls eine leere Zeichenfolge festgelegt werden sollte.</span><span class="sxs-lookup"><span data-stu-id="9d8de-127">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="9d8de-128">Diese beiden Anweisungen aktivieren die Sicht die Spalte Überschrift Hyperlinks wie folgt festgelegt:</span><span class="sxs-lookup"><span data-stu-id="9d8de-128">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="9d8de-129">Aktuelle Sortierreihenfolge</span><span class="sxs-lookup"><span data-stu-id="9d8de-129">Current sort order</span></span>  | <span data-ttu-id="9d8de-130">Letzte Name Hyperlink</span><span class="sxs-lookup"><span data-stu-id="9d8de-130">Last Name Hyperlink</span></span> | <span data-ttu-id="9d8de-131">Datum-Link</span><span class="sxs-lookup"><span data-stu-id="9d8de-131">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="9d8de-132">Letzte aufsteigend</span><span class="sxs-lookup"><span data-stu-id="9d8de-132">Last Name ascending</span></span>  | <span data-ttu-id="9d8de-133">descending</span><span class="sxs-lookup"><span data-stu-id="9d8de-133">descending</span></span>          | <span data-ttu-id="9d8de-134">ascending</span><span class="sxs-lookup"><span data-stu-id="9d8de-134">ascending</span></span>      |
| <span data-ttu-id="9d8de-135">Letzte Name absteigend</span><span class="sxs-lookup"><span data-stu-id="9d8de-135">Last Name descending</span></span> | <span data-ttu-id="9d8de-136">ascending</span><span class="sxs-lookup"><span data-stu-id="9d8de-136">ascending</span></span>           | <span data-ttu-id="9d8de-137">ascending</span><span class="sxs-lookup"><span data-stu-id="9d8de-137">ascending</span></span>      |
| <span data-ttu-id="9d8de-138">Datum aufsteigend</span><span class="sxs-lookup"><span data-stu-id="9d8de-138">Date ascending</span></span>       | <span data-ttu-id="9d8de-139">ascending</span><span class="sxs-lookup"><span data-stu-id="9d8de-139">ascending</span></span>           | <span data-ttu-id="9d8de-140">descending</span><span class="sxs-lookup"><span data-stu-id="9d8de-140">descending</span></span>     |
| <span data-ttu-id="9d8de-141">Absteigend nach Datum</span><span class="sxs-lookup"><span data-stu-id="9d8de-141">Date descending</span></span>      | <span data-ttu-id="9d8de-142">ascending</span><span class="sxs-lookup"><span data-stu-id="9d8de-142">ascending</span></span>           | <span data-ttu-id="9d8de-143">ascending</span><span class="sxs-lookup"><span data-stu-id="9d8de-143">ascending</span></span>      |

<span data-ttu-id="9d8de-144">Die Methode verwendet LINQ to Entities an die Spalte zu sortieren.</span><span class="sxs-lookup"><span data-stu-id="9d8de-144">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="9d8de-145">Der Code erstellt ein `IQueryable` Variable vor der Switch-Anweisung ändert sie in der Switch-Anweisung und die Aufrufe der `ToListAsync` Methode nach der `switch` Anweisung.</span><span class="sxs-lookup"><span data-stu-id="9d8de-145">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="9d8de-146">Beim Erstellen und ändern Sie `IQueryable` Variablen, wird keine Abfrage an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="9d8de-146">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="9d8de-147">Die Abfrage wird nicht ausgeführt, bis Sie konvertieren die `IQueryable` Objekt in eine Auflistung durch Aufrufen einer Methode wie z. B. `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="9d8de-147">The query is not executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="9d8de-148">Aus diesem Grund dieser Code führt zu einer einzelnen Abfrage, die nicht, bis ausgeführt wird die `return View` Anweisung.</span><span class="sxs-lookup"><span data-stu-id="9d8de-148">Therefore, this code results in a single query that is not executed until the `return View` statement.</span></span>

<span data-ttu-id="9d8de-149">Dieser Code konnte ausführliche mit einer großen Anzahl von Spalten abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-149">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="9d8de-150">[Im letzten Lernprogramm dieser Reihe](advanced.md#dynamic-linq) wird gezeigt, wie Sie Code schreiben, mit dem Sie den Namen eines übergeben der `OrderBy` Spalte in einer Zeichenfolgenvariablen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-150">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="9d8de-151">Die Indexansicht Student Spaltenüberschrift Links hinzufügen</span><span class="sxs-lookup"><span data-stu-id="9d8de-151">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="9d8de-152">Ersetzen Sie den Code in *Views/Students/Index.cshtml*, durch den folgenden Code Spaltenüberschrift Hyperlinks hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-152">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="9d8de-153">Die geänderten Zeilen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="9d8de-153">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="9d8de-154">Dieser Code verwendet die Informationen in `ViewData` Eigenschaften zum Einrichten von Hyperlinks mit der entsprechenden Abfrage Zeichenfolgenwerte.</span><span class="sxs-lookup"><span data-stu-id="9d8de-154">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="9d8de-155">Die app auszuführen, wählen Sie die **Studenten** Registerkarte, und klicken Sie auf die **Nachname** und **Registrierungsdatum** funktioniert Spaltenüberschriften, um diese Sortierung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-155">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Studenten Indexseite im Namensreihenfolge](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box-to-the-students-index-page"></a><span data-ttu-id="9d8de-157">Den Studenten Indexseite ein Suchfeld hinzufügen</span><span class="sxs-lookup"><span data-stu-id="9d8de-157">Add a Search Box to the Students Index page</span></span>

<span data-ttu-id="9d8de-158">Zum Hinzufügen der Studenten Indexseite filtern, Sie fügen einem Textfeld und einer Schaltfläche "Absenden" zur Ansicht und entsprechende Änderungen an der `Index` Methode.</span><span class="sxs-lookup"><span data-stu-id="9d8de-158">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="9d8de-159">Das Textfeld können Sie geben eine Zeichenfolge, die in den Vornamen und den letzten Namensfelder gesucht werden soll.</span><span class="sxs-lookup"><span data-stu-id="9d8de-159">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="9d8de-160">Hinzufügen von Filterfunktionen zur Verfügung, die Index-Methode</span><span class="sxs-lookup"><span data-stu-id="9d8de-160">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="9d8de-161">In *StudentsController.cs*, ersetzen Sie die `Index` -Methode durch folgenden Code (die Änderungen werden hervorgehoben).</span><span class="sxs-lookup"><span data-stu-id="9d8de-161">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="9d8de-162">Sie hinzugefügt haben eine `searchString` Parameter an die `Index` Methode.</span><span class="sxs-lookup"><span data-stu-id="9d8de-162">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="9d8de-163">Der Zeichenfolgenwert für die Suche wird aus einem Textfeld empfangen, die Sie die Indexansicht hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-163">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="9d8de-164">Sie haben ebenfalls hinzugefügt, die LINQ-Anweisung eine Where-Klausel, die nur Studenten auswählt, deren vor- oder Nachnamen die zu suchende Zeichenfolge enthält.</span><span class="sxs-lookup"><span data-stu-id="9d8de-164">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="9d8de-165">Die Anweisung, die fügt die Where-Klausel ist nur dann, wenn es ein Wert für die Suche wird ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-165">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="9d8de-166">Sie werden hier Aufrufen der `Where` Methode auf eine `IQueryable` Objekt und der Filter wird auf dem Server verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-166">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="9d8de-167">In einigen Szenarien Sie aufgerufen werden möglicherweise die `Where` -Methode eine Erweiterungsmethode für eine in-Memory-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="9d8de-167">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="9d8de-168">(Z. B. Angenommen, Sie ändern, dass den Verweis auf `_context.Students` also, sondern von einer EF `DbSet` er verweist auf eine Repository-Methode, die zurückgibt eine `IEnumerable` Auflistung.) Das Ergebnis wäre normalerweise identisch, aber in einigen Fällen unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-168">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="9d8de-169">Angenommen, die .NET Framework-Implementierung von der `Contains` -Methode führt einen Vergleich Groß-/Kleinschreibung standardmäßig, jedoch in SQL Server wird dies durch die sortierungseinstellung der SQL Server-Instanz bestimmt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-169">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="9d8de-170">Diese Einstellung wird standardmäßig auf Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="9d8de-170">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="9d8de-171">Rufen Sie die `ToUpper` Methode zum Erstellen von Tests explizit Groß-/Kleinschreibung: *, in denen (s = > s.LastName.ToUpper(). Contains(searchString.ToUpper())*.</span><span class="sxs-lookup"><span data-stu-id="9d8de-171">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="9d8de-172">Würde sicherzustellen, dass Ergebnisse gleich bleiben, wenn Sie ändern den Code weiter unten, um ein Repository verwenden die zurückgibt, eine `IEnumerable` Auflistung anstelle von einer `IQueryable` Objekt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-172">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="9d8de-173">(Beim Aufrufen der `Contains` Methode auf eine `IEnumerable` -Auflistung, erhalten Sie die .NET Framework-Implementierung; Wenn Sie es auf Aufrufen einer `IQueryable` -Objekt erhalten Sie die Implementierung des Anbieters.) Es ist jedoch eine Leistungseinbuße für diese Lösung.</span><span class="sxs-lookup"><span data-stu-id="9d8de-173">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there is a performance penalty for this solution.</span></span> <span data-ttu-id="9d8de-174">Die `ToUpper` Code eine Funktion in der WHERE-Klausel der t-SQL SELECT-Anweisung setzen würden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-174">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="9d8de-175">Die würde verhindern, dass den Optimierer einen Index verwenden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-175">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="9d8de-176">Davon ausgehend, dass SQL hauptsächlich als Groß-/Kleinschreibung installiert ist, es wird empfohlen, vermeiden Sie die `ToUpper` code, bis die Migration zu einem Datenspeicher für die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="9d8de-176">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="9d8de-177">Fügen Sie ein Suchfeld auf die Indexansicht Student</span><span class="sxs-lookup"><span data-stu-id="9d8de-177">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="9d8de-178">In *Views/Student/Index.cshtml*, fügen Sie der hervorgehobene Code hinzu, unmittelbar bevor das öffnende Tag um erstellen eine Beschriftung, ein Textfeld, Tabelle und eine **Suche** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="9d8de-178">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="9d8de-179">Dieser Code verwendet die `<form>` [tag Helper](xref:mvc/views/tag-helpers/intro) Suchtextfeld und Schaltfläche hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-179">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="9d8de-180">Wird standardmäßig der `<form>` Tag Hilfsprogramm sendet Formulardaten mit einer POST, was bedeutet, dass der Parameter als Abfragezeichenfolgen in den Hauptteil der HTTP-Nachricht und nicht in der URL übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-180">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="9d8de-181">Bei der Angabe von HTTP GET die Formulardaten übergeben die URL als Abfragezeichenfolgen, dadurch können sich Benutzer auf die URL von Lesezeichen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-181">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="9d8de-182">Das W3C Richtlinien wird empfohlen, das zu verwendende erhalten, wenn die Aktion nicht in einem Update führt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-182">The W3C guidelines recommend that you should use GET when the action does not result in an update.</span></span>

<span data-ttu-id="9d8de-183">Die app auszuführen, wählen Sie die **Studenten** Registerkarte, geben Sie eine Suchzeichenfolge ein, und klicken Sie auf Suchen, um sicherzustellen, dass die Filterung arbeitet.</span><span class="sxs-lookup"><span data-stu-id="9d8de-183">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Studenten Indexseite mit Filtern](sort-filter-page/_static/filtering.png)

<span data-ttu-id="9d8de-185">Beachten Sie, dass die URL der zu suchende Zeichenfolge enthält.</span><span class="sxs-lookup"><span data-stu-id="9d8de-185">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="9d8de-186">Wenn Sie diese Seite Lesezeichen, erhalten Sie die gefilterte Liste, bei der Verwendung von Lesezeichen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-186">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="9d8de-187">Hinzufügen von `method="get"` auf die `form` Tag ist die Ursache der Abfragezeichenfolge an, die generiert werden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-187">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="9d8de-188">In dieser Phase, wenn Sie einen Spaltenüberschrift sortieren Link klicken Sie verlieren die Filterwert, der im eingegebenen der **Suche** Feld.</span><span class="sxs-lookup"><span data-stu-id="9d8de-188">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="9d8de-189">Sie beheben, die im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-189">You'll fix that in the next section.</span></span>

## <a name="add-paging-functionality-to-the-students-index-page"></a><span data-ttu-id="9d8de-190">Den Studenten Indexseite Pagingfunktionen hinzufügen</span><span class="sxs-lookup"><span data-stu-id="9d8de-190">Add paging functionality to the Students Index page</span></span>

<span data-ttu-id="9d8de-191">Um die Studenten Indexseite Paging hinzugefügt haben, erstellen Sie eine `PaginatedList` -Klasse, verwendet `Skip` und `Take` Anweisungen zum Filtern von Daten auf dem Server statt immer alle Zeilen der Tabelle abzurufen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-191">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="9d8de-192">Dann Sie zusätzliche Änderungen in machen der `Index` Methode und Pagingschaltflächen zum Hinzufügen der `Index` anzeigen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-192">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="9d8de-193">Die folgende Abbildung zeigt die Pagingschaltflächen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-193">The following illustration shows the paging buttons.</span></span>

![Studenten Indexseite mit Paginierungslinks](sort-filter-page/_static/paging.png)

<span data-ttu-id="9d8de-195">Erstellen Sie in den Projektordner `PaginatedList.cs`, und Ersetzen Sie den Code durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="9d8de-195">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="9d8de-196">Die `CreateAsync` Methode in diesem Code akzeptiert Seitengröße und die Seitenzahl und wendet die entsprechenden `Skip` und `Take` Anweisungen, die die `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="9d8de-196">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="9d8de-197">Wenn `ToListAsync` aufgerufen wird, auf die `IQueryable`, wird eine Liste mit nur die angeforderte Seite zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9d8de-197">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="9d8de-198">Die Eigenschaften `HasPreviousPage` und `HasNextPage` dienen zum Aktivieren oder deaktivieren Sie **vorherige** und **Weiter** paging Schaltflächen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-198">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="9d8de-199">Ein `CreateAsync` Methode wird verwendet, statt einen Konstruktor zum Erstellen der `PaginatedList<T>` Objekt, da der Konstruktoren asynchronen Code ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="9d8de-199">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-functionality-to-the-index-method"></a><span data-ttu-id="9d8de-200">Fügen Sie Pagingfunktionen zur Index-Methode</span><span class="sxs-lookup"><span data-stu-id="9d8de-200">Add paging functionality to the Index method</span></span>

<span data-ttu-id="9d8de-201">In *StudentsController.cs*, ersetzen Sie die `Index` -Methode durch folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="9d8de-201">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="9d8de-202">Dieser Code Fügt eine Seite-Number-Parameter, eine aktuelle sortieren Reihenfolge Parameter- und eine aktuelle Filter-Parameter auf die Methodensignatur an.</span><span class="sxs-lookup"><span data-stu-id="9d8de-202">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? page)
```

<span data-ttu-id="9d8de-203">Ersten Mal die Seite wird angezeigt, oder wenn der Benutzer eine Auslagerung oder Sortierung von Link geklickt hat noch nicht, werden alle Parameter null sein.</span><span class="sxs-lookup"><span data-stu-id="9d8de-203">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="9d8de-204">Wenn ein Auslagerung Link geklickt wird, enthält die Seitenvariable die Seitenzahl angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-204">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="9d8de-205">Die `ViewData` Element mit dem Namen CurrentSort erhält die Ansicht mit der aktuellen Sortierung aus, da dies in die Auslagerungsdatei Links enthalten sein muss, um der Sortierreihenfolge beim Paging identisch zu halten.</span><span class="sxs-lookup"><span data-stu-id="9d8de-205">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="9d8de-206">Die `ViewData` Element mit dem Namen "aktuelle Filter" enthält die Ansicht mit der aktuellen Filterzeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="9d8de-206">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="9d8de-207">Dieser Wert muss in die Auslagerungsdatei Links enthalten sein, um die filtereinstellungen während der Auslagerung zu gewährleisten, und es muss in das Textfeld wiederhergestellt werden, wenn die Seite erneut angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="9d8de-207">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="9d8de-208">Wenn die Suchzeichenfolge während Paging geändert wird, verfügt über die Seite auf 1 zurückgesetzt werden sollen, der neue Filter kann in verschiedenen anzuzeigenden ergeben.</span><span class="sxs-lookup"><span data-stu-id="9d8de-208">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="9d8de-209">Die Suchzeichenfolge wird geändert, wenn ein Wert in das Textfeld eingegeben wird und die Schaltfläche "Absenden" gedrückt wird.</span><span class="sxs-lookup"><span data-stu-id="9d8de-209">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="9d8de-210">In diesem Fall die `searchString` Parameter nicht null ist.</span><span class="sxs-lookup"><span data-stu-id="9d8de-210">In that case, the `searchString` parameter is not null.</span></span>

```csharp
if (searchString != null)
{
    page = 1;
}
else
{
    searchString = currentFilter;
}
```

<span data-ttu-id="9d8de-211">Am Ende der `Index` -Methode, die `PaginatedList.CreateAsync` Methode konvertiert die Student-Abfrage in einer einzelnen Seite der Schüler in einen Auflistungstyp, die Paging unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-211">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="9d8de-212">Diese einzelnen Studenten-Seite wird dann an die Ansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="9d8de-212">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), page ?? 1, pageSize));
```

<span data-ttu-id="9d8de-213">Die `PaginatedList.CreateAsync` Methode nimmt eine Seitenzahl an.</span><span class="sxs-lookup"><span data-stu-id="9d8de-213">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="9d8de-214">Die zwei Fragezeichen darstellen, die Null-Sammeloperator.</span><span class="sxs-lookup"><span data-stu-id="9d8de-214">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="9d8de-215">Der Null-Sammeloperator definiert einen Standardwert für einen NULL-Werte zulässt. der Ausdruck `(page ?? 1)` bedeutet, dass der Rückgabewert von `page` , wenn es einen Wert, oder 1 zurück, wenn `page` ist null.</span><span class="sxs-lookup"><span data-stu-id="9d8de-215">The null-coalescing operator defines a default value for a nullable type; the expression `(page ?? 1)` means return the value of `page` if it has a value, or return 1 if `page` is null.</span></span>

## <a name="add-paging-links-to-the-student-index-view"></a><span data-ttu-id="9d8de-216">Können die Indexansicht Student Paginierungslinks hinzufügen</span><span class="sxs-lookup"><span data-stu-id="9d8de-216">Add paging links to the Student Index view</span></span>

<span data-ttu-id="9d8de-217">In *Views/Students/Index.cshtml*, ersetzen Sie den vorhandenen Code durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="9d8de-217">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="9d8de-218">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="9d8de-218">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="9d8de-219">Die `@model` Anweisung am oberen Rand der Seite "gibt an, dass die Sicht nun Ruft eine `PaginatedList<T>` -Objekt anstelle einer `List<T>` Objekt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-219">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="9d8de-220">Die Spalte Header Links verwenden die Abfragezeichenfolge an die aktuelle Suchzeichenfolge an den Controller übergeben, damit der Benutzer in Filterergebnisse sortieren kann:</span><span class="sxs-lookup"><span data-stu-id="9d8de-220">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="9d8de-221">Tag-Hilfsprogramme sind die Auslagerung Schaltflächen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="9d8de-221">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-page="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="9d8de-222">Führen Sie die app, und wechseln Sie zu der Seite "Students".</span><span class="sxs-lookup"><span data-stu-id="9d8de-222">Run the app and go to the Students page.</span></span>

![Studenten Indexseite mit Paginierungslinks](sort-filter-page/_static/paging.png)

<span data-ttu-id="9d8de-224">Klicken Sie auf die Auslagerung Links in verschiedenen Sortierreihenfolgen, stellen Sie sicher, dass Paging funktioniert.</span><span class="sxs-lookup"><span data-stu-id="9d8de-224">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="9d8de-225">Klicken Sie dann geben Sie eine Suchzeichenfolge ein, und versuchen Sie es erneut aus, um sicherzustellen, dass Paging auch ordnungsgemäß mit Sortier- und Filtervorgänge Paging.</span><span class="sxs-lookup"><span data-stu-id="9d8de-225">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page-that-shows-student-statistics"></a><span data-ttu-id="9d8de-226">Erstellen Sie eine Informationsseite, die Student Statistiken</span><span class="sxs-lookup"><span data-stu-id="9d8de-226">Create an About page that shows Student statistics</span></span>

<span data-ttu-id="9d8de-227">Für der Contoso-University Website **zu** Seite müssen Sie anzeigen, wie viele Studenten für jedes Registrierungsdatum registriert haben.</span><span class="sxs-lookup"><span data-stu-id="9d8de-227">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="9d8de-228">Dazu gruppieren und einfache Berechnungen für Gruppen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-228">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="9d8de-229">Um dies zu erreichen, müssen Sie Folgendes ausführen:</span><span class="sxs-lookup"><span data-stu-id="9d8de-229">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="9d8de-230">Erstellen Sie eine Modellklasse Ansicht für die Daten, die an die Ansicht ьbergeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-230">Create a view model class for the data that you need to pass to the view.</span></span>

* <span data-ttu-id="9d8de-231">Ändern Sie die Info-Methode im Home-Controller.</span><span class="sxs-lookup"><span data-stu-id="9d8de-231">Modify the About method in the Home controller.</span></span>

* <span data-ttu-id="9d8de-232">Ändern Sie die Info-Sicht.</span><span class="sxs-lookup"><span data-stu-id="9d8de-232">Modify the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="9d8de-233">Erstellen des Modells anzeigen</span><span class="sxs-lookup"><span data-stu-id="9d8de-233">Create the view model</span></span>

<span data-ttu-id="9d8de-234">Erstellen einer *SchoolViewModels* Ordner in der *Modelle* Ordner.</span><span class="sxs-lookup"><span data-stu-id="9d8de-234">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="9d8de-235">Fügen Sie eine Klassendatei in den neuen Ordner *EnrollmentDateGroup.cs* , und Ersetzen Sie den Code durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="9d8de-235">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="9d8de-236">Ändern Sie den Home-Controller</span><span class="sxs-lookup"><span data-stu-id="9d8de-236">Modify the Home Controller</span></span>

<span data-ttu-id="9d8de-237">In *HomeController.cs*, fügen Sie die folgenden using-Anweisungen am Anfang der Datei:</span><span class="sxs-lookup"><span data-stu-id="9d8de-237">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="9d8de-238">Fügen Sie eine Klassenvariable für den Datenbankkontext unmittelbar nach der öffnenden geschweiften Klammer für die Klasse, und rufen Sie eine Instanz des Kontexts von ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="9d8de-238">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="9d8de-239">Ersetzen Sie die `About`-Methode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="9d8de-239">Replace the `About` method with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="9d8de-240">Registrierungsdatum Student-Entität gruppiert, berechnet die Anzahl der Entitäten in jeder Gruppe und speichert die Ergebnisse in einer Auflistung von LINQ-Anweisung `EnrollmentDateGroup` Model-Objekte anzeigen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-240">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>
> [!NOTE] 
> <span data-ttu-id="9d8de-241">Klicken Sie in der Version 1.0 von Entity Framework Core das gesamte Resultset an den Client zurückgegeben wird, und Gruppierung erfolgt auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="9d8de-241">In the 1.0 version of Entity Framework Core, the entire result set is returned to the client, and grouping is done on the client.</span></span> <span data-ttu-id="9d8de-242">In einigen Szenarien konnte das Leistungsproblemen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="9d8de-242">In some scenarios this could create performance problems.</span></span> <span data-ttu-id="9d8de-243">Achten Sie darauf, dass zum Testen der Leistung mit Produktion Datenmengen und bei Bedarf unformatierten SQL verwenden, um die Gruppierung auf dem Server ausführen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-243">Be sure to test performance with production volumes of data, and if necessary use raw SQL to do the grouping on the server.</span></span> <span data-ttu-id="9d8de-244">Informationen zur Verwendung von unformatierten SQL finden Sie unter [im letzten Lernprogramm dieser Reihe](advanced.md).</span><span class="sxs-lookup"><span data-stu-id="9d8de-244">For information about how to use raw SQL, see [the last tutorial in this series](advanced.md).</span></span>

### <a name="modify-the-about-view"></a><span data-ttu-id="9d8de-245">Ändern Sie die Informationen zur Ansicht</span><span class="sxs-lookup"><span data-stu-id="9d8de-245">Modify the About View</span></span>

<span data-ttu-id="9d8de-246">Ersetzen Sie den Code in der *Views/Home/About.cshtml* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="9d8de-246">Replace the code in the *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="9d8de-247">Führen Sie die app, und wechseln Sie zu der Seite "Info".</span><span class="sxs-lookup"><span data-stu-id="9d8de-247">Run the app and go to the About page.</span></span> <span data-ttu-id="9d8de-248">Die Anzahl der Schüler für jedes Registrierungsdatum wird in einer Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9d8de-248">The count of students for each enrollment date is displayed in a table.</span></span>

![Zu den Seiten](sort-filter-page/_static/about.png)

## <a name="summary"></a><span data-ttu-id="9d8de-250">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="9d8de-250">Summary</span></span>

<span data-ttu-id="9d8de-251">In diesem Lernprogramm haben Sie gesehen, wie sortieren, filtern, Paging und gruppieren ausführen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-251">In this tutorial, you've seen how to perform sorting, filtering, paging, and grouping.</span></span> <span data-ttu-id="9d8de-252">In den nächsten Lernprogrammen erfahren Sie, wie datenmodelländerungen zu behandeln, indem Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="9d8de-252">In the next tutorial, you'll learn how to handle data model changes by using migrations.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="9d8de-253">[Zurück](crud.md)
[Weiter](migrations.md)</span><span class="sxs-lookup"><span data-stu-id="9d8de-253">[Previous](crud.md)
[Next](migrations.md)</span></span>  