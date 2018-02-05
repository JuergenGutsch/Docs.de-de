---
title: Razor-Pages mit EF-Core - Sortierung, Filter, Paging - 3 von 8
author: rick-anderson
description: "In diesem Lernprogramm fügen Sie sortieren, Filtern und paging Funktionen zur Seite mit ASP.NET Core und Entity Framework Core."
ms.author: riande
ms.date: 10/22/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 9c1ee6f8c00f3cd501ea86fbf73f51ae540a010a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="sorting-filtering-paging-and-grouping---ef-core-with-razor-pages-3-of-8"></a>Sortieren, filtern, paging und Gruppierung - EF-Core mit Razor-Pages (3 von 8)

Durch [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT), und [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

In diesem Lernprogramm, sortieren, filtern, gruppieren und Paging wird die Funktionalität hinzugefügt.

Die folgende Abbildung zeigt eine ausgefüllte Seite ". Die Spaltenüberschriften sind durch Klicken aktivierbare Links, um die Spalte zu sortieren. Auf eine Spaltenüberschrift wiederholt Schaltet zwischen aufsteigender und absteigender Sortierreihenfolge.

![Indexseite für Studenten](sort-filter-page/_static/paging.png)

Wenn Probleme können nicht zu lösen auftreten, laden Sie die [abgeschlossene Anwendung für diese Stufe](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

## <a name="add-sorting-to-the-index-page"></a>Fügen Sie Sortierung für die Indexseite hinzu

Hinzufügen von Zeichenfolgen, die die *Students/Index.cshtml.cs* `PageModel` Sortierung Parameter enthalten:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]


Update der *Students/Index.cshtml.cs* `OnGetAsync` durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Der obige Code empfängt eine `sortOrder` Parameter aus der Abfragezeichenfolge in der URL. Die URL (einschließlich der Abfragezeichenfolge) wird generiert, indem die [Anchor-Tag-Hilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

Die `sortOrder` ist "Name" oder "Date". Die `sortOrder` Parameter ist optional gefolgt von "_desc" absteigenden Reihenfolge an. Standardmäßig wird eine aufsteigende Sortierreihenfolge verwendet.

Wenn die Indexseite vom angefordert wird, die **Studenten** verknüpfen, gibt es keine Abfragezeichenfolge. Den Studenten werden in aufsteigender Reihenfolge nach dem Nachnamen angezeigt. Wird standardmäßig (fallen durch Groß-/Kleinschreibung) wird aufsteigender Reihenfolge nach dem Nachnamen der `switch` Anweisung. Wenn der Benutzer eine Spaltenüberschrift Verknüpfung, die den entsprechenden klickt `sortOrder` Wert in den Wert der Abfragezeichenfolge angegeben ist.

`NameSort`und `DateSort` werden von der Seite "Razor" So konfigurieren Sie die Spaltenüberschrift Hyperlinks mit den entsprechenden Abfragezeichenfolgen-Werte verwendet:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Der folgende Code enthält die C#- [?: Operator](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

Die erste Zeile gibt an, dass `sortOrder` ist null oder leer und `NameSort` -Wert von "Name_desc." Wenn `sortOrder` ist **nicht** null oder leer und `NameSort` auf eine leere Zeichenfolge festgelegt ist.

Die `?: operator` ist auch bekannt als der ternäre Operator.

Diese beiden Anweisungen aktivieren die Sicht die Spalte Überschrift Hyperlinks wie folgt festgelegt:

| Aktuelle Sortierreihenfolge | Letzte Name Hyperlink | Datum-Link |
|:--------------------:|:-------------------:|:--------------:|
| Letzte aufsteigend | descending        | ascending      |
| Letzte Name absteigend | ascending           | ascending      |
| Datum aufsteigend       | ascending           | descending     |
| Absteigend nach Datum      | ascending           | ascending      |

Die Methode verwendet LINQ to Entities an die Spalte zu sortieren. Im Code Initialisiert ein `IQueryable<Student> ` vor der Switch-Anweisung in der Switch-Anweisung ändert:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-)]

 Wenn ein`IQueryable` erstellt oder geändert wird, wird keine Abfrage an die Datenbank gesendet. Die Abfrage wird nicht ausgeführt, bis die `IQueryable` Objekt in einer Auflistung konvertiert wird. `IQueryable`durch Aufrufen einer Methode wie z. B. in einer Auflistung konvertiert `ToListAsync`. Aus diesem Grund die `IQueryable` code Ergebnisse in einer einzelnen Abfrage, die nicht, bis die folgende Anweisung ausgeführt wird:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`Ausführliche mit einer großen Anzahl von Spalten konnte abgerufen werden.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Die Indexansicht Student Spaltenüberschrift Links hinzufügen

Ersetzen Sie den Code in *Students/Index.cshtml*, mit den folgenden hervorgehobenen Code:

[!code-html[](intro/samples/cu/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Der vorangehende Code:

* Fügt links zu den `LastName` und `EnrollmentDate` Spaltenüberschriften.
* Verwendet die Informationen in `NameSort` und `DateSort` zum Einrichten von Links mit den aktuellen Werten der Sort-Reihenfolge.

So überprüfen Sie die Funktionsfähigkeit der Sortierung:

* Führen Sie die app, und wählen Sie die **Studenten** Registerkarte.
* Klicken Sie auf **Nachname**.
* Klicken Sie auf **Registrierungsdatum**.

So erhalten Sie ein besseres Verständnis des Codes:

* In *Student/Index.cshtml.cs*, legen Sie einen Haltepunkt auf `switch (sortOrder)`.
* Hinzufügen einer Überwachung für `NameSort` und `DateSort`.
* In *Student/Index.cshtml*, legen Sie einen Haltepunkt auf `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Durchlaufen Sie den Debugger.

