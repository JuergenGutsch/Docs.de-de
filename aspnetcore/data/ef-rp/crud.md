---
title: Razor-Pages mit EF-Kern - CRUD - 2-8
author: rick-anderson
description: "Zeigt, wie erstellen, lesen, aktualisieren und Löschen mit EF Core"
manager: wpickett
ms.author: riande
ms.date: 10/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/crud
ms.openlocfilehash: 757aeb713b645cea0fe633b150784184d2d3571e
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="create-read-update-and-delete---ef-core-with-razor-pages-2-of-8"></a>Erstellen Sie, lesen Sie, aktualisieren Sie und löschen Sie-EF-Core mit Razor-Pages (2 von 8)

Durch [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

In diesem Lernprogramm die scaffolded CRUD-Vorgänge (erstellen, lesen, aktualisieren und löschen) Code überprüft und angepasst wird.

Hinweis: Zur Minimierung des Komplexität, und behalten Sie diese Lernprogramme EF Core konzentriert, ist EF Kerncode in den Razor-Pages-Seite-Modellen verwendet. Einige Entwickler mithilfe einer Ebene oder im Repository-Musters in eine Abstraktionsebene zwischen der Benutzeroberfläche (Razor-Pages) und die Datenzugriffsebene zu erstellen.

In diesem Lernprogramm, erstellen, bearbeiten, löschen und Details Razor-Pages in der *Student* Ordner geändert werden.

Der scaffolded Code verwendet das folgende Muster für Seiten erstellen, bearbeiten und löschen:

* Abrufen und anzeigen die angeforderten Daten mit der HTTP-GET-Methode `OnGetAsync`.
* Änderungen an den Daten speichern, mit der HTTP-POST-Methode `OnPostAsync`.

Die Index- und Details Seiten abrufen, und zeigen die angeforderten Daten mit der HTTP-GET-Methode`OnGetAsync`

## <a name="replace-singleordefaultasync-with-firstordefaultasync"></a>Ersetzen Sie SingleOrDefaultAsync durch FirstOrDefaultAsync

Verwendet der generierte Code [SingleOrDefaultAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) , die angeforderte Entität abzurufen. [FirstOrDefaultAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) ist jedoch effizienter, auf eine Entität abrufen:

* Es sei denn, Sie muss überprüfen, ob der Code ist nicht mehr als eine Entität, die von der Abfrage zurückgegeben. 
* `SingleOrDefaultAsync`Weitere Daten abruft und unnötige Arbeit ausführt.
* `SingleOrDefaultAsync`löst eine Ausnahme aus, wenn mehr als eine Entität, die den Filterteil entspricht.
*  `FirstOrDefaultAsync`keine auszulösen, wenn es mehr als eine Entität, die den Filterteil passt.

Ersetzen Sie Global `SingleOrDefaultAsync` mit `FirstOrDefaultAsync`. `SingleOrDefaultAsync`wird in 5 Stellen verwendet werden:

* `OnGetAsync`in der Seite "Details".
* `OnGetAsync`und `OnPostAsync` auf den Seiten bearbeiten und löschen.

<a name="FindAsync"></a>
### <a name="findasync"></a>FindAsync

Im Wesentlichen Codezeilen scaffolded [FindAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) kann verwendet werden, anstelle von `FirstOrDefaultAsync` oder `SingleOrDefaultAsync`. 

`FindAsync`:

* Sucht nach einer Entität mit dem Primärschlüssel (PS). Wenn eine Entität mit der PK vom Kontext nachverfolgt wird, wird er ohne eine Anforderung mit der Datenbank zurückgegeben.
* Ist einfach und präzise.
* Ist optimiert, um eine einzelne Entität zu suchen.
* Können Perf Vorteile in einigen Situationen, aber sie selten eine sprachbasierte für Szenarien mit normalen Web eintreffen.
* Implizit verwendet [FirstAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) anstelle von [SingleAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).
Wenn jedoch andere Entitäten enthalten sein sollen, bei Find wird nicht mehr geeignet. Dies bedeutet, dass Sie möglicherweise Abbrechen suchen, und verschieben Sie Ihre app im Verlauf einer Abfrage.

## <a name="customize-the-details-page"></a>Anpassen der Seite "Details"

Navigieren Sie zu `Pages/Students` Seite. Die **bearbeiten**, **Details**, und **löschen** Links werden generiert, indem die [Anker Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in die *Seiten/Studenten / Index.cshtml* Datei.

[!code-cshtml[Main](intro/samples/cu/Pages/Students/Index1.cshtml?range=40-44)]

Wählen Sie einen Link Details. Die URL weist das Format `http://localhost:5000/Students/Details?id=2`. Den Studenten-ID übergeben wird, verwenden eine Abfragezeichenfolge (`?id=2`).

Aktualisieren Sie die bearbeiten, Details und Löschen von Razor-Pages verwenden die `"{id:int}"` routenvorlage. Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`.

Eine Anforderung zur Seite mit der "{-Id: Int}" routenvorlage, die **nicht** eine ganze Zahl Route Wert gibt eine HTTP 404 (nicht gefunden) Fehler enthalten. Beispielsweise `http://localhost:5000/Students/Details` Fehler 404 zurückgegeben. Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:

 ```cshtml
@page "{id:int?}"
```

Die app auszuführen, klicken Sie auf einen Link Details und überprüfen Sie die URL ist die ID als Routendaten übergeben (`http://localhost:5000/Students/Details/2`).

Ändern nicht global `@page` auf `@page "{id:int}"`, auf diese Weise wird die Links zur Startseite sowie Seiten zu erstellen.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Fügen Sie verwandte Daten

Scaffolded Code für die Studenten Indexseite enthalten nicht die `Enrollments` Eigenschaft. In diesem Abschnitt, den Inhalt der `Enrollments` Sammlung wird die Seite "Details" angezeigt.

Die `OnGetAsync` Methode *Pages/Students/Details.cshtml.cs* verwendet die `FirstOrDefaultAsync` Methode zum Abrufen einer einzelnes `Student` Entität. Fügen Sie den folgenden hervorgehobenen Code hinzu:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

Die `Include` und `ThenInclude` Methoden dazu führen, dass den Kontext beim Laden der `Student.Enrollments` Navigationseigenschaft, und innerhalb jeder Registrierung der `Enrollment.Course` Navigationseigenschaft. Diese Methoden sind Examinied ausführlich im Lernprogramm lesen produktbezogene Daten.

Die `AsNoTracking` Methode verbessert die Leistung in Szenarien, bei die Entitäten zurückgegeben werden im aktuellen Kontext nicht aktualisiert. `AsNoTracking`weiter unten in diesem Lernprogramm wird erläutert werden.

### <a name="display-related-enrollments-on-the-details-page"></a>Verwandte Registrierungen auf der Seite "Details" anzeigen

Open *Pages/Students/Details.cshtml*. Fügen Sie zum Anzeigen einer Liste der Registrierung die folgenden hervorgehobenen Code hinzu:

 <!--2do ricka. if doesn't change, remove dup -->
[!code-cshtml[Main](intro/samples/cu/Pages/Students/Details1.cshtml?highlight=32-53)]

Wenn Code Einzug falsch ist, nachdem Sie der Code eingefügt wird, drücken Sie STRG + K + D, um sie zu korrigieren.

Der vorangehende Code durchläuft die Entitäten in der `Enrollments` Navigationseigenschaft. Für jede Anmeldung zeigt es den Kurstitel und die Dienstqualität dargestellt. Der Titel des Kurses wird abgerufen, aus der Kurs-Entität, die in gespeichert ist die `Course` Navigationseigenschaft der Registrierung Entität.

Die app auszuführen, wählen Sie die **Studenten** Registerkarte, und klicken Sie auf die **Details** Link, um ein Student. Die Liste der Kurse und Bewertungen für die ausgewählten Studenten wird angezeigt.

## <a name="update-the-create-page"></a>Aktualisieren Sie die Seite "erstellen"

Update der `OnPostAsync` Methode im *Pages/Students/Create.cshtml.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>
### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Überprüfen Sie die [TryUpdateModelAsync](https://docs.microsoft.com/ dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) Code:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

Im vorangehenden Code `TryUpdateModelAsync<Student>` versucht, Aktualisieren der `emptyStudent` -Objekt unter Verwendung der übermittelte Formularwerte aus der [PageContext](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) Eigenschaft in der [PageModel](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel?view=aspnetcore-2.0). `TryUpdateModelAsync`aktualisiert nur die aufgeführten Eigenschaften (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).

Im vorhergehenden Beispiel:

* Das zweite Argument (` "student", // Prefix`) ist das Präfix verwendet, um Werte zu suchen. Es ist nicht in der Groß-/Kleinschreibung beachtet.
* Die übermittelte Formularwerte werden konvertiert, auf die Typen in der `Student` -Modells mit [modellbindung](xref:mvc/models/model-binding#how-model-binding-works).

<a id="overpost"></a>
### <a name="overposting"></a>Overposting

Mithilfe von `TryUpdateModel` Felder mit bereitgestellter Werte aktualisieren, ist eine bewährte Sicherheitsmethode, da er Overposting verhindert. Nehmen wir beispielsweise an, die Student-Entität enthält eine `Secret` -Eigenschaft, die dieser Webseite aktualisieren oder hinzufügen sollten nicht:

[!code-csharp[Main](intro/samples/cu/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Selbst wenn die app ist eine `Secret` Feld in der CREATE-/Update-Razor-Page, ein Hacker konnte festgelegt die `Secret` Wert durch overposting. Ein Hacker ein Tool wie Fiddler verwenden kann, oder Schreiben von JavaScript, zum Bereitstellen einer `Secret` Wert bilden. Der ursprüngliche Code einschränken nicht die Felder, die der Modellbinder verwendet werden, wenn es sich um eine Student-Instanz erstellt.

Alle von Ihnen gewünschten Hackers für angegebene Wert die `Secret` Formularfelds in der Datenbank aktualisiert. Die folgende Abbildung zeigt das Hinzufügen von Fiddler-Tool die `Secret` Feld (mit dem Wert "OverPost"), um die übermittelte Formularwerte.

![Hinzufügen eines geheimen Felds Fiddler](../ef-mvc/crud/_static/fiddler.png)

Der Wert "OverPost" erfolgreich, um hinzugefügt wurde die `Secret` Eigenschaft der eingefügten Zeile. Die app-Designer, die nie beabsichtigt waren die `Secret` -Eigenschaft auf der Seite "erstellen" festgelegt werden.

<a name="vm"></a>
### <a name="view-model"></a>ViewModel-Element

Einem Ansichtsmodell enthält in der Regel eine Teilmenge der Eigenschaften in das von der Anwendung verwendete Modell enthalten. Das Anwendungsmodell wird häufig die Domänenmodell bezeichnet. Das Domänenmodell enthält in der Regel alle Eigenschaften, die durch die entsprechende Entität in der Datenbank erforderlich. Das Ansichtsmodell enthält nur die Eigenschaften, die für die Benutzeroberflächenebene (z. B. die Seite "erstellen") erforderlich sind. Zusätzlich zu den Ansichtsmodell einige apps verwenden ein Bindungsmodell oder Eingabe zum Übergeben von Daten zwischen der Seitenklasse Modell Razor-Pages und den Browser. Beachten Sie Folgendes `Student` Ansichtsmodell:

[!code-csharp[Main](intro/samples/cu/Models/StudentVM.cs)]

Ansichtsmodelle bieten eine alternative Möglichkeit, Overposting zu verhindern. Das Ansichtsmodell enthält nur die Eigenschaften, um (Anzeige) anzuzeigen oder zu aktualisieren.

Der folgende code verwendet die `StudentVM` Modell zum Erstellen eines neuen Studenten anzeigen:

[!code-csharp[Main](intro/samples/cu/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Die [SetValues](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) Methode legt die Werte dieses Objekts fest, indem Werte aus einer anderen [PropertyValues](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) Objekt. `SetValues`verwendet die Eigenschaft namensübereinstimmung. Ansichtstyp Modell muss nicht mit dem Typ des Modells verknüpft sein, es muss lediglich Eigenschaften vorhanden, die übereinstimmen.

Mit `StudentVM` erfordert [CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Students/CreateVM.cshtml) aktualisiert werden, um verwenden `StudentVM` statt `Student`.

In Razor-Pages die `PageModel` abgeleiteten Klasse wird das Modell anzeigen. 

## <a name="update-the-edit-page"></a>Aktualisieren Sie die Seite "Bearbeiten"

Aktualisieren Sie das Modell für das Bearbeiten (Seite):

[!code-csharp[Main](intro/samples/cu/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Die codeänderungen sind auf der Seite "erstellen", mit wenigen Ausnahmen vergleichbar:

* `OnPostAsync`verfügt über ein optionales `id` Parameter.
* Die aktuelle Studenten wird aus der Datenbank abgerufen anstatt zu einer leeren Student erstellen.
* `FirstOrDefaultAsync`wurde durch ersetzt [FindAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync?view=efcore-2.0). `FindAsync`ist eine gute Wahl, wenn Sie eine Entität aus dem Primärschlüssel auswählen. Finden Sie unter [FindAsync](#FindAsync) für Weitere Informationen.

### <a name="test-the-edit-and-create-pages"></a>Testen Sie die Bearbeitung und erstellen Seiten

Erstellen Sie und bearbeiten Sie ein paar Student-Entitäten.

## <a name="entity-states"></a>Status der Entität

Der Datenbankkontext der nachverfolgt, ob Entitäten im Arbeitsspeicher mit ihren entsprechenden Zeilen in der Datenbank synchronisiert sind. Die DB-Sync Kontextinformationen bestimmt, was geschieht, wenn `SaveChanges` aufgerufen wird. Z. B. wenn eine neue Entität wird übergeben die `Add` Methode, die Zustand der Entität, um festgelegt ist `Added`. Wenn `SaveChanges` aufgerufen wird, wird die DB Kontext gibt einen SQL INSERT-Befehl.

Eine Entität kann in einem der folgenden Zustände werden:

* `Added`: Die Entität ist nicht in der Datenbank noch vorhanden. Die `SaveChanges` -Methode gibt eine INSERT-Anweisung.

* `Unchanged`: Keine Änderungen müssen mit dieser Entität gespeichert werden. Eine Entität weist diesen Status beim Lesen aus der Datenbank.

* `Modified`: Einige oder alle Eigenschaftswerte für die Entität wurden geändert. Die `SaveChanges` -Methode gibt eine UPDATE-Anweisung.

* `Deleted`: Die Entität wurde zum Löschen markiert wurde. Die `SaveChanges` -Methode gibt eine DELETE-Anweisung.

* `Detached`: Die Entität wird nicht von den Datenbankkontext nachverfolgt wird.

Zustandsänderungen werden in einer desktop-app in der Regel automatisch festgelegt. Eine Entität gelesen wird, werden Änderungen, und der Entitätszustand automatisch geändert werden, um `Modified`. Aufrufen von `SaveChanges` generiert eine SQL UPDATE-Anweisung, die nur die geänderten Eigenschaften aktualisiert.

In einer Web-app die `DbContext` , liest eine Entität und zeigt die Daten freigegeben werden, nachdem eine Seite gerendert wird. Wenn ein `OnPostAsync` -Methode aufgerufen wird, erfolgt eine neue webanforderung und durch eine neue Instanz der dem `DbContext`. Lesen erneut die Entität in diesem neuen Kontext simuliert desktop Verarbeitung.

## <a name="update-the-delete-page"></a>Aktualisieren Sie die Seite "löschen"

In diesem Abschnitt wird Code hinzugefügt, um einen benutzerdefinierten Fehler implementieren Meldung an, wenn der Aufruf von `SaveChanges` ein Fehler auftritt. Fügen Sie eine Zeichenfolge, um mögliche Fehlermeldungen enthalten:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Ersetzen Sie die `OnGetAsync`-Methode durch folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Der vorangehende Code enthält, den optionalen Parameter `saveChangesError`. `saveChangesError`Gibt an, ob die Methode nach einem Fehler beim Löschen der Student-Objekts aufgerufen wurde. Der Löschvorgang möglicherweise aufgrund von vorübergehenden Netzwerkproblemen fehlschlägt. Vorübergehende Netzwerkfehler werden eher in der Cloud. `saveChangesError`ist "false", wenn die Seite "löschen" `OnGetAsync` über die Benutzeroberfläche aufgerufen wird. Wenn `OnGetAsync` wird aufgerufen, indem `OnPostAsync` (da der Löschvorgang nicht erfüllt), wird die `saveChangesError` Parameter ist "true".

### <a name="the-delete-pages-onpostasync-method"></a>Die Delete Seiten OnPostAsync-Methode

Ersetzen Sie die `OnPostAsync` durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Der vorangehende Code Ruft die ausgewählte Entität ruft dann die `Remove` Methode, um die Entität Status festgelegt wird, um `Deleted`. Wenn `SaveChanges` aufgerufen wird, wird eine SQL-DELETE-Befehl generiert wird. Wenn `Remove` ein Fehler auftritt:

* Die DB-Ausnahme abgefangen wird.
* Die Delete-Seiten `OnGetAsync` -Methode aufgerufen wird und `saveChangesError=true`.

### <a name="update-the-delete-razor-page"></a>Aktualisieren Sie die Razor-Löschseite

Die folgende hervorgehobene Fehlermeldung der löschen Razor-Page hinzufügen.

[!code-cshtml[Main](intro/samples/cu/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Testen Sie löschen.

## <a name="common-errors"></a>Häufige Fehler

Student-Startseite "oder" andere Links funktionieren nicht:

Überprüfen Sie die Seite "Razor" enthält den richtigen `@page` Richtlinie. Beispielsweise die Student/Razor-Startseite sollten **nicht** eine routenvorlage enthalten:

```cshtml
@page "{id:int}"
```

Jeder Razor-Page umfasst die `@page` Richtlinie.

>[!div class="step-by-step"]
[Zurück](xref:data/ef-rp/intro)
[Weiter](xref:data/ef-rp/sort-filter-page)
