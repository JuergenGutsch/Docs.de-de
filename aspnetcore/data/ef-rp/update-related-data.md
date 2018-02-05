---
title: "Razor-Pages mit EF-Core - Aktualisieren von verknüpften Daten - 7, 8"
author: rick-anderson
description: "In diesem Lernprogramm werden Sie verknüpfte Daten aktualisieren, durch die Aktualisierung von foreign Key-Felder und Navigationseigenschaften."
manager: wpickett
ms.author: riande
ms.date: 11/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 5c91c91ab938f3aa4abc55049c54f399469f6163
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="updating-related-data---ef-core-razor-pages-7-of-8"></a>Aktualisieren von verknüpften Daten - EF Core Razor-Pages (7 von 8)

Durch [Tom Dykstra](https://github.com/tdykstra), und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Dieses Lernprogramm veranschaulicht aktualisieren verknüpfte Daten. Wenn Probleme können nicht zu lösen auftreten, laden Sie die [abgeschlossene Anwendung für diese Stufe](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part7).

In der folgenden Abbildung werden einige der abgeschlossenen Seiten gezeigt.

![Bearbeiten (Seite) Kurs](update-related-data/_static/course-edit.png)
![Seite Dozenten bearbeiten](update-related-data/_static/instructor-edit-courses.png)

Überprüfen Sie und Testen Sie die Kurs-Seiten erstellen und bearbeiten. Erstellen Sie einen neuen Kurs. Abteilung wird nach dem Primärschlüssel (eine Ganzzahl), nicht seinen Namen ausgewählt. Bearbeiten Sie den neuen Kurs. Wenn Sie die Tests abgeschlossen haben, löschen Sie den neuen Kurs.

## <a name="create-a-base-class-to-share-common-code"></a>Erstellen Sie eine Basisklasse zum gemeinsamen Code nutzen

Über die Seiten Kurse/Create "und" Kurse/bearbeiten wird eine Liste der Abteilungsnamen benötigen. Erstellen der *Pages/Courses/DepartmentNamePageModel.cshtml.cs* Basisklasse für die Seiten erstellen und bearbeiten:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Der vorangehende Code erstellt ein [SelectList](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) enthält die Liste aller Abteilungsnamen. Wenn `selectedDepartment` angegeben ist, wird dieser Abteilung ausgewählt ist, der `SelectList`.

Das Erstellen und Bearbeiten von Seite Modellklassen abgeleitet `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Anpassen der Courses-Seiten

Wenn eine neue Kurs-Entität erstellt wird, muss sie eine Beziehung zu einer vorhandenen Abteilung verfügen. Zum Hinzufügen einer Abteilung beim Erstellen eines Kurs enthält die Basisklasse für erstellen und bearbeiten eine Dropdownliste zum Auswählen der Abteilung. Im Dropdown-Liste wird der `Course.DepartmentID` Fremdschlüssel (FS)-Eigenschaft. EF Kern verwendet die `Course.DepartmentID` FK beim Laden der `Department` Navigationseigenschaft.

![Kurs erstellen](update-related-data/_static/ddl.png)

Aktualisieren Sie das Modell erstellen, durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-)]

Der vorangehende Code:

* Wird von `DepartmentNamePageModel` abgeleitet.
* Verwendet `TryUpdateModelAsync` verhindern [overposting](xref:data/ef-rp/crud#overposting).
* Ersetzt `ViewData["DepartmentID"]` mit `DepartmentNameSL` (von der Basisklasse).

`ViewData["DepartmentID"]`ersetzt mit strikter typbindung `DepartmentNameSL`. Stark typisierte Modelle sind über bevorzugte schwach typisiert. Weitere Informationen finden Sie unter [schwach typisierte Daten (ViewData und ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualisieren der Seite "Kurse erstellen"

Update *Pages/Courses/Create.cshtml* durch Folgendes Markup:

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Das vorhergehende Markup nimmt folgende Änderungen:

* Ändert die Beschriftung von **"DepartmentID"** auf **Abteilung**.
* Ersetzt `"ViewBag.DepartmentID"` mit `DepartmentNameSL` (von der Basisklasse).
* Fügt die Option "Select-Abteilung". Diese Änderung wird anstelle der ersten Abteilung "Select-Abteilung" gerendert.
* Fügt eine validierungsmeldung angezeigt, wenn die Abteilung nicht ausgewählt ist.

Die Razor-Page verwendet der [wählen Sie Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Testen Sie die Seite "erstellen". Die Seite "erstellen" zeigt an, den Abteilungsnamen, anstatt die Department-ID.

### <a name="update-the-courses-edit-page"></a>Aktualisieren Sie die Seite Kurse zu bearbeiten.

Aktualisieren Sie das Modell bearbeiten, durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-)]

Die Änderungen sind ähnlich, die in das Modell erstellen. Im vorangehenden Code `PopulateDepartmentsDropDownList` übergibt die Department-ID, die die Abteilung, angegeben in der Dropdown-Liste auswählen.

Update *Pages/Courses/Edit.cshtml* durch Folgendes Markup:

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Das vorhergehende Markup nimmt folgende Änderungen:

* Zeigt die Kurs-ID an. Der primäre Schlüssel (PK) einer Entität werden im Allgemeinen nicht angezeigt. PKs sind in der Regel für Benutzer ohne Bedeutung. In diesem Fall ist der Primärschlüssel der Kurs Anzahl.
* Ändert die Beschriftung von **"DepartmentID"** auf **Abteilung**.
* Ersetzt `"ViewBag.DepartmentID"` mit `DepartmentNameSL` (von der Basisklasse).
* Fügt die Option "Select-Abteilung". Diese Änderung wird anstelle der ersten Abteilung "Select-Abteilung" gerendert.
* Fügt eine validierungsmeldung angezeigt, wenn die Abteilung nicht ausgewählt ist.

Die Seite enthält ein ausgeblendetes Feld (`<input type="hidden">`) für die Kurs-Anzahl. Hinzufügen einer `<label>` tag Hilfsprogramm mit `asp-for="Course.CourseID"` nicht das ausgeblendete Feld überflüssig. `<input type="hidden">`ist erforderlich, damit die Anzahl der Kurs in die bereitgestellten Daten aufgenommen werden, wenn der Benutzer klickt **speichern**.

Testen des aktualisierten Codes. Erstellen, bearbeiten und einen Kurs zu löschen.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>AsNoTracking zu den Details hinzufügen und Löschen von Modellen Seite

[AsNoTracking](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) kann die Leistung verbessern, wenn keine Überwachung erforderlich ist. Hinzufügen `AsNoTracking` zum Seitenmodell löschen und Details. Der folgende Code zeigt die aktualisierte Seitenmodell löschen:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Update der `OnGetAsync` Methode in der *Pages/Courses/Details.cshtml.cs* Datei:

[!code-csharp[Main](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Ändern Sie die Seiten löschen und Details

Aktualisieren Sie das Löschen von Razor-Page durch Folgendes Markup:

[!code-cshtml[Main](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Nehmen Sie die gleichen Änderungen an der Seite "Details" ein.

### <a name="test-the-course-pages"></a>Testen Sie die Kurs-Seiten

Test erstellen, bearbeiten, Details, und löschen.

## <a name="update-the-instructor-pages"></a>Aktualisieren Sie die Instructor-Seiten

In den folgenden Abschnitten aktualisieren die Instructor-Seiten.

### <a name="add-office-location"></a>Hinzufügen von Office-Speicherort

Wenn Sie einen Instructor-Eintrag bearbeiten zu können, empfiehlt es sich, den Dozenten bürozuweisung zu aktualisieren. Die `Instructor` Entität verfügt über eine 1: 0 (null)-oder-1-Beziehung mit der `OfficeAssignment` Entität. Die Instructor-Code zu behandeln.

* Wenn der Benutzer die Office-Zuweisung behoben wird, löschen Sie die `OfficeAssignment` Entität.
* Wenn der Benutzer eine bürozuweisung gibt, und es leer war, erstellen Sie ein neues `OfficeAssignment` Entität.
* Wenn der Benutzer die Zuweisung von Office ändert, Aktualisieren der `OfficeAssignment` Entität.

Aktualisieren Sie das Modell bearbeiten Lehrkräfte durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-)]

Der vorangehende Code:

- Ruft die aktuellen `Instructor` Entität aus der Datenbank mit unverzüglichem Laden für die `OfficeAssignment` Navigationseigenschaft.
- Aktualisiert die abgerufenen `Instructor` Entität mit Werten aus den Modellbinder. `TryUpdateModel`verhindert, dass [overposting](xref:data/ef-rp/crud#overposting).
- Wenn der Niederlassung leer ist, legt `Instructor.OfficeAssignment` auf Null. Wenn `Instructor.OfficeAssignment` null ist, ist die zugehörige Zeile in der `OfficeAssignment` Tabelle gelöscht wird.

### <a name="update-the-instructor-edit-page"></a>Aktualisieren Sie die Instructor bearbeiten (Seite)

Update *Pages/Instructors/Edit.cshtml* mit den Office-Speicherort:

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Stellen Sie sicher, dass Sie einem Bürostandort Lehrkräfte ändern können.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Die Instructor-Bearbeitungsseite Kursaufgaben hinzufügen

Lehrkräfte können eine beliebige Anzahl von Kurse werden folgende Themen behandelt. In diesem Abschnitt fügen Sie die Möglichkeit zum Ändern der Kurs Zuordnung hinzu. Die folgende Abbildung zeigt den aktualisierten Dozenten bearbeiten (Seite):

![Instructor-Bearbeitungsseite mit Kurse](update-related-data/_static/instructor-edit-courses.png)

`Course`und `Instructor` weist eine m: n-Beziehung. Zum Hinzufügen und Entfernen von Beziehungen, hinzufügen und Entfernen von Entitäten aus der `CourseAssignments` Entitätenmenge verknüpfen.

Kontrollkästchen aktivieren, Änderungen an der Kurse, denen ein Kursleiter zugewiesen ist. Ein Kontrollkästchen wird für jede Vorgehensweise in der Datenbank angezeigt. Kurse, denen der Dozenten zugewiesen ist, werden überprüft. Der Benutzer kann aktivieren oder deaktivieren Kontrollkästchen, um den Kurs Zuweisungen zu ändern. Wenn die Anzahl der Kurse viel größer sind:

* Wahrscheinlich verwenden Sie eine andere Benutzeroberfläche, um die Kurse anzuzeigen.
* Die Methode zum Bearbeiten einer Joinentität zum Erstellen oder Löschen von Beziehungen würde nicht ändern.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Hinzufügen von Klassen zur Unterstützung Instructor-Seiten erstellen und bearbeiten

Erstellen Sie *SchoolViewModels/AssignedCourseData.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Die `AssignedCourseData` Klasse enthält Daten, um die Kontrollkästchen für zugewiesenen Kurse durch einen Kursleiter zu erstellen.

Erstellen der *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* Basisklasse:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

Die `InstructorCoursesPageModel` ist die Basisklasse, die Sie für die Bearbeitung und Seite Modelle erstellen. `PopulateAssignedCourseData`Liest alle `Course` zum Auffüllen Entitäten `AssignedCourseDataList`. Für jeden Kurs der Code legt die `CourseID`, Titel und ob der Kurs Kursleiter zugewiesen wird. Ein [HashSet](https://docs.microsoft.com/dotnet/api/system.collections.generic.hashset-1) dient zum effizienten Suchvorgänge zu erstellen.

### <a name="instructors-edit-page-model"></a>Seitenmodell für Lehrkräfte bearbeiten

Aktualisieren Sie das Modell bearbeiten Dozenten durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-)]

Der vorangehende Code verarbeitet Änderungen an der sicherheitsbereichszuweisung Office.

Aktualisieren Sie den Dozenten Razor-Ansicht:

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Wenn Sie den Code in Visual Studio einfügen, werden Zeilenumbrüche in einer Weise geändert, die der Code unterbrochen wird. Drücken Sie einmal STRG + Z, um die automatische Formatierung rückgängig zu machen. STRG + Z behebt die Zeilenumbrüche, damit sie sehen, wie Sie hier sehen. Der Einzug keinen perfekt, aber die `@</tr><tr>`, `@:<td>`, `@:</td>`, und `@:</tr>` Zeilen jeweils muss in einer einzelnen Zeile wie gezeigt. Drücken Sie mit dem Block von neuem Code ausgewählt ist Tab dreimal an den neuen Code mit dem vorhandenen Code. Stimmen Sie auf, oder Überprüfen Sie den Status dieses Fehlers [mit diesem Link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Der vorangehende Code erstellt eine HTML-Tabelle, die drei Spalten aufweist. Jede Spalte verfügt über ein Kontrollkästchen und eine Beschriftung, die den Titel und Kursnummer enthält. Die Kontrollkästchen, die alle haben den gleichen Namen ("SelectedCourses"). Mit dem gleichen Namen informiert den Modellbinder, um sie als eine Gruppe zu behandeln. Das Value-Attribut der einzelnen Kontrollkästchen wird festgelegt, um `CourseID`. Wenn die Seite zurückgesendet wird, übergibt der Modellbinder ein Array, aus denen besteht die `CourseID` Werte für nur die Kontrollkästchen, die ausgewählt sind.

Wenn Sie die Kontrollkästchen gerendert werden, haben Kurse zu den Dozenten zugewiesen Attribute überprüft.

Führen Sie die app, und Testen Sie die aktualisierte Lehrkräfte bearbeiten (Seite). Einige Kurs Zuweisungen zu ändern. Die Änderungen werden auf der Seite "Index" angezeigt.

Hinweis: Ansatz wird hier zum Bearbeiten von Daten für Dozenten Kurs funktioniert gut, wenn eine begrenzte Anzahl von Kurse. Für Sammlungen, die viel größer sind, werden eine andere Benutzeroberfläche und eine andere Methode für die Aktualisierung noch verwendbar und effektiver gebildet.

### <a name="update-the-instructors-create-page"></a>Aktualisieren der Lehrkräfte-Seite "erstellen"

Aktualisieren Sie das Modell erstellen Dozenten durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Der vorhergehende Code ist ähnlich wie die *Pages/Instructors/Edit.cshtml.cs* Code.

Aktualisieren Sie die Instructor-Erstellen von Razor-Page durch Folgendes Markup:

[!code-cshtml[Main](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Testen Sie die Instructor-Seite erstellen.

## <a name="update-the-delete-page"></a>Aktualisieren Sie die Seite "löschen"

Aktualisieren Sie das Modell löschen, durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-)]

Der vorangehende Code stellt die folgenden Änderungen:

* Verwendet unverzüglichem Laden für die `CourseAssignments` Navigationseigenschaft. `CourseAssignments`eingeschlossen werden müssen oder sie werden nicht gelöscht, wenn der Kursleiter gelöscht wird. Um zu vermeiden, diese zu lesen, konfigurieren Sie kaskadierte Löschung in der Datenbank.

* Die Instructor gelöscht werden soll als Administrator, der alle Abteilungen zugewiesen wird, entfernt die Instructor-Zuweisung von die Abteilungen.

>[!div class="step-by-step"]
[Zurück](xref:data/ef-rp/read-related-data)
[Weiter](xref:data/ef-rp/concurrency)
