---
title: "ASP.NET Core MVC mit EF-Core - lesen verknüpften Daten - 6 von 10"
author: tdykstra
description: "In diesem Lernprogramm Sie lesen und Anzeigen von verknüpften Daten – d. h. die Daten, die das Entity Framework in Navigationseigenschaften lädt."
manager: wpickett
ms.author: tdykstra
ms.date: 03/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 778ef976fdbef70684ca26b0c7c548ffcc83ee00
ms.sourcegitcommit: e45f8912ce32b4071bf7e83b8f8315cd8bba3520
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2017
---
# <a name="reading-related-data---ef-core-with-aspnet-core-mvc-tutorial-6-of-10"></a>Lesen-bezogene Daten – EF-Core mit ASP.NET Core MVC-Lernprogramm (6 von 10)

Durch [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Die Contoso-University Beispielwebanwendung veranschaulicht, wie ASP.NET Core MVC-Webanwendungen, die mit Entity Framework Core und Visual Studio. Informationen über die Reihe von Lernprogrammen finden Sie unter [im ersten Lernprogramm, in der Reihe](intro.md).

Im vorherigen Lernprogramm abgeschlossen Sie das Datenmodell "School". In diesem Lernprogramm Sie lesen und Anzeigen von verknüpften Daten – d. h. die Daten, die das Entity Framework in Navigationseigenschaften lädt.

Die folgenden Abbildungen zeigen den Seiten, arbeiten Sie mit.

![Kurse Indexseite](read-related-data/_static/courses-index.png)

![Indexseite für Dozenten](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Eager, explizite und lazy Loading-verknüpfter Daten

Es gibt mehrere Möglichkeiten, objektrelationales Mapping (ORM)-Software, z. B. Entity Framework in die Navigationseigenschaften einer Entität verknüpfte Daten geladen werden können:

* Unverzüglichem Laden. Wenn die Entität gelesen wird, werden darin verknüpfte Daten abgerufen. Dies führt normalerweise zu einer einzelnen Join-Abfrage, die alle Daten abruft, die erforderlich ist. Sie geben unverzüglichem Laden in Entity Framework Core mit der `Include` und `ThenInclude` Methoden.

  ![Beispiel mit unverzüglichem Laden](read-related-data/_static/eager-loading.png)

  Sie können einige der Daten in eine eigene Abfrage abrufen und EF "behoben von" die Navigationseigenschaften.  D. h. fügt EF automatisch separat abgerufenen Entitäten, auf dem sie gehören, in den Navigationseigenschaften der zuvor abgerufenen Entitäten hinzu. Für die Abfrage, die verknüpfte Daten abruft, können Sie die `Load` Methode anstelle einer Methode, die eine Liste oder ein Objekt, z. B. zurückgibt `ToList` oder `Single`.

  ![Beispiel für eine eigene Abfrage](read-related-data/_static/separate-queries.png)

* Explizites Laden. Wenn die Entität zuerst gelesen wird, ist nicht verbundene Daten abgerufen. Schreiben Sie Code, der die verwandten Daten abruft, wenn es benötigt wird. Wie im Fall von Eager loading mit separaten Abfragen an die Datenbank gesendet explizite das Laden der Ergebnisse in mehreren Abfragen. Der Unterschied besteht darin, dass der Code mit expliziten Laden die Navigationseigenschaften zu ladenden gibt an. In Entity Framework Core 1.1 können Sie die `Load` Methode, um das explizite laden auszuführen. Zum Beispiel:

  ![Explizites Laden-Beispiel](read-related-data/_static/explicit-loading.png)

* Verzögertes Laden. Wenn die Entität zuerst gelesen wird, ist nicht verbundene Daten abgerufen. Allerdings werden beim ersten Versuch, auf eine Navigationseigenschaft, für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen. Eine Abfrage wird jedes Mal an die Datenbank gesendet Sie zum Abrufen von Daten von einer Navigationseigenschaft zum ersten Mal versuchen. Entity Framework Core 1.0 unterstützt nicht das verzögertes Laden.

### <a name="performance-considerations"></a>Überlegungen zur Leistung

Wenn Sie, die Sie für jede Entität abgerufen verknüpfte Daten benötigen wissen, bietet eager loading, häufig die beste Leistung, da eine einzelne Abfrage, die an die Datenbank gesendet, in der Regel effizienter als separate Abfragen für jede Entität abgerufen wird. Nehmen wir beispielsweise an, dass jede Abteilung zehn Verwandte Kurse gelten. Unverzüglichem Laden von alle verknüpften Daten führt in einer einzelnen (Join)-Abfrage und einem einzelnen Roundtrip-mit der Datenbank. Für Kurse für jede Abteilung eine eigene Abfrage führt zu elf Roundtrips zur Datenbank. Zusätzliche Roundtrips zur Datenbank sind vor allem auf die Leistung beeinträchtigen, wenn Latenz hoch ist.

Andererseits, in einigen Szenarien eine eigene Abfrage ist jedoch effizienter. Unverzüglichem Laden von alle verknüpften Daten in einer Abfrage könnte eine sehr komplexe Verknüpfung generiert werden, verursachen, die SQL Server nicht effizient verarbeiten kann. Oder Sie müssen eine Entität Navigationseigenschaften nur für einen Teil einer Reihe von Entitäten zugreifen, die Sie verarbeiten, kann eine eigene Abfrage möglicherweise besser ausgeführt werden, da unverzüglichem Laden aller Elemente im Vorfeld mehr Daten als benötigt abrufen würde. Wenn die Leistung kritisch ist, empfiehlt es sich zum Testen von Leistung beides Möglichkeiten, um die beste Wahl vornehmen zu können.

## <a name="create-a-courses-page-that-displays-department-name"></a>Erstellen Sie eine Kurse-Seite, die Name der Abteilung anzeigt

Die Kurs-Entität enthält, eine Navigationseigenschaft, die die Entität "Department" der Abteilung enthält, die der Kurs zugewiesen ist. Um den Namen der zugewiesenen Abteilung in einer Liste von Kurse anzuzeigen, müssen Sie die Name-Eigenschaft die Entität Department nicht entnommen werden, die in der `Course.Department` Navigationseigenschaft.

Erstellen Sie einen Controller mit dem Namen CoursesController für den Kurs Entitätstyp, die mit denselben Optionen für die **MVC-Controller mit Ansichten unter Verwendung von Entity Framework** Scaffolder, die Sie zuvor für den Controller Studenten wie gezeigt in der folgende Abbildung:

![Hinzufügen eines Controllers Kurse](read-related-data/_static/add-courses-controller.png)

Open *CoursesController.cs* und untersuchen Sie die `Index` Methode. Der automatische Gerüstbau angegeben unverzüglichem Laden für die `Department` Navigationseigenschaft mithilfe der `Include` Methode.

Ersetzen Sie die `Index` Methode durch den folgenden Code, der einen geeigneteren Namen für die `IQueryable` Kurs Entitäten zurückgibt (`courses` anstelle von `schoolContext`):

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Open *Views/Courses/Index.cshtml* , und Ersetzen Sie den Code durch den folgenden Code. Die Änderungen werden hervorgehoben:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Sie haben die folgenden Änderungen an der scaffolded Code vorgenommen:

* Die Überschrift von Index in Courses geändert.

* Hinzugefügt eine **Anzahl** Spalte, die zeigt die `CourseID` Eigenschaftswert. Primärschlüssel werden nicht standardmäßig Gerüstbau, da normalerweise ohne Bedeutung für Endbenutzer sind. Allerdings in diesem Fall der Primärschlüssel sinnvoll ist und Sie sie anzeigen möchten.

* Geändert die **Abteilung** Spalte der Abteilungsname angezeigt. Der Code zeigt die `Name` -Eigenschaft der Abteilung-Entität, die in geladen ist die `Department` Navigationseigenschaft:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Führen Sie die app, und wählen Sie die **Kurse** Registerkarte ", um die Liste mit den Abteilungsnamen anzuzeigen.

![Kurse Indexseite](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Erstellen Sie eine Lehrkräfte-Seite, in der Kurse und Registrierung angezeigt.

In diesem Abschnitt müssen Sie einem Controller und Ansicht für die Instructor-Entität erstellen, um die Seite Lehrkräfte anzuzeigen:

![Indexseite für Dozenten](read-related-data/_static/instructors-index.png)

Auf dieser Seite liest und zeigt verknüpfte Daten auf folgende Weise:

* Die Liste der Lehrkräfte zeigt aufeinander bezogene Daten in die OfficeAssignment-Entität. Die Instructor und OfficeAssignment Entitäten sind in einer 1: 0 (null)-oder-1-Beziehung. Verwenden Sie für die Entitäten OfficeAssignment unverzüglichem Laden. Wie zuvor erläutert, ist die unverzüglichem Laden in der Regel effizienter, wenn Sie die verknüpften Daten für alle abgerufenen Zeilen von der primären Tabelle benötigen. In diesem Fall möchten Sie Office-Zuweisungen für alle angezeigten Lehrkräfte anzuzeigen.

* Wenn der Benutzer einen Kursleiter auswählt, werden verknüpfte Kurs Entitäten angezeigt. Die Entitäten Kursleiter und Kurs befinden sich in einer m: n-Beziehung. Verwenden Sie für die Kurs-Entitäten und ihre zugehörigen Entitäten der Abteilung unverzüglichem Laden. In diesem Fall können eine eigene Abfrage effizienter sein, da Sie nur für den ausgewählten Dozenten Kurse benötigen. Dieses Beispiel zeigt jedoch mit unverzüglichem Laden für Navigationseigenschaften innerhalb von Entitäten, die selbst in den Navigationseigenschaften werden.

* Wenn der Benutzer einen Kurs auswählt, werden verknüpfte Daten aus der Entitätssammlung Registrierung angezeigt. Die Entitäten Kurs und Registrierung sind in einer 1: n-Beziehung. Sie müssen eine eigene Abfrage für Registrierung Entitäten und ihre verknüpften Student-Entitäten verwenden.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Erstellen Sie ein Ansichtsmodell für die Indexansicht Dozenten

Die Seite "Lehrkräfte" zeigt Daten aus drei verschiedenen Tabellen. Aus diesem Grund erstellen Sie ein Ansichtsmodell, das drei Eigenschaften enthält jede mit den Daten für eine der Tabellen.

In der *SchoolViewModels* Ordner erstellen *InstructorIndexData.cs* und Ersetzen Sie den vorhandenen Code durch folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Erstellen Sie die Instructor-Controller und Ansichten

Erstellen Sie einen Kursleiter-Controller mit EF Lese-/schreibaktionen wie in der folgenden Abbildung gezeigt:

![Hinzufügen eines Controllers Dozenten](read-related-data/_static/add-instructors-controller.png)

Open *InstructorsController.cs* und fügen Sie eine using-Anweisung für den Namespace ViewModels:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Ersetzen Sie die Index-Methode, durch den folgenden Code zum unverzüglichem Laden von verknüpften Daten und fügen Sie ihn in das Modell anzeigen.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Die Methode akzeptiert optionale Routendaten (`id`) und einen Abfragezeichenfolgenparameter (`courseID`), die die ID-Werte der ausgewählten Kursleiter und ausgewählten Kurs bereitstellen. Die Parameter bereitgestellt werden, indem die **wählen** links auf der Seite.

Der Code wird zuerst Erstellen einer Instanz des Modells anzeigen und darin die Liste der Lehrkräfte einfügen. Der Code gibt unverzüglichem Laden für die `Instructor.OfficeAssignment` und `Instructor.CourseAssignments` Navigationseigenschaften. Innerhalb der `CourseAssignments` -Eigenschaft, die `Course` -Eigenschaft ist geladen, und innerhalb der, die `Enrollments` und `Department` Eigenschaften werden geladen, und in jedem `Enrollment` Entität die `Student` Eigenschaft geladen wird.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Da die Sicht immer die OfficeAssignment Entität erfordert, ist es effizienter, die in derselben Abfrage abgerufen. Kurs Entitäten sind erforderlich, wenn auf der Webseite ein Kursleiter ausgewählt ist, damit eine einzelne Abfrage ist besser als mehrere Abfragen nur, wenn die Seite mit einem Kurs ausgewählt als ohne häufiger angezeigt wird.

Der Code wird wiederholt `CourseAssignments` und `Course` da Sie zwei Eigenschaften von benötigen `Course`. Die erste Zeichenfolge von `ThenInclude` ruft ruft `CourseAssignment.Course`, `Course.Enrollments`, und `Enrollment.Student`.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

An diesem Punkt im Code eine andere `ThenInclude` wäre für Navigationseigenschaften des `Student`, die Sie nicht benötigen. Aber aufrufenden `Include` beginnt über mit `Instructor` Eigenschaften, daher Sie die Kette erneut, diesen Zeitangaben durchlaufen müssen `Course.Department` anstelle von `Course.Enrollments`.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Der folgende Code ausgeführt wird, wenn ein Kursleiter ausgewählt wurde. Die ausgewählte Kursleiter wird aus der Liste der Kursleiter das Ansichtsmodell abgerufen. Des Ansichtsmodells `Courses` Eigenschaft wird dann mit den Kurs Entitäten aus dieser Dozenten geladen `CourseAssignments` Navigationseigenschaft.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

Die `Where` -Methode gibt eine Auflistung, aber in diesem Fall die Kriterien, nur einen einzigen Instructor-Entität zurückgegeben wird die Methode zu übergeben. Die `Single` -Methode konvertiert die Auflistung in eine einzelne Instructor-Entität, die Sie Zugriff auf diese Entität gewährt `CourseAssignments` Eigenschaft. Die `CourseAssignments` Eigenschaft enthält `CourseAssignment` , nur die verknüpften sollen Entitäten `Course` Entitäten.

Verwenden Sie die `Single` Methode auf eine Auflistung, wenn Sie wissen, dass die Auflistung wird nur ein Element verfügen. Die einzige Methode löst eine Ausnahme aus, wenn die übergebene Auflistung leer ist oder wenn mehr als ein Element vorhanden ist. Ist eine Alternative `SingleOrDefault`, womit einen Default-Wert (in diesem Fall null), wenn die Auflistung leer ist. Jedoch in diesem Fall, da immer noch ansonsten eine Ausnahme (aus beim Suchen nach einem `Courses` Eigenschaft auf einen null-Verweis), und die Ausnahmemeldung würde weniger deutlich die Ursache des Problems angeben. Beim Aufrufen der `Single` -Methode, Sie können auch übergeben in der Where Bedingung statt der `Where` Methode getrennt:

```csharp
.Single(i => i.ID == id.Value)
```

anstelle von:

```csharp
.Where(I => i.ID == id.Value).Single()
```

Als Nächstes wird ein Kurs ausgewählt wurde, der ausgewählte Kurs aus der Liste der Kurse in das Ansichtsmodell abgerufen. Klicken Sie dann die des Ansichtsmodells `Enrollments` Eigenschaft wird geladen, mit der Registrierung Entitäten aus dieser Kurs `Enrollments` Navigationseigenschaft.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Ändern Sie die Sicht Instructor-Index

In *Views/Instructors/Index.cshtml*, den Code durch den folgenden Code ersetzen. Die Änderungen werden hervorgehoben.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Sie haben die folgenden Änderungen an den vorhandenen Code vorgenommen:

* Die Modellklasse, um geändert `InstructorIndexData`.

* Der Seitenname von geändert **Index** auf **Lehrkräfte**.

* Hinzugefügt ein **Office** Spalte `item.OfficeAssignment.Location` nur, wenn `item.OfficeAssignment` nicht null ist. (Da dies eine 1: 0 (null)-oder-1-Beziehung ist, gibt es möglicherweise nicht verknüpfte Entität OfficeAssignment.)

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
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Einen neuen Link mit der Bezeichnung hinzugefügt **wählen** unmittelbar vor der Links von anderen in jeder Zeile, die der ausgewählten Instructor-ID zu sendende verursacht die `Index` Methode.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Führen Sie die app, und wählen Sie die **Lehrkräfte** Registerkarte. Die Seite zeigt die Location-Eigenschaft der verknüpften OfficeAssignment Entitäten und eine leere Zelle, wenn keine verknüpften OfficeAssignment Entität vorhanden ist.

![Lehrkräfte Indexseite, der keine Auswahl](read-related-data/_static/instructors-index-no-selection.png)

In der *Views/Instructors/Index.cshtml* Datei, nach die schließenden Tabelle Element (am Ende der Datei), fügen Sie folgenden Code. Dieser Code zeigt eine Liste der Kurse, die im Zusammenhang mit einen Kursleiter beim ein Kursleiter ausgewählt ist.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Dieser Code liest die `Courses` Eigenschaft des Modells anzeigen, das eine Liste der Kurse anzeigen. Sie bietet außerdem eine **wählen** Link, der die ID der ausgewählten Kurs, sendet der `Index` Aktionsmethode.

Aktualisieren Sie die Seite, und wählen Sie einen Kursleiter. Jetzt sehen Sie ein Raster mit den für den ausgewählten Kursleiter zugewiesene Kurse zeigt an, und jeder Kurs Sie finden Sie unter den Namen der zugewiesenen Abteilung.

![Lehrkräfte Index Seite Dozenten ausgewählt](read-related-data/_static/instructors-index-instructor-selected.png)

Fügen Sie nachdem der Codeblock, den Sie gerade hinzugefügt haben den folgenden Code ein. Dadurch wird eine Liste der Studenten, die registriert werden in einen Kurs, wenn dieser Kurs ausgewählt ist.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Dieser Code liest die Registrierung-Eigenschaft des Modells anzeigen, um eine Liste der Schüler in diesem Kurs registriert anzuzeigen.

Aktualisieren Sie die Seite erneut, und wählen Sie einen Kursleiter. Wählen Sie dann einen Kurs, finden in der Liste der registrierten Studenten und deren Qualitäten.

![Lehrkräfte Index Seite Kursleiter und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="explicit-loading"></a>Explizites Laden

Wenn Sie die Liste der Kursleiter abgerufen *InstructorsController.cs*, unverzüglichem Laden für die Angabe der `CourseAssignments` Navigationseigenschaft.

Angenommen Sie, Sie erwartet, dass Benutzer nur selten Bereitstellungen in einem ausgewählten Kursleiter und Kurs angezeigt werden soll. In diesem Fall möchten Sie die Registrierung Daten zu laden, nur, wenn diese angefordert wird. Um ein Beispiel dazu explizites Laden anzuzeigen, ersetzen die `Index` -Methode durch folgenden Code wird die unverzüglichem Laden für Bereitstellungen entfernt und lädt diese Eigenschaft explizit. Die codeänderungen werden hervorgehoben.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Der neue Code löscht die *ThenInclude* Methodenaufrufe für Enrollment Daten aus dem Code, die Instructor-Entitäten abruft. Wenn eine Lehrkraft- und Kurs ausgewählt sind, ruft der hervorgehobene Code Registrierung Entitäten für den ausgewählten Kurs und Student-Entitäten für jede Anmeldung ab.

Ausführen die app, wechseln Sie zu den Dozenten Indexseite jetzt und Sie keinen Unterschied in der Anzeige auf der Seite angezeigt werden, obwohl Sie geändert haben, wie die Daten abgerufen werden.

## <a name="summary"></a>Zusammenfassung

Sie haben jetzt unverzüglichem Laden mit einer Abfrage und mehrere Abfragen verwendet, um verwandte Daten in den Navigationseigenschaften gelesen. In den nächsten Lernprogrammen erfahren Sie, wie verknüpfte Daten aktualisiert werden.

>[!div class="step-by-step"]
>[Zurück](complex-data-model.md)
>[Weiter](update-related-data.md)  
