---
title: "Razor-Pages mit EF-Core - lesen verknüpften Daten - 6 8"
author: rick-anderson
description: "In diesem Lernprogramm lesen und Anzeigen von verknüpften Daten – d. h. die Daten, die das Entity Framework in Navigationseigenschaften lädt."
manager: wpickett
ms.author: riande
ms.date: 11/05/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/read-related-data
ms.openlocfilehash: ccb1e95ae2b43fd0a4c4b1ac9ed58a4d474ab3b6
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="reading-related-data---ef-core-with-razor-pages-6-of-8"></a>Lesen bezogene Daten per Push – EF-Core mit Razor-Pages (6 von 8)

Durch [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

In diesem Lernprogramm werden Daten gelesen und angezeigt. Verwandte Daten sind Daten, die in den Navigationseigenschaften EF Core lädt.

Wenn Probleme können nicht zu lösen auftreten, laden Sie die [abgeschlossene Anwendung für diese Stufe](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part6-related).

Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Lernprogramm:

![Kurse Indexseite](read-related-data/_static/courses-index.png)

![Indexseite für Dozenten](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Eager, explizite und lazy Loading-verknüpfter Daten

Es gibt mehrere Möglichkeiten, EF Core in die Navigationseigenschaften einer Entität verknüpfte Daten geladen werden können:

* [Unverzüglichem Laden](https://docs.microsoft.com/ef/core/querying/related-data#eager-loading). Unverzüglichem Laden ist eine Abfrage für einen Entitätstyp auch verknüpfte Entitäten lädt. Wenn die Entität gelesen wird, werden ihre verknüpften Daten abgerufen. Dies führt normalerweise zu einer einzelnen Join-Abfrage, die alle Daten abruft, die erforderlich ist. EF Core werden mehrere Abfragen für einige Typen von unverzüglichem Laden ausgeben. Mehrere Abfragen ausgeben kann effizienter sein, als bei einigen Abfragen in EF6 der Fall war, obwohl eine einzelne Abfrage vorhanden war. Unverzüglichem Laden wird angegeben, mit der `Include` und `ThenInclude` Methoden.

 ![Beispiel mit unverzüglichem Laden](read-related-data/_static/eager-loading.png)
 
 Unverzüglichem Laden sendet mehrere Abfragen aus, wenn eine Auflistung Nvavigation enthalten ist:

 * Eine Abfrage für die Hauptabfrage 
 * Eine Abfrage für jede Sammlung "Rand" in der Struktur laden.

* Trennen Sie Abfragen mit `Load`: die Daten in eine eigene Abfrage abgerufen werden können, und EF Core "Korrekturen einrichten" die Navigationseigenschaften. "Korrekturen einrichten" bedeutet, dass es sich bei EF Core füllt automatisch die Navigationseigenschaften. Trennen Sie Abfragen mit `Load` detaillierter Explicit laden als unverzüglichem Laden.

 ![Beispiel für eine eigene Abfrage](read-related-data/_static/separate-queries.png)

 Hinweis: EF Core behebt automatisch von Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden. Auch wenn die Daten für eine Navigationseigenschaft ist *nicht* explizit eingeschlossen, die Eigenschaft kann noch immer aufgefüllt werden, wenn einige oder alle verknüpften Entitäten wurden zuvor geladen.

* [Explizites Laden](https://docs.microsoft.com/ef/core/querying/related-data#explicit-loading). Wenn die Entität zuerst gelesen wird, ist nicht verbundene Daten abgerufen. Abrufen der zugehörigen Daten bei Bedarf muss Code geschrieben werden. Explizites Laden mit eine eigene Abfrage führt zu mehreren Abfragen an die Datenbank gesendet. Mit expliziten Laden gibt der Code die Navigationseigenschaften geladen werden. Verwenden der `Load` Methode, um das explizite laden auszuführen. Zum Beispiel:

 ![Explizites Laden-Beispiel](read-related-data/_static/explicit-loading.png)

* [Verzögertes Laden](https://docs.microsoft.com/ef/core/querying/related-data#lazy-loading). [EF Core unterstützt derzeit nicht das verzögertes Laden](https://github.com/aspnet/EntityFrameworkCore/issues/3797). Wenn die Entität zuerst gelesen wird, ist nicht verbundene Daten abgerufen. Eine Navigationseigenschaft zugegriffen wird, das zum ersten Mal werden automatisch für diese Navigationseigenschaft erforderlichen Daten abgerufen werden. Mit der Datenbank wird eine Abfrage gesendet jedes Mal eine Navigationseigenschaft zum ersten Mal zugegriffen wird.

* Die `Select` Operator lädt nur die verknüpften Daten erforderlich sind.

## <a name="create-a-courses-page-that-displays-department-name"></a>Erstellen Sie eine Kurse-Seite, die Name der Abteilung anzeigt

Die Kurs-Entität enthält, eine Navigationseigenschaft, die enthält die `Department` Entität. Die `Department` Entität enthält die Abteilung, die der Kurs zugewiesen ist.

So zeigen Sie den Namen der zugewiesenen Abteilung in einer Liste von Kurse an

* Abrufen der `Name` Eigenschaft aus der `Department` Entität.
* Die `Department` Entität stammen aus den `Course.Department` Navigationseigenschaft.

![ourse.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>
### <a name="scaffold-the-course-model"></a>Das Gerüst für die Kurs-Modell erstellen

* Beenden Sie Visual Studio.
* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).
* Führen Sie den folgenden Befehl aus:

 ```console
dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
 ```

Die vorherigen Befehl Gerüste der `Course` Modell. Öffnen Sie das Projekt in Visual Studio.

Erstellen Sie das Projekt. Der Build generiert Fehler wie folgt:

`1>Pages/Courses/Index.cshtml.cs(26,37,26,43): error CS1061: 'SchoolContext' does not
 contain a definition for 'Course' and no extension method 'Course' accepting a first
 argument of type 'SchoolContext' could be found (are you missing a using directive or
 an assembly reference?)`

 Globales ändern `_context.Course` auf `_context.Courses` (d. h. hinzufügen ein "s" `Course`). 7 Vorkommen gefunden und aktualisiert.

Open *Pages/Courses/Index.cshtml.cs* und untersuchen Sie die `OnGetAsync` Methode. Das Modul Gerüstbau angegeben unverzüglichem Laden für die `Department` Navigationseigenschaft. Die `Include` Methode gibt unverzüglichem Laden.

Führen Sie die app, und wählen Sie die **Kurse** Link. Department-Spalte zeigt die `DepartmentID`, die nicht hilfreich sind.

Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Der vorangehende Code fügt `AsNoTracking`. `AsNoTracking`verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden. Die Entitäten werden nicht nachverfolgt werden, da sie nicht im aktuellen Kontext aktualisiert werden.

Update *Views/Courses/Index.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

An den scaffolded Code wurden die folgenden Änderungen vorgenommen:

* Die Überschrift von Index in Courses geändert.
* Hinzugefügt eine **Anzahl** Spalte, die zeigt die `CourseID` Eigenschaftswert. Primärschlüssel werden nicht standardmäßig Gerüstbau, da normalerweise ohne Bedeutung für Endbenutzer sind. Allerdings ist in diesem Fall der Primärschlüssel sinnvoll.
* Geändert die **Abteilung** Spalte der Abteilungsname angezeigt. Der Code zeigt die `Name` Eigenschaft von der `Department` Entität, die geladen wird die `Department` Navigationseigenschaft:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Führen Sie die app, und wählen Sie die **Kurse** Registerkarte ", um die Liste mit den Abteilungsnamen anzuzeigen.

![Kurse Indexseite](read-related-data/_static/courses-index.png)

<a name="select"></a>
### <a name="loading-related-data-with-select"></a>Laden von verknüpften Daten mit Select

Die `OnGetAsync` Methode lädt verknüpfte Daten mit der `Include` Methode:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Die `Select` Operator lädt nur die verknüpften Daten erforderlich sind. Für die einzelnen Elemente wie die `Department.Name` verwendet eine SQL INNER JOIN. Für Auflistungen verwendet eine andere Datenbank zugreifen, aber Gleiches gilt für die.`Include` Operator für Auflistungen.

Der folgende Code lädt verknüpfte Daten mit der `Select` Methode:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Die `CourseViewModel`:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Finden Sie unter [IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) ein vollständiges Beispiel.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Erstellen Sie eine Lehrkräfte-Seite, in der Kurse und Registrierung angezeigt.

In diesem Abschnitt wird die Seite "Lehrkräfte" erstellt.

<a name="IP"></a>
![Indexseite für Dozenten](read-related-data/_static/instructors-index.png)

Auf dieser Seite liest und zeigt verknüpfte Daten auf folgende Weise:

* Die Liste der Lehrkräfte zeigt verknüpfte Daten aus der `OfficeAssignment` Entität (Office in der vorherigen Abbildung). Die `Instructor` und `OfficeAssignment` Entitäten sind in einer 1: 0 (null)-oder-1-Beziehung. Unverzüglichem Laden dient für die `OfficeAssignment` Entitäten. Unverzüglichem Laden ist in der Regel effizienter, wenn die verknüpften Daten angezeigt werden müssen. In diesem Fall werden die Office-Zuweisungen für die Kursleiter angezeigt.
* Wenn der Benutzer wählt einen Kursleiter (Harui in der vorherigen Abbildung), im Zusammenhang `Course` Entitäten werden angezeigt. Die `Instructor` und `Course` Entitäten sind in einer m: n-Beziehung. Eager loading für die `Course` Entitäten und die zugehörigen `Department` Entitäten verwendet wird. In diesem Fall können eine eigene Abfrage effizienter sein, da nur Kurse für den ausgewählten Kursleiter benötigt werden. Dieses Beispiel zeigt, wie mit unverzüglichem Laden für Navigationseigenschaften in Entitäten, die in den Navigationseigenschaften sind.
* Wenn der Benutzer einen Kurs (Chemie in der vorherigen Abbildung), wählt verknüpften Daten aus der `Enrollments` Entität wird angezeigt. In der vorherigen Abbildung sind Student-Name und Dienstqualität angezeigt. Die `Course` und `Enrollment` Entitäten sind in einer 1: n-Beziehung.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Erstellen Sie ein Ansichtsmodell für die Indexansicht Dozenten

Die Seite "Lehrkräfte" zeigt Daten aus drei verschiedenen Tabellen. Einem Ansichtsmodell wird erstellt, die die drei Entitäten, die die drei Tabellen darstellt.

In der *SchoolViewModels* Ordner erstellen *InstructorIndexData.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Die Instructor-Modell zu erstellen

* Beenden Sie Visual Studio.
* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).
* Führen Sie den folgenden Befehl aus:

 ```console
dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
 ```

Die vorherigen Befehl Gerüste der `Instructor` Modell. Öffnen Sie das Projekt in Visual Studio.

Erstellen Sie das Projekt. Der Build generiert Fehler.

Globales ändern `_context.Instructor` auf `_context.Instructors` (d. h. hinzufügen ein "s" `Instructor`). 7 Vorkommen gefunden und aktualisiert.

Führen Sie die app, und navigieren Sie zu der Seite "Lehrkräfte".

Ersetzen Sie *Pages/Instructors/Index.cshtml.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,20-)]

Die `OnGetAsync` -Methode akzeptiert optional Routendaten für die ID der ausgewählten Kursleiter.

Untersuchen Sie die Abfrage auf die *Pages/Instructors/Index.cshtml* Seite:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Die Abfrage verfügt über zwei enthält:

* `OfficeAssignment`: Die angezeigte in der [Lehrkräfte Ansicht](#IP).
* `CourseAssignments`: Die in der Courses vermittelten bringt.


### <a name="update-the-instructors-index-page"></a>Aktualisieren Sie die Indexseite Dozenten

Update *Pages/Instructors/Index.cshtml* durch Folgendes Markup:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Das vorhergehende Markup nimmt folgende Änderungen:

* Updates der `page` -Direktive aus `@page` auf `@page "{id:int?}"`. `"{id:int?}"`ist eine routenvorlage. Die routenvorlage ändert Ganzzahl Abfragezeichenfolgen in der URL, um Routendaten. Z. B. durch Klicken auf die **wählen** Link, um einen Kursleiter, wenn die Page-Anweisung eine URL wie die folgende erzeugt:

    `http://localhost:1234/Instructors?id=2`

    Wenn der Seitendirektive ist `@page "{id:int?}"`, wird die vorherige URL:

    `http://localhost:1234/Instructors/2`

* Seitentitel wird **Lehrkräfte**.
* Hinzugefügt ein **Office** Spalte `item.OfficeAssignment.Location` nur, wenn `item.OfficeAssignment` nicht null ist. Da dies eine 1: 0 (null)-oder-1-Beziehung ist, gibt es möglicherweise nicht verknüpfte OfficeAssignment Entität.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Hinzugefügt eine **Kurse** Spalte Kurse vermittelten jedes Kursleiter. Finden Sie unter [explizite Zeile Übergang mit `@:` ](xref:mvc/views/razor#explicit-line-transition-with-) Weitere Informationen über diese Razor-Syntax.

* Code hinzugefügt, der dynamisch hinzugefügt `class="success"` auf die `tr` Element des ausgewählten Kursleiter. Hiermit wird eine Hintergrundfarbe für die ausgewählte Zeile mit einem Bootstrap-Klasse.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Einen neuen Link mit der Bezeichnung hinzugefügt **wählen**. Dieser Link sendet die ausgewählten Instructor-ID, die die `Index` Methode und legt die Hintergrundfarbe fest.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Führen Sie die app, und wählen Sie die **Lehrkräfte** Registerkarte. Die Seite zeigt die `Location` (Office) aus den verknüpften `OfficeAssignment` Entität. Wenn OfficeAssignment "ist null, wird eine leere Tabellenzelle angezeigt.

![Lehrkräfte Indexseite, der keine Auswahl](read-related-data/_static/instructors-index-no-selection.png)

Klicken Sie auf die **wählen** Link. Der Stil zeilenänderungen.

### <a name="add-courses-taught-by-selected-instructor"></a>Fügen Sie ausgewählte Dozent unterrichtet Kurse hinzu

Update der `OnGetAsync` Methode im *Pages/Instructors/Index.cshtml.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-)]

Überprüfen Sie die aktualisierte Abfrage:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Die vorhergehende Abfrage fügt die `Department` Entitäten.

Der folgende Code, das ausgeführt wird, wenn Sie ein Kursleiter ausgewählt ist (`id != null`). Die ausgewählte Kursleiter wird aus der Liste der Kursleiter das Ansichtsmodell abgerufen. Des Ansichtsmodells `Courses` Eigenschaft geladen wird, mit der `Course` Entitäten aus dieser Dozenten `CourseAssignments` Navigationseigenschaft.

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

Die `Where` Methode gibt eine Auflistung zurück. In den vorherigen `Where` -Methode, nur einen einzigen `Instructor` Entität zurückgegeben wird. Die `Single` -Methode konvertiert die Auflistung in einem einzelnen `Instructor` Entität. Die `Instructor` Entität ermöglicht den Zugriff auf die `CourseAssignments` Eigenschaft. `CourseAssignments`ermöglicht den Zugriff auf den zugehörigen `Course` Entitäten.

![Instructor-Kurse-m:M](complex-data-model/_static/courseassignment.png)

Die `Single` Methode wird von einer Sammlung verwendet, wenn die Auflistung nur ein Element aufweist. Die `Single` Methode löst eine Ausnahme aus, wenn die Auflistung leer ist oder wenn mehr als ein Element vorhanden ist. Ist eine Alternative `SingleOrDefault`, womit einen Default-Wert (in diesem Fall null), wenn die Auflistung leer ist. Mithilfe von `SingleOrDefault` für eine leere Auflistung:

* Löst eine Ausnahme (aus beim Suchen nach einem `Courses` Eigenschaft auf einen null-Verweis).
* Die Ausnahmemeldung würde weniger deutlich die Ursache des Problems angeben.

Der folgende Code füllt des Ansichtsmodells `Enrollments` Eigenschaft, wenn Sie ein Kurs ausgewählt ist:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Fügen Sie das folgende Markup bis zum Ende der *Pages/Courses/Index.cshtml* Razor-Page:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-)]