## <a name="add-a-search-box-to-the-students-index-page"></a>Den Studenten Indexseite ein Suchfeld hinzufügen

So fügen Sie Filter auf die Indexseite Studenten hinzu

* Ein Textfeld und eine Schaltfläche "Absenden" wird auf der Seite "Razor" hinzugefügt. Das Textfeld liefert eine Suchzeichenfolge ein, auf dem ersten oder letzten.
* Das Seitenmodell wird aktualisiert, und der Wert des Textfelds verwenden.

### <a name="add-filtering-functionality-to-the-index-method"></a>Hinzufügen von Filterfunktionen zur Verfügung, die Index-Methode

Update der *Students/Index.cshtml.cs* `OnGetAsync` durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Der vorangehende Code:

* Fügt der `searchString` Parameter an die `OnGetAsync` Methode. Der Zeichenfolgenwert für die Suche wird aus einem Textfeld empfangen, die im nächsten Abschnitt hinzugefügt wird.
* Der LINQ-Anweisung hinzugefügt eine `Where` Klausel. Die `Where` -Klausel wählt nur die Studenten, deren vor- oder Nachnamen die zu suchende Zeichenfolge enthält. Die LINQ-Anweisung ausgeführt wird, nur dann, wenn ein zu suchende Wert.

Hinweis: Der vorhergehende Code Ruft die `Where` Methode auf eine `IQueryable` Objekt und den Filter auf dem Server verarbeitet wird. In einigen Szenarien Tha app aufrufen kann die `Where` -Methode eine Erweiterungsmethode für eine in-Memory-Auflistung. Nehmen wir beispielsweise an `_context.Students` ändert sich von EF Core `DbSet` an eine Repository-Methode, die gibt eine `IEnumerable` Auflistung. Das Ergebnis wäre normalerweise identisch, aber in einigen Fällen unterscheiden.

