---
title: "ASP.NET Core MVC mit EF-Kern - Update bezogene Daten per Push – 7 von 10"
author: tdykstra
description: "In diesem Lernprogramm werden Sie verknüpfte Daten aktualisieren, durch die Aktualisierung von foreign Key-Felder und Navigationseigenschaften."
manager: wpickett
ms.author: tdykstra
ms.date: 03/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 4085ca9340291f6ab594285360f3b65738699098
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="updating-related-data---ef-core-with-aspnet-core-mvc-tutorial-7-of-10"></a>Aktualisieren von verknüpften Daten - EF-Core mit ASP.NET Core MVC-Lernprogramm (7 von 10)

Durch [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Die Contoso-University Beispielwebanwendung veranschaulicht, wie ASP.NET Core MVC-Webanwendungen, die mit Entity Framework Core und Visual Studio. Informationen über die Reihe von Lernprogrammen finden Sie unter [im ersten Lernprogramm, in der Reihe](intro.md).

Im vorherigen Lernprogramm angezeigten Sie verwandte Daten; In diesem Lernprogramm werden Sie verknüpfte Daten aktualisieren, durch die Aktualisierung von foreign Key-Felder und Navigationseigenschaften.

Die folgenden Abbildungen zeigen einige Seiten, denen Sie verwenden müssen.

![Kurs bearbeiten (Seite)](update-related-data/_static/course-edit.png)

![Instructor bearbeiten (Seite)](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>Das Erstellen und von Bearbeitungsseiten für Kurse anpassen

Wenn eine neue Kurs-Entität erstellt wird, muss sie eine Beziehung zu einer vorhandenen Abteilung verfügen. Um dies zu ermöglichen, enthält der scaffolded Code Controllermethoden und erstellen und Bearbeiten von Ansichten, die eine Dropdown-Liste für die Auswahl der Abteilung enthalten. Im Dropdown-Liste wird die `Course.DepartmentID` Fremdschlüsseleigenschaft, und das ist alles Entity Framework zu benötigt, um das Laden der `Department` Navigationseigenschaft mit der entsprechenden Abteilung-Entität. Sie müssen den scaffolded Code, aber ändern, damit Sie etwas hinzufügen Fehlerbehandlung und sortieren Sie die Dropdown-Liste.

In *CoursesController.cs*, löschen Sie die vier erstellen und Bearbeiten von Methoden, und Ersetzen Sie sie durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Nach der `Edit` HttpPost-Methode erstellen Sie eine neue Methode, die Abteilung Informationen für die Dropdown-Liste geladen.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Die `PopulateDepartmentsDropDownList` Methode ruft eine Liste aller Abteilungen, sortiert nach dem Namen ab, erstellt eine `SelectList` Auflistung eine Dropdown-Liste, und übergibt die Auflistung an der Sicht `ViewBag`. Akzeptiert die Methode den optionalen `selectedDepartment` Parameter, der den Aufrufcode das Element an, die beim Rendern der Dropdown Liste ausgewählt werden kann. Die Ansicht übergeben den Namen "DepartmentID" die `<select>` Tag-Hilfsobjekt und das Hilfsprogramm zum Suchen in weiß der `ViewBag` -Objekt für eine `SelectList` mit dem Namen "DepartmentID".

Die HttpGet `Create` Methodenaufrufe der `PopulateDepartmentsDropDownList` Methode ohne das ausgewählte Element festlegen, da für einen neuen Kurs noch die Abteilung eingerichtet ist nicht:

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Die HttpGet `Edit` -Methode legt das ausgewählte Element, das anhand der ID der Abteilung, die den zu bearbeitenden Kurs bereits zugewiesen ist:

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Für beide Methoden HttpPost `Create` und `Edit` auch Code enthalten, der das ausgewählte Element festgelegt werden soll, wenn sie die Seite nach einem Fehler erneut. Dadurch wird sichergestellt, dass wenn die Seite erneut angezeigt wird, um die Fehlermeldung anzuzeigen, die Abteilung ausgewählt wurde ausgewählten bleibt.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Fügen Sie hinzu. AsNoTracking zu den Details und Löschmethoden

Zum Optimieren der Leistung der Kursdetails und Delete-Seiten hinzufügen `AsNoTracking` ruft in der `Details` und HttpGet `Delete` Methoden.

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Ändern Sie die Kurs-Ansichten

In *Views/Courses/Create.cshtml*, fügen Sie eine Option "Select-Abteilung", um die **Abteilung** Dropdown-Liste, ändern Sie die Beschriftung von **"DepartmentID"** auf  **Abteilung**, und fügen Sie eine validierungsmeldung angezeigt.

[!code-html[Main](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

In *Views/Courses/Edit.cshtml*, stellen Sie die gleiche Änderung für das Feld für die Abteilung, die Sie soeben in *Create.cshtml*.

Ebenfalls im *Views/Courses/Edit.cshtml*, ein Zahlenfeld "Kurs" vor dem Hinzufügen der **Titel** Feld. Da die Kurs-Anzahl der Primärschlüssel ist, wird angezeigt, jedoch nicht geändert werden kann.

[!code-html[Main](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Es ist bereits ein ausgeblendetes Feld (`<input type="hidden">`) für die Kursnummer in der Bearbeitungsansicht. Hinzufügen einer `<label>` Tag Hilfsprogramm nicht entfällt der Bedarf für das ausgeblendete Feld, da sie nicht dazu, dass die Anzahl der Kurs in die bereitgestellten Daten aufgenommen werden, wenn der Benutzer klickt **speichern** auf die **bearbeiten** Seite.

In *Views/Courses/Delete.cshtml*, fügen Sie oben ein Zahlenfeld Kurs hinzu und Department ID, Name der Abteilung zu ändern.

[!code-html[Main](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

In *Views/Courses/Details.cshtml*, stellen Sie die gleiche Änderung, die Sie soeben für *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Testen Sie die Kurs-Seiten

Die app auszuführen, wählen Sie die **Kurse** auf **neu erstellen**, und geben Sie Daten für einen neuen Kurs:

![Seite "Kurs erstellen"](update-related-data/_static/course-create.png)

Klicken Sie auf **Erstellen**. Die Kurse Indexseite wird mit den neuen Kurs zur Liste hinzugefügt angezeigt. Der Abteilungsname in der Liste der Index-Seite stammt die Navigationseigenschaft, die anzeigt, dass die Beziehung ordnungsgemäß eingerichtet wurde.

Klicken Sie auf **bearbeiten** für eine Vorgehensweise in der Courses-Indexseite.

![Kurs bearbeiten (Seite)](update-related-data/_static/course-edit.png)

Ändern von Daten auf der Seite, und klicken Sie auf **speichern**. Die Kurse Indexseite wird mit den aktualisierten Kursdaten angezeigt.

## <a name="add-an-edit-page-for-instructors"></a>Fügen Sie eine Bearbeitungsseite für Dozenten

Beim Bearbeiten eines Instructor-Datensatzes möchten Sie den Dozenten bürozuweisung aktualisieren können. Die Instructor-Entität verfügt über eine auf 0 (null) oder eine 1-Beziehung mit der Entität OfficeAssignment, was, dass der Code hat bedeutet, behandeln die folgenden Situationen:

* Wenn der Benutzer die Office-Zuweisung löscht, und er ursprünglich einen Wert hatte, löschen Sie die OfficeAssignment-Entität.

* Erstellen Sie eine neue OfficeAssignment Entität aus, wenn der Benutzer eine Office-Zuweisungswert gibt und er ursprünglich leer war.

* Wenn der Benutzer den Wert einer Zuweisung von Office ändert, ändern Sie den Wert in einer vorhandenen OfficeAssignment-Entität.

### <a name="update-the-instructors-controller"></a>Aktualisieren Sie den Controller für Dozenten

In *InstructorsController.cs*, ändern Sie den Code in die HttpGet `Edit` Methode, sodass die It der Instructor-Entität lädt `OfficeAssignment` Navigationseigenschaft und Aufrufe `AsNoTracking`:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

Ersetzen Sie die HttpPost `Edit` Methode durch den folgenden Code aus, um Updates für Office-Zuweisung zu behandeln:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Der Code führt Folgendes aus:

-  Ändert den Namen der Methode, um `EditPost` , da die Signatur jetzt die HttpGet ist `Edit` Methode (die `ActionName` Attribut gibt an, dass die `/Edit/` URL wird weiterhin verwendet).

-  Ruft die aktuelle Instructor-Entität aus der Datenbank-eager loading für die `OfficeAssignment` Navigationseigenschaft. Dies ist identisch mit was Sie in der HttpGet haben `Edit` Methode.

-  Die abgerufene Instructor-Entität aktualisiert mit Werten aus den Modellbinder. Die `TryUpdateModel` Überladung können Sie auf die Whitelist die Eigenschaften, die Sie einschließen möchten. Dies verhindert die übermäßige Buchung wie beschrieben in der [zweite Lernprogramm](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```
    
-   Wenn der Niederlassung leer ist, legt die Instructor.OfficeAssignment-Eigenschaft auf null fest, damit die zugehörige Zeile in der Tabelle OfficeAssignment gelöscht werden.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- Speichert die Änderungen in der Datenbank an.

### <a name="update-the-instructor-edit-view"></a>Aktualisieren Sie die Ansicht Dozenten bearbeiten

In *Views/Instructors/Edit.cshtml*, Hinzufügen eines neuen Felds für die Bearbeitung der Bürostandort am Ende vor der **speichern** Schaltfläche:

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Die app auszuführen, wählen Sie die **Lehrkräfte** Registerkarte, und klicken Sie dann auf **bearbeiten** auf einen Kursleiter. Ändern der **Filiale** , und klicken Sie auf **speichern**.

![Instructor bearbeiten (Seite)](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Hinzufügen von Kurs Zuweisungen auf der Seite "Dozenten bearbeiten"

Lehrkräfte können eine beliebige Anzahl von Kurse werden folgende Themen behandelt. Jetzt müssen Sie die Seite Dozenten bearbeiten verbessern, durch Hinzufügen der Funktion zum Ändern der Kurs-Zuordnung, die eine Gruppe von Kontrollkästchen, wie im folgenden Screenshot gezeigt:

![Instructor-Bearbeitungsseite mit Kurse](update-related-data/_static/instructor-edit-courses.png)

Die Beziehung zwischen den Entitäten Kurs und Dozenten ist m: n. Zum Hinzufügen und Entfernen von Beziehungen, hinzufügen und Entfernen von Entitäten in und aus der CourseAssignments Join-Entitätenmenge.

Die Benutzeroberfläche, die Sie ändern, welche Kurse ermöglicht ein Kursleiter ist ist zugewiesen eine Gruppe von Kontrollkästchen. Ein Kontrollkästchen für jede Vorgehensweise in der Datenbank angezeigt wird, und diejenigen Argumente, die der Dozenten derzeit zugewiesen ist, werden ausgewählt. Der Benutzer kann aktivieren oder deaktivieren Kontrollkästchen, um den Kurs Zuweisungen zu ändern. Wenn die Anzahl der Kurse viel größer sind, Sie möchten wahrscheinlich verwenden Sie eine andere Methode zur Darstellung der Daten in der Ansicht von, aber verwenden Sie die gleiche Methode zum Bearbeiten einer Joinentität erstellen oder Löschen von Beziehungen.

### <a name="update-the-instructors-controller"></a>Aktualisieren Sie den Controller für Dozenten

Um Daten zur Ansicht für die Liste von Kontrollkästchen bereitzustellen, verwenden Sie eine Modellklasse anzeigen.

Erstellen Sie *AssignedCourseData.cs* in der *SchoolViewModels* Ordner und Ersetzen Sie den vorhandenen Code durch folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

In *InstructorsController.cs*, ersetzen Sie die HttpGet `Edit` -Methode durch folgenden Code. Die Änderungen werden hervorgehoben.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Der Code fügt unverzüglichem Laden für die `Courses` Navigationseigenschaft und ruft die neue `PopulateAssignedCourseData` -Methode zum Bereitstellen von Informationen für das Kontrollkästchen Array mithilfe der `AssignedCourseData` Modellklasse anzeigen.

Der Code in der `PopulateAssignedCourseData` Methode liest über alle Kurs Entitäten auf, um eine Liste der Kurse mit Ansichtsklasse Modell zu laden. Für jeden Kurs im Code wird überprüft, ob der Kurs in des Dozenten befindet `Courses` Navigationseigenschaft. Um effiziente Suche nach erstellen, bei der Überprüfung, ob ein Kurs zu den Dozenten zugewiesen wird, den Dozenten zugewiesene Kurse abgelegt sind eine `HashSet` Auflistung. Die `Assigned` -Eigenschaftensatz auf "true" für Kurse Kursleiter zugewiesen ist. Die Ansicht wird diese Eigenschaft verwenden, um zu bestimmen, welche Felder als angezeigt werden, müssen aktiviert. Schließlich wird die Liste auf die Ansicht übergeben `ViewData`.

Als Nächstes fügen Sie den Code, der ausgeführt wird, wenn der Benutzer klickt **speichern**. Ersetzen Sie die `EditPost` -Methode durch folgenden code, und fügen Sie eine neue Methode, die aktualisiert die `Courses` Navigationseigenschaft die Instructor-Entität.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Die Methodensignatur unterscheidet sich jetzt von der HttpGet `Edit` Methode, sodass der Name der Methode von ändert `EditPost` an `Edit`.

Da die Sicht eine Auflistung von Entitäten Kurs besitzt, kann nicht automatisch der Modellbinder aktualisiert die `CourseAssignments` Navigationseigenschaft. Verwenden Sie statt des Modellbinders zum Aktualisieren der `CourseAssignments` Navigationseigenschaft, Sie dies vornehmen, in der neuen `UpdateInstructorCourses` Methode. Daher müssen Sie zum Ausschließen der `CourseAssignments` Eigenschaft von der modellbindung. Dies erfordert jede Änderung an der aufrufende Code `TryUpdateModel` , da Sie die Überladung Whitelisting verwenden und `CourseAssignments` befindet sich nicht in der Include-Liste.

Wenn keine Überprüfung aktiviert wurden, den Code in `UpdateInstructorCourses` initialisiert die `CourseAssignments` Navigationseigenschaft mit eine leere Auflistung und gibt:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Der Code durchläuft alle Kurse in der Datenbank und jeder Kurs gegen diejenigen zugewiesener den Dozenten im Vergleich zu den Vorgängen, die in der Ansicht ausgewählt wurden überprüft. Um effiziente Suchvorgänge zu erleichtern, werden die letzten beiden Auflistungen in gespeichert `HashSet` Objekte.

Wenn das Kontrollkästchen für einen Kurs ausgewählt wurde, aber der Kurs befindet sich nicht in der `Instructor.CourseAssignments` Navigationseigenschaft, die Auflistung in der Navigationseigenschaft des Betriebs hinzugefügt wird.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Wenn das Kontrollkästchen für einen Kurs wurde nicht aktiviert, aber die Vorgehensweise in ist der `Instructor.CourseAssignments` Navigationseigenschaft, des Betriebs aus der Navigationseigenschaft entfernt wird.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Aktualisieren Sie die Instructor-Ansichten

In *Views/Instructors/Edit.cshtml*, Hinzufügen einer **Kurse** Feld mit einem Array von Kontrollkästchen durch das Hinzufügen der folgenden code unmittelbar nach der `div` Elemente für die **Office**  Feld und vor der `div` -Element für die **speichern** Schaltfläche.

<a id="notepad"></a>
> [!NOTE] 
> Wenn Sie den Code in Visual Studio einfügen, werden Zeilenumbrüche auf eine Weise geändert werden, die der Code unterbrochen wird.  Drücken Sie einmal STRG + Z, um die automatische Formatierung rückgängig zu machen.  Die Zeilenumbrüche wird korrigiert werden, damit sie sehen, wie Sie hier sehen. Der Einzug keinen perfekt, aber die `@</tr><tr>`, `@:<td>`, `@:</td>`, und `@:</tr>` Zeilen müssen jeweils in einer einzelnen Zeile dargestellten oder erhalten Sie einen Laufzeitfehler. Drücken Sie mit dem Block von neuem Code ausgewählt ist Tab dreimal an den neuen Code mit dem vorhandenen Code. Sie können den Status des Problems überprüfen [hier](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

[!code-html[Main](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Dieser Code erstellt eine HTML-Tabelle, die drei Spalten aufweist. In jeder Spalte wird ein Kontrollkästchen, gefolgt von einem Titel, der den Titel und Kursnummer besteht. Die Kontrollkästchen, die alle haben den gleichen Namen ("SelectedCourses"), der dem Modellbinder darüber informiert, dass sie sind als eine Gruppe behandelt werden soll. Das Value-Attribut der einzelnen Kontrollkästchen wird festgelegt, auf den Wert des `CourseID`. Wenn die Seite zurückgesendet wird, übergibt der Modellbinder ein Array mit dem Controller an, aus denen besteht die `CourseID` Werte für nur die Kontrollkästchen ausgewählt sind.

Die Kontrollkästchen gerendert werden, können solche, die für Kurse zu den Dozenten zugewiesen werden Attribute, die ausgewählt werden (angezeigt werden, wenn sie aktiviert), überprüft.

Die app auszuführen, wählen Sie die **Lehrkräfte** Registerkarte, und klicken Sie auf **bearbeiten** auf einen Kursleiter finden Sie unter der **bearbeiten** Seite.

![Instructor-Bearbeitungsseite mit Kurse](update-related-data/_static/instructor-edit-courses.png)

Ändern Sie einige Kurs Zuweisungen, und klicken Sie auf Speichern. Die Änderungen, die Sie vornehmen, werden auf der Seite "Index" angezeigt.

> [!NOTE] 
> Der Ansatz hier Dozenten Kurs Data funktioniert gut, wenn eine begrenzte Anzahl von Kurse zu bearbeiten. Bei Auflistungen, die viel größer sind, wäre eine andere Benutzeroberfläche und eine andere Methode für die Aktualisierung erforderlich.

## <a name="update-the-delete-page"></a>Aktualisieren Sie die Seite "löschen"

In *InstructorsController.cs*, löschen Sie die `DeleteConfirmed` -Methode und einfügen, die im folgenden an seiner Stelle Codebeispiel.

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Dieser Code macht die folgenden Änderungen:

* Tut Eager loading für die `CourseAssignments` Navigationseigenschaft.  Sie dazu gehören oder EF erfahren Sie nicht zu verwandten `CourseAssignment` Entitäten und wird nicht gelöscht werden.  Um zu vermeiden, diese zu lesen konnte hier kaskadierte Löschung in der Datenbank konfiguriert werden.

* Die Instructor gelöscht werden soll als Administrator, der alle Abteilungen zugewiesen wird, entfernt die Instructor-Zuweisung von die Abteilungen.

## <a name="add-office-location-and-courses-to-the-create-page"></a>Hinzufügen von Office-Speicherort und Kurse auf der Seite "erstellen"

In *InstructorsController.cs*, löschen Sie die HttpGet und HttpPost `Create` Methoden, und fügen Sie dann den folgenden Code an ihrer Stelle:

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Dieser Code ähnelt der für gesehen haben die `Edit` Methoden außer, dass zunächst keine Kurse ausgewählt sind. Die HttpGet `Create` Methodenaufrufe der `PopulateAssignedCourseData` Methode nicht, da aber in ausgewählte Kurse möglicherweise sortieren, geben Sie eine leere Auflistung für die `foreach` Schleife in der Ansicht (andernfalls den Code anzeigen Auslösung ausgeben würde ein null-Verweis).

Die HttpPost `Create` Methode fügt jeder ausgewählten Kurs zu den `CourseAssignments` Navigationseigenschaft, die vor dem Fehler bei der Validierung überprüft und der Datenbank den neuen Kursleiter hinzugefügt. Kurse werden hinzugefügt, auch wenn Modellfehler, damit beim Modellfehler (beispielsweise ein Benutzer ein ungültiges Datum sortiert) vorliegen, und die Seite wird mit einer Fehlermeldung erneut, Kurs Angaben, die vorgenommen wurden automatisch wiederhergestellt werden.

Beachten Sie, dass damit Kurse zum Hinzufügen der `CourseAssignments` Navigationseigenschaft Sie die Eigenschaft als eine leere Auflistung zu initialisieren müssen:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Als Alternative zur controllercode Dadurch konnte Sie dazu im Modell Dozenten ändern den Eigenschaftengetter, um automatisch den Sammlungssatz zu erstellen, wenn er nicht vorhanden ist, wie im folgenden Beispiel gezeigt:

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Wenn Sie ändern die `CourseAssignments` Eigenschaft auf diese Weise können Sie explizite Eigenschaft Initialisierungscode im Controller entfernen.

In *Views/Instructor/Create.cshtml*, fügen Sie ein Office-Speicherort-Textfeld hinzu und überprüfen Sie die Kontrollkästchen für Kurse, bevor Sie die Schaltfläche "Absenden". Im Fall der bearbeiten (Seite), [zu beheben, die Formatierung, wenn Visual Studio den Code neu formatiert, wenn Sie es einfügen](#notepad).

[!code-html[Main](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Durch Ausführen der app, und erstellen einen Kursleiter testen. 

## <a name="handling-transactions"></a>Behandeln von Transaktionen

Wie in beschrieben die [CRUD-Lernprogramm](crud.md), das Entity Framework implizit Transaktionen implementiert. Für Szenarien, in denen mehr, – beispielsweise gesteuert müssen wenn Vorgängen, die außerhalb von Entity Framework in einer Transaktion--enthalten sein sollen, finden Sie unter [Transaktionen](https://docs.microsoft.com/ef/core/saving/transactions).

## <a name="summary"></a>Zusammenfassung

Sie haben jetzt die Einführung in die Arbeit mit verknüpften Daten abgeschlossen. In den nächsten Lernprogrammen sehen Sie, wie Parallelitätskonflikte behandelt werden.

>[!div class="step-by-step"]
[Zurück](read-related-data.md)
[Weiter](concurrency.md)  