Das vorhergehende Markup zeigt eine Liste der Kurse, die im Zusammenhang mit einen Kursleiter beim ein Kursleiter ausgewählt ist.

Testen der app an. Klicken Sie auf eine **wählen** Link auf der Seite "Lehrkräfte".

![Lehrkräfte Index Seite Dozenten ausgewählt](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a>Student-Daten anzeigen

In diesem Abschnitt wird die app aktualisiert, um die Student-Daten für einen ausgewählten Kurs anzuzeigen.

Aktualisieren Sie die Abfrage in der `OnGetAsync` Methode im *Pages/Instructors/Index.cshtml.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Update *Pages/Instructors/Index.cshtml*. Fügen Sie am Ende der Datei das folgende Markup hinzu:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Das vorhergehende Markup zeigt eine Liste der Studenten, die registriert werden in den ausgewählten Kurs.

Aktualisieren Sie die Seite, und wählen Sie einen Kursleiter. Wählen Sie einen Kurs, finden in der Liste der registrierten Studenten und deren Qualitäten.

![Lehrkräfte Index Seite Kursleiter und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Verwenden von einzelnen

Die `Single` Methode übergeben kann die `Where` Bedingung statt der `Where` Methode getrennt:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21,28-29)]

Die vorangehenden `Single` Ansatz bietet keine Vorteile gegenüber `Where`. Einige Entwickler bevorzugen das `Single` Ansatz Stil.