Angenommen, die .NET Framework-Implementierung des `Contains` vergleicht standardmäßig Groß-/Kleinschreibung beachtet. In SQL Server `Contains` Bindungsfehlern richtet sich nach der Einstellung der Sortierreihenfolge der SQL Server-Instanz. SQL Server wird standardmäßig auf Groß-/Kleinschreibung. `ToUpper`kann aufgerufen werden, um den Test explizit Groß-/Kleinschreibung durchzuführen:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Der vorangehende Code wird sichergestellt, dass die Groß-/Kleinschreibung erfolgt, wenn der Code ändert sich verwendet `IEnumerable`. Wenn `Contains` aufgerufen wird, auf ein `IEnumerable` -Auflistung, die .NET Core-Implementierung verwendet. Wenn `Contains` aufgerufen wird, auf ein `IQueryable` -Objekt, wird die datenbankimplementierung verwendet. Zurückgeben einer `IEnumerable` aus einem Repository kann eine erhebliche Leistungssteigerung Penality haben:

1. Alle Zeilen werden von der DB-Server zurückgegeben.
1. Der Filter wird auf die zurückgegebenen Zeilen in der Anwendung angewendet.

Es ist eine Leistungseinbuße zum Aufrufen `ToUpper`. Die `ToUpper` Code Fügt eine Funktion in der WHERE-Klausel der t-SQL SELECT-Anweisung. Die hinzugefügte Funktion wird verhindert, dass den Optimierer einen Index verwenden. Davon ausgehend, dass SQL als Groß-/Kleinschreibung installiert ist, es wird empfohlen, vermeiden Sie die `ToUpper` aufgerufen wird, wenn es nicht erforderlich ist.

### <a name="add-a-search-box-to-the-student-index-view"></a>Fügen Sie ein Suchfeld auf die Indexansicht Student

In *Views/Student/Index.cshtml*, fügen Sie folgenden hervorgehobenen Code zum Erstellen einer **Suche** Schaltfläche und Sortiment Chrome.

[!code-html[](intro/samples/cu/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Der obige Code verwendet die `<form>` [tag Helper](xref:mvc/views/tag-helpers/intro) Suchtextfeld und Schaltfläche hinzufügen. Wird standardmäßig der `<form>` Tag Hilfsprogramm sendet Formulardaten mit einer POST-Anforderung. Mit POST werden die Parameter in der HTTP-Nachrichtentext und nicht in der URL übergeben. Wenn HTTP GET verwendet wird, werden die Formulardaten als Abfragezeichenfolgen in der URL übergeben. Übergeben die Daten mit Abfragezeichenfolgen kann Benutzer die URL von Lesezeichen. Die [W3C Richtlinien](https://www.w3.org/2001/tag/doc/whenToUseGet.html) wird empfohlen, dass GET verwendet werden soll, wenn das Aktionsergebnis ein Update nicht.

Testen der App:

* Wählen Sie die **Studenten** Registerkarte und geben Sie eine Suchzeichenfolge ein.
* Wählen Sie **Suche**.

Beachten Sie, dass die URL der zu suchende Zeichenfolge enthält.

```html
http://localhost:5000/Students?SearchString=an
```

Wenn die Seite mit einem Lesezeichen versehen ist, wird das Lesezeichen enthält die URL der Seite und die `SearchString` einer Abfragezeichenfolge. Die `method="get"` in der `form` Tag ist die Ursache der Abfragezeichenfolge an, die generiert werden.

Aktuell, wenn eine Spaltenüberschrift sortieren Verknüpfung aktiviert ist, der Filter Wert aus der **Suche** Feld verloren gegangen ist. Der verlorene Filterwert ist im nächsten Abschnitt fest.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Den Studenten Indexseite Pagingfunktionen hinzufügen

In diesem Abschnitt eine `PaginatedList` -Klasse erstellt, um die Paginierung unterstützt. Die `PaginatedList` -Klasse verwendet `Skip` und `Take` Anweisungen zum Filtern von Daten auf dem Server statt alle Zeilen der Tabelle abzurufen. Die folgende Abbildung zeigt die Pagingschaltflächen.

![Studenten Indexseite mit Paginierungslinks](sort-filter-page/_static/paging.png)

Erstellen Sie in den Projektordner `PaginatedList.cs` durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/PaginatedList.cs)]

Die `CreateAsync` Methode im vorangehenden Code akzeptiert Seitengröße und die Seitenzahl und wendet die entsprechenden `Skip` und `Take` Anweisungen, die die `IQueryable`. Wenn `ToListAsync` aufgerufen wird, auf die `IQueryable`, sie gibt eine Liste, die nur die angeforderte Seite enthält. Die Eigenschaften `HasPreviousPage` und `HasNextPage` dienen zum Aktivieren oder deaktivieren Sie **vorherige** und **Weiter** paging Schaltflächen.

Die `CreateAsync` Methode dient zum Erstellen der `PaginatedList<T>`. Ein Konstruktor kann nicht erstellt werden. die `PaginatedList<T>` -Objekt Konstruktoren nicht asynchronen Code ausführen.

## <a name="add-paging-functionality-to-the-index-method"></a>Fügen Sie Pagingfunktionen zur Index-Methode

In *Students/Index.cshtml.cs*, aktualisieren Sie den Typ des `Student` aus `IList<Student>` auf `PaginatedList<Student>`:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Update der *Students/Index.cshtml.cs* `OnGetAsync` durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-)]

Der vorangehende Code fügt den Seitenindex aktuellen `sortOrder`, und die `currentFilter` auf die Methodensignatur.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Alle Parameter sind null, wenn:

* Die Seite aufgerufen wird die **Studenten** Link.
* Der Benutzer noch keine Auslagerung oder Sortierung von Link geklickt hat.

Wenn ein Auslagerung Link geklickt wird, enthält die Indexvariable Seite die Seitenzahl angezeigt.

`CurrentSort`Stellt den Razor-Page mit der aktuellen Sortierung. Die aktuelle Sortierreihenfolge muss in den Paginierungslinks, behalten Sie die Sortierreihenfolge beim Paging enthalten sein.

`CurrentFilter`Stellt den Razor-Page mit die aktuelle Filterzeichenfolge bereit. Die `CurrentFilter` Wert:

* Muss in die Auslagerungsdatei Links enthalten sein, um die filtereinstellungen während der Auslagerung zu gewährleisten.
* Muss in das Textfeld wiederhergestellt werden, wenn die Seite erneut angezeigt wird.

Wenn die Suchzeichenfolge beim Paging geändert wird, wird die Seite auf 1 zurückgesetzt. Die Seite sind auf 1 zurückgesetzt wird, der neue Filter kann in verschiedenen anzuzeigenden ergeben. Bei ein Suchwert Eingabe und **Absenden** ausgewählt ist:

* Die Suchzeichenfolge wird geändert.
* Die `searchString` Parameter nicht null ist.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

Die `PaginatedList.CreateAsync` -Methode konvertiert die Student-Abfrage in einer einzelnen Seite der Schüler in einen Auflistungstyp, die Paging unterstützt. Diese einzelnen Studenten-Seite wird auf der Seite "Razor" übergeben.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Die zwei Fragezeichen in `PaginatedList.CreateAsync` darstellen der [Null-Sammeloperator](https://docs.microsoft.com/ dotnet/csharp/language-reference/operators/null-conditional-operator). Der Null-Sammeloperator definiert einen Standardwert für einen NULL-Werte zulässt. Der Ausdruck `(pageIndex ?? 1)` bedeutet, dass der Rückgabewert von `pageIndex` , wenn er einen Wert aufweist. Wenn `pageIndex` verfügt über einen Wert verfügen, geben 1 zurück.

## <a name="add-paging-links-to-the-student-razor-page"></a>Paginierungslinks zu den Student Razor-Page hinzufügen

Aktualisieren Sie das Markup in *Students/Index.cshtml*. Die Änderungen werden hervorgehoben:

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-)]

Die Spalte Header Links verwenden die Abfragezeichenfolge übergeben Sie die aktuellen Suchzeichenfolge für die `OnGetAsync` Methode, damit der Benutzer in Filterergebnisse sortieren kann:

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=28-31)]

Tag-Hilfsprogramme sind die Auslagerung Schaltflächen angezeigt:

[!code-html[](intro/samples/cu/Pages/Students/Index.cshtml?range=72-)]

Führen Sie die app, und navigieren Sie zu der Seite "Students".