## <a name="explicit-loading"></a>Explizites Laden

Der aktuelle Code gibt unverzüglichem Laden für `Enrollments` und `Students`:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Angenommen, Benutzer selten Registrierungen in einen Kurs anzeigen möchten. In diesem Fall wäre eine Optimierung nur die Registrierung Daten zu laden, wenn diese angefordert wird. In diesem Abschnitt die `OnGetAsync` wird aktualisiert, um das explizite Laden von verwenden `Enrollments` und `Students`.

Update der `OnGetAsync` durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Der vorangehende Code löscht die *ThenInclude* Methodenaufrufe für Registrierung und Student-Daten. Wenn ein Kurs ausgewählt ist, ruft der hervorgehobene Code ab:

* Die `Enrollment` Entitäten für den ausgewählten Kurs.
* Die `Student` Entitäten für jede `Enrollment`.

Beachten Sie das vorherige Codebeispiel kommentiert `.AsNoTracking()`. Navigationseigenschaften können nur für überwachte Entitäten explizit geladen werden.

Testen der app an. Aus Benutzersicht mit dem Verhalten der app auf die vorherige Version.

Im nächste Lernprogramm zeigt, wie verknüpfte Daten zu aktualisieren.

>[!div class="step-by-step"]
>[Zurück](xref:data/ef-rp/complex-data-model)
>[Weiter](xref:data/ef-rp/update-related-data)