* Sie sicher, dass Paging funktioniert, klicken Sie auf die Links Auslagerung in verschiedenen Sortierreihenfolgen.
* Um sicherzustellen, dass die Auslagerungsdatei ordnungsgemäß funktioniert, Sortieren und filtern, geben Sie eine Suchzeichenfolge ein, und wiederholen Sie den Paging aus.

![Studenten Indexseite mit Paginierungslinks](sort-filter-page/_static/paging.png)

So erhalten Sie ein besseres Verständnis des Codes:

* In *Student/Index.cshtml.cs*, legen Sie einen Haltepunkt auf `switch (sortOrder)`.
* Hinzufügen einer Überwachung für `NameSort`, `DateSort`, `CurrentSort`, und `Model.Student.PageIndex`.
* In *Student/Index.cshtml*, legen Sie einen Haltepunkt auf `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Durchlaufen Sie den Debugger.

## <a name="update-the-about-page-to-show-student-statistics"></a>Aktualisieren Sie die Info-Seite, um Student Statistiken anzeigen

In diesem Schritt *Pages/About.cshtml* wird aktualisiert, um anzuzeigen, wie viele Studenten für jedes Registrierungsdatum registriert haben. Das Update Gruppierung verwendet, und umfasst die folgenden Schritte aus:

* Erstellen Sie eine Modellklasse Ansicht für die Daten, die verwendet werden, indem Sie die **zu** Seite.
* Ändern Sie das Modell für Razor-Page und die Seite.

### <a name="create-the-view-model"></a>Erstellen des Modells anzeigen

Erstellen einer *SchoolViewModels* Ordner in der *Modelle* Ordner.

In der *SchoolViewModels* Ordner Hinzufügen einer *EnrollmentDateGroup.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Aktualisieren Sie das Modell der Info-Seite

Update der *Pages/About.cshtml.cs* Datei durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/About.cshtml.cs)]

Registrierungsdatum Student-Entität gruppiert, berechnet die Anzahl der Entitäten in jeder Gruppe und speichert die Ergebnisse in einer Auflistung von LINQ-Anweisung `EnrollmentDateGroup` Model-Objekte anzeigen.

Hinweis: Die LINQ `group` Befehl wird derzeit von EF Core nicht unterstützt. Im vorangehenden Code werden alle Studentendatensätze von SQL Server zurückgegeben. Die `group` Befehl wird in der app Razor-Pages nicht auf dem SQL Server angewendet. EF Core 2.1 unterstützt diese LINQ `group` Operator und die Gruppierung tritt auf, auf dem SQL Server. Finden Sie unter [Relational: übersetzen GroupBy()-Funktion to SQL unterstützen](https://github.com/aspnet/EntityFrameworkCore/issues/2341). [EF Core 2.1](https://github.com/aspnet/EntityFrameworkCore/wiki/roadmap) werden mit .NET Core 2.1 veröffentlicht werden. Weitere Informationen finden Sie unter der [Roadmap für die .NET Core](https://github.com/dotnet/core/blob/master/roadmap.md).

### <a name="modify-the-about-razor-page"></a>Ändern Sie die zu den Razor-Pages

Ersetzen Sie den Code in der *Views/Home/About.cshtml* Datei durch den folgenden Code:

[!code-html[](intro/samples/cu/Pages/About.cshtml)]

Führen Sie die app, und navigieren Sie zu der Seite "Info". Die Anzahl der Schüler für jedes Registrierungsdatum wird in einer Tabelle angezeigt.

Wenn Probleme können nicht zu lösen auftreten, laden Sie die [abgeschlossene Anwendung für diese Stufe](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![Zu den Seiten](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Debuggen von ASP.NET Core 2.x-Quellcode](https://github.com/aspnet/Docs/issues/4155)

In den nächsten Lernprogrammen verwendet die app Migrationen, um das Datenmodell zu aktualisieren.

>[!div class="step-by-step"]
[Zurück](xref:data/ef-rp/crud)
[Weiter](xref:data/ef-rp/migrations)
