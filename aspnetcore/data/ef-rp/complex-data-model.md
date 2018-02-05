---
title: Razor-Pages mit EF-Kern - Datenmodell - 5 8
author: rick-anderson
description: "In diesem Lernprogramm fügen Sie weitere Entitäten und Beziehungen und das Datenmodell anpassen, indem Sie Formatierung, Überprüfung und Zuordnungsregeln für die Datenbank angeben."
manager: wpickett
ms.author: riande
ms.date: 10/25/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 58bb773ba16314827da84909def05a8ef370479b
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="creating-a-complex-data-model---ef-core-with-razor-pages-tutorial-5-of-8"></a>Erstellen ein Modell mit komplexen Daten - EF-Core mit Razor-Pages Lernprogramm (5 von 8)

Durch [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

In den vorherigen Lernprogrammen arbeitet mit einer einfachen Datenmodell, das von drei Entitäten verfasst wurde. In diesem Lernprogramm:

* Weitere Entitäten und Beziehungen werden hinzugefügt.
* Das Datenmodell wird durch Angeben von Formatierung, Überprüfung und Zuordnungsregeln für die Datenbank angepasst.

Die Entitätsklassen für das vollständige Datenmodell ist in der folgenden Abbildung dargestellt:

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

Wenn Probleme können nicht zu lösen auftreten, laden Sie die [abgeschlossene Anwendung für diese Stufe](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part5-complex).

## <a name="customize-the-data-model-with-attributes"></a>Das Datenmodell mit Attributen anpassen

In diesem Abschnitt wird das Datenmodell mithilfe von Attributen angepasst.

### <a name="the-datatype-attribute"></a>Das DataType-Attribut

Die Student-Seiten derzeit zeigt an, der das Registrierungsdatum. Date-Felder anzeigen in der Regel nur das Datum und nicht die Zeit.

Update *Models/Student.cs* mit den folgenden hervorgehobenen Code:

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Die [DataType](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Attribut gibt an, einen Datentyp, der als Typ der systeminternen genauer bestimmt ist. In diesem Fall nur das Datum angezeigt werden soll, nicht das Datum und die Uhrzeit. Die [DataType-Enumeration](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) stellt für viele Datentypen, z. B. Datum, Uhrzeit, PhoneNumber, Währung, EmailAddress usw. bereit. Die `DataType` Attribut kann auch aktivieren, die app-spezifische Funktionen automatisch bereitgestellt. Zum Beispiel:

* Die `mailto:` Link wird automatisch erstellt, für die `DataType.EmailAddress`.
* Die Auswahl von Datum dient zur `DataType.Date` in den meisten Browsern.

Die `DataType` Attribut ausgibt HTML 5 `data-` (ausgesprochen als Daten Dash) Attribute, die HTML 5-Browser zu nutzen. Die `DataType` Attribute stellen keine Validierung.

`DataType.Date`das Format des Datums, das angezeigt wird, nicht angegeben werden. Standardmäßig wird die Date-Felds gemäß der Standardformate basierend auf dem Server angezeigt [CultureInfo](https://docs.microsoft.com/aspnet/core/fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Die `ApplyFormatInEditMode` Einstellung gibt an, dass die Formatierung auf die Bearbeitungsoberfläche auch angewendet werden soll. Einige Felder verwenden keine `ApplyFormatInEditMode`. Beispielsweise sollte das Währungssymbol im Allgemeinen nicht in einem Text-Bearbeitungsfeld angezeigt werden.

Die `DisplayFormat` -Attribut allein verwendet werden kann. Es wird im Allgemeinen empfiehlt sich, verwenden Sie die `DataType` -Attribut mit dem `DisplayFormat` Attribut. Die `DataType` Attribut übermittelt, die die Semantik der Daten im Gegensatz zu wie auf dem Bildschirm gerendert werden soll. Die `DataType` Attribut bietet die folgenden Vorteile, die in nicht verfügbar sind `DisplayFormat`:

* Browser kann HTML5-Funktionen aktivieren. Zeigen Sie z. B. ein Monatskalender-Steuerelement, das Gebietsschema entsprechende Währungssymbol, Links per e-Mail, die clientseitige Validierung von Benutzereingaben, usw. ein.
* Standardmäßig wird der Browser Daten im richtigen Format basierend auf dem Gebietsschema gerendert.

Weitere Informationen finden Sie unter der [ \<input >-Tag-Helper-Dokumentation](xref:mvc/views/working-with-forms#the-input-tag-helper).

Führen Sie die App aus. Navigieren Sie zu der Studenten Indexseite. Wie oft werden nicht mehr angezeigt. Jede Ansicht, verwendet die `Student` Modell zeigt das Datum ohne Uhrzeit an.

![Studenten Indexseite mit Datumsangaben ohne Zeiten](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Das StringLength-Attribut

Regeln für die Überprüfung von Daten und Überprüfungsfehlermeldungen können Attribute angegeben werden. Die [StringLength](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) Attribut gibt an, die minimalen und maximalen Länge von Zeichen, die in einem Datenfeld zulässig sind. Die `StringLength` Attribut bietet auch eine clientseitige und serverseitige Validierung. Der minimale Wert hat keine Auswirkungen auf das Datenbankschema.

Update der `Student` Modell mit den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Der vorangehende Code beschränkt die Objektnamen nicht mehr als 50 Zeichen. Die `StringLength` Attribut nicht verhindern, dass einen Benutzer Leerzeichen für einen Namen eingeben. Die [der reguläre Ausdruck](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) Attribut wird verwendet, um Einschränkungen auf die Eingabe anwenden. Beispielsweise erfordert, dass der folgende Code das erste Zeichen Großbuchstaben bestehen muss und die übrigen Zeichen ein Buchstabe sein:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Führen Sie die app ein:

* Navigieren Sie zur Seite "Students".
* Wählen Sie **neu erstellen**, und geben Sie einen Namen, die mehr als 50 Zeichen.
* Wählen Sie **erstellen**, clientseitige Validierung zeigt eine Fehlermeldung angezeigt.

![Studenten Indexseite mit Zeichenfolge LÄNGENFEHLER](complex-data-model/_static/string-length-errors.png)

In **Objekt-Explorer von SQL Server** (SSOX), öffnen Sie die Student-Tabellen-Designer durch Doppelklicken auf die **Student** Tabelle.

![Students-Tabelle in SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

Die vorhergehende Abbildung zeigt das Schema für die `Student` Tabelle. Die Namensfelder aufweisen `nvarchar(MAX)` da Migrationen auf die Datenbank nicht ausgeführt wurde. Bei der Ausführung von Migrationen weiter unten in diesem Lernprogramm werden die Namensfelder werden `nvarchar(50)`.

### <a name="the-column-attribute"></a>Das Spaltenattribut

Attribute können steuern, wie Klassen und Eigenschaften in der Datenbank zugeordnet werden. In diesem Abschnitt die `Column` Attribut wird verwendet, um den Namen der Zuordnung der `FirstMidName` -Eigenschaft auf "FirstName" in der Datenbank.

Wenn die Datenbank erstellt wird, werden Eigenschaftennamen für das Modell für Spaltennamen verwendet (außer bei der `Column` Attribut verwendet wird).

Die `Student` modellieren verwendet `FirstMidName` für den Vornamen-Feld verwendet wird, weil das Feld kann auch ein zweiter Vorname enthalten.

Update der *Student.cs* -Datei mit den folgenden hervorgehobenen Code:

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Mit der vorherigen Änderung `Student.FirstMidName` in der app ordnet die `FirstName` Spalte die `Student` Tabelle.

Das Hinzufügen der `Column` Attribut ändert sich das Modell Sichern der `SchoolContext`. Sicherung des Modells die `SchoolContext` nicht mehr mit die Datenbank überein. Wenn die Anwendung vor dem Anwenden von Migrationen ausgeführt wird, wird die folgende Ausnahme generiert:

```SQL
SqlException: Invalid column name 'FirstName'.
```
So aktualisieren Sie die Datenbank:

* Erstellen Sie das Projekt.
* Öffnen Sie ein Befehlsfenster in den Projektordner ein. Geben Sie die folgenden Befehle erstellen eine neue Migration und aktualisieren die Datenbank aus:

    ```console
    dotnet ef migrations add ColumnFirstName
    dotnet ef database update
    ```

Die `dotnet ef migrations add ColumnFirstName` Befehl wird die folgende Warnmeldung generiert:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Die Warnung wird generiert, da nun die Felder sind auf 50 Zeichen begrenzt. Wenn Sie einen Namen in der Datenbank mehr als 50 Zeichen hätte, wäre die 51 bis zum letzten Zeichen verloren gehen.

* Testen der app an.

Öffnen Sie die Student-Tabelle in SSOX:

![Students-Tabelle in SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

Vor der Migration angewendet wurde, wurden die Spalten des Typs [nvarchar(MAX)](https://docs.microsoft.com/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Die Namensspalten sind jetzt `nvarchar(50)`. Name der Spalte geändert hat `FirstMidName` auf `FirstName`.

> [!Note]
> Im folgenden Abschnitt erstellen die app auf einigen Stufen werden Compilerfehler generiert. Die Anweisungen geben, wann die app zu erstellen.

## <a name="student-entity-update"></a>Entitätsaktualisierung für Studenten

![Student-Entität](complex-data-model/_static/student-entity.png)

Update *Models/Student.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Das erforderliche Attribut

Die `Required` Attribut macht die erforderlichen Felder des Name-Eigenschaften. Die `Required` Attribut ist nicht erforderlich, für NULL-Typen wie Werttypen (`DateTime`, `int`, `double`usw..). Typen, die nicht null sein können, werden automatisch als Pflichtfelder behandelt.

Die `Required` Attribut konnten ersetzt werden, mit einem minimum Length-Parameter in der `StringLength` Attribut:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Das Attribut anzeigen

Die `Display` Attribut gibt an, dass die Beschriftung für die Textfelder werden soll, "Vorname", "Last Name", "Full Name" und "Registrierungsdatum". Die Standard-Beschriftungen hatte kein Leerzeichen, unterteilen die Wörter ein, z. B. "Lastname".

### <a name="the-fullname-calculated-property"></a>Die berechnete FullName-Eigenschaft

`FullName`ist eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch Verketten von zwei weitere Eigenschaften erstellt wird. `FullName`nicht festgelegt ist, wird es nur einen Get-Accessor verfügt. Nicht `FullName` Spalte in der Datenbank erstellt wird.

## <a name="create-the-instructor-entity"></a>Die Instructor-Entität erstellen

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

Erstellen Sie *Models/Instructor.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Beachten Sie, dass mehrere Eigenschaften in werden der `Student` und `Instructor` Entitäten. In diesem Lernprogramm Implementieren von Vererbung weiter unten in dieser Serie wird dieser Code umgestaltet, um Redundanzen zu entfernen.

Mehrere Attribute können in einer Zeile sein. Die `HireDate` Attribute wie folgt geschrieben werden konnte:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Die Navigationseigenschaften CourseAssignments und OfficeAssignment

Die `CourseAssignments` und `OfficeAssignment` Eigenschaften sind Navigationseigenschaften.

Ein Kursleiter kann eine beliebige Anzahl von Kurse werden folgende Themen behandelt, sodass `CourseAssignments` als eine Auflistung definiert ist.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Wenn eine Navigationseigenschaft über mehrere Entitäten enthält:

* Es muss ein Listentyp, in dem die Einträge hinzugefügt, gelöscht und aktualisiert werden können.

Navigation gehören:

* `ICollection<T>`
*  `List<T>`
*  `HashSet<T>`

Wenn `ICollection<T>` angegeben ist, erstellt von EF Core eine `HashSet<T>` Auflistung standardmäßig.

Die `CourseAssignment` Entität wird im Abschnitt für die m: n-Beziehungen erläutert.

Contoso University Geschäftsregeln Zustand, der ein Kursleiter höchstens eine Niederlassung aufweisen kann. Die `OfficeAssignment` Eigenschaft enthält eine einzelne `OfficeAssignment` Entität. `OfficeAssignment`ist null, wenn keine Office zugewiesen ist.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Erstellen Sie die Entität OfficeAssignment

![OfficeAssignment-Entität](complex-data-model/_static/officeassignment-entity.png)

Erstellen Sie *Models/OfficeAssignment.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Das Key-Attribut

Die `[Key]` Attribut dient zum Identifizieren einer Eigenschaft als Primärschlüssel (PS) Wenn der Eigenschaftsname etwas ist als ClassnameID oder -ID.

Es ist eine auf 0 (null) oder 1 eine Beziehung zwischen der `Instructor` und `OfficeAssignment` Entitäten. Eine bürozuweisung existiert nur in Bezug auf den Dozenten, dem sie zugewiesen ist. Die `OfficeAssignment` PK entspricht auch der Fremdschlüssel (FS) die `Instructor` Entität. EF Core nicht automatisch erkannt. `InstructorID` als der Primärschlüssel der `OfficeAssignment` da:

* `InstructorID`nicht folgen die ID oder ClassnameID-Benennungskonvention.

Aus diesem Grund die `Key` Attribut wird verwendet, um zu identifizieren `InstructorID` als der PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Standardmäßig behandelt EF Core den Schlüssel als nicht-datenbankgeneriert, da die Spalte für eine identifizierende Beziehung ist.

### <a name="the-instructor-navigation-property"></a>Die Navigationseigenschaft für Dozenten

Die `OfficeAssignment` Navigationseigenschaft für die `Instructor` Entität ist auf NULL festlegbar da:

* Referenztypen (z. B. Klassen NULL sind).
* Ein Kursleiter wurden möglicherweise nicht über eine bürozuweisung.


Die `OfficeAssignment` Entität hat eine NULL- `Instructor` Navigationseigenschaft da:

* `InstructorID`ist NULL-Werte zulässt.
* Eine bürozuweisung kann ohne einen Kursleiter vorhanden sein.

Wenn ein `Instructor` Entität besitzt einen zugehörigen `OfficeAssignment` Entität, jede Entität enthält einen Verweis auf die andere in der Navigationseigenschaft.

Die `[Required]` Attribut konnte angewendet werden, um die `Instructor` Navigationseigenschaft:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Der vorangehende Code gibt an, dass es muss eine verwandte Dozenten. Der vorhergehende Code ist nicht erforderlich, da die `InstructorID` Fremdschlüssel (also auch die PK) ist NULL-Werte zulässt.

## <a name="modify-the-course-entity"></a>Ändern Sie die Kurs-Entität

![Kurs-Entität](complex-data-model/_static/course-entity.png)

Update *Models/Course.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Die `Course` Entität verfügt über eine Eigenschaft Fremdschlüssel (FS) `DepartmentID`. `DepartmentID`verweist auf den zugehörigen `Department` Entität. Die `Course` Entität verfügt über eine `Department` Navigationseigenschaft.

EF Core erfordert eine FK-Eigenschaft für ein Datenmodell, wenn das Modell eine Navigationseigenschaft für eine verwandte Entität ist.

EF Core erstellt FKs automatisch in der Datenbank, wo sie benötigt werden. EF Core erstellt [Shadowing von Eigenschaften](https://docs.microsoft.com/ef/core/modeling/shadow-properties) für automatisch erstellte FKs. Mit der FK im Datenmodell kann Updates einfacher und effizienter vornehmen. Betrachten Sie beispielsweise ein Modell, in dem die Eigenschaft FK `DepartmentID` ist *nicht* enthalten. Wenn eine Entität Kurs abgerufen wird, um zu bearbeiten:

* Die `Department` Entität ist null, wenn sie nicht explizit geladen wird.
* Zum Aktualisieren der Entität Kurs der `Department` Entität muss zuerst abgerufen werden.

Wenn die FK-Eigenschaft `DepartmentID` enthalten ist im Datenmodell, besteht keine Notwendigkeit zum Abrufen der `Department` Entität vor einem Update.

### <a name="the-databasegenerated-attribute"></a>Das DatabaseGenerated-Attribut

Die `[DatabaseGenerated(DatabaseGeneratedOption.None)]` Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt, sondern wird von der Datenbank generiert.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Standardmäßig nimmt EF Core PK Werte von der Datenbank generiert werden. DB generiert PK Werte ist in der Regel die beste Herangehensweise. Für `Course` Entitäten, die der Benutzer gibt die PK. Z. B. eine Kurs Zahl wie z. B. eine Reihe 1000 für die Math-Abteilung, eine Reihe 2000 für die englischen Abteilung.

Die `DatabaseGenerated` Attribut kann auch zum Generieren der Standardwerte verwendet werden. Beispielsweise kann die Datenbank automatisch ein Datumsfeld so, dass das Datum aufgezeichnet wird, das eine Zeile erstellt oder aktualisiert wurde, generieren. Weitere Informationen finden Sie unter [Eigenschaften generiert](https://docs.microsoft.com/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Foreign Key- und Navigation-Eigenschaften

Der Fremdschlüssel (FS) Eigenschaften und Navigationseigenschaften in den `Course` Entität wider, die folgenden Beziehungen:

Ein Kurs wird an eine Abteilung zugewiesen, daher besteht eine `DepartmentID` FK und ein `Department` Navigationseigenschaft.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Ein Kurs kann eine beliebige Anzahl von Lernende, haben also die `Enrollments` Navigationseigenschaft ist eine Sammlung:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Ein Kurs kann von mehreren Lehrkräfte vermittelten werden daher die `CourseAssignments` Navigationseigenschaft ist eine Sammlung:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`erläutert [später](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Erstellen Sie die Entität Department

![Entität Department](complex-data-model/_static/department-entity.png)

Erstellen Sie *Models/Department.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Das Spaltenattribut

Zuvor die `Column` Attribut wurde verwendet, um die Zuordnung für die Namen von Spalten zu ändern. Im Code für die `Department` Entität, die `Column` Attribut wird verwendet, um die SQL-datentypzuordnung zu ändern. Die `Budget` Spalte wird mit den SQL Server-Money-Datentyp in der DB definiert:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Die Zuordnung ist im Allgemeinen nicht erforderlich. EF Core wählt im Allgemeinen die entsprechenden SQL Server-Datentyp, der basierend auf den CLR-Typ für die Eigenschaft. Die CLR `decimal` wird zugeordnet zu SQL Server `decimal` Typ. `Budget`ist für die Währung, und der Money-Datentyp eignet sich besser für die Währung.

### <a name="foreign-key-and-navigation-properties"></a>Foreign Key- und Navigation-Eigenschaften

Die folgenden Beziehungen entsprechen den FK und Navigation-Eigenschaften:

* Eine Abteilung möglicherweise nicht haben oder einen Administrator.
* Ein Administrator ist immer einen Kursleiter. Aus diesem Grund die `InstructorID` Eigenschaft ist als die FK, enthalten die `Instructor` Entität.

Die Navigationseigenschaft heißt `Administrator` jedoch enthält ein `Instructor` Entität:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.

Eine Abteilung möglicherweise viele Kurse, daher eine Navigationseigenschaft Kurse besteht:

```csharp
public ICollection<Course> Courses { get; set; }
```

Hinweis: EF Core können gemäß der Konvention kaskadierendes Delete für NULL--FKS. und für viele-zu-viele-Beziehungen. Löschweitergabe kann zirkuläre Cascade Delete Regeln führen. Zirkuläre kaskadierte Löschung Regeln verursacht eine Ausnahme, wenn eine Migration hinzugefügt wird.

Beispielsweise, wenn die `Department.InstructorID` Eigenschaft wurde nicht als auf NULL festlegbar definiert:

* EF Core konfiguriert eine Cascade Delete-Regel klicken, um den Dozenten zu löschen, wenn die Abteilung gelöscht wird.
* Den Dozenten löschen, wenn die Abteilung gelöscht wird, ist das beabsichtigte Verhalten nicht.

Bei Bedarf von Geschäftsregeln die `InstructorID` Eigenschaft werden keine NULL-Werte zulässt, verwenden Sie die folgenden fluent-API-Anweisung:

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

Der vorangehende Code deaktiviert kaskadierte Löschung auf der Abteilung Instructor-Beziehung.

## <a name="update-the-enrollment-entity"></a>Aktualisieren Sie die Registrierung-Entität

Ein Anmeldungsdatensatz ist für einen einen Kurs von einem Studenten ausgeführte.

![Registrierung-Entität](complex-data-model/_static/enrollment-entity.png)

Update *Models/Enrollment.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Foreign Key- und Navigation-Eigenschaften

Die FK-Eigenschaften und Navigationseigenschaften reflektieren die folgenden Beziehungen:

Ein Anmeldungsdatensatz ist für einen Kurs, es ist also eine `CourseID` FK-Eigenschaft und eine `Course` Navigationseigenschaft:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Ein Anmeldungsdatensatz ist für ein Student, es ist also eine `StudentID` FK-Eigenschaft und eine `Student` Navigationseigenschaft:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>n:n-Beziehungen

Es ist eine m: n-Beziehung zwischen der `Student` und `Course` Entitäten. Die `Enrollment` Entität als Jointabelle m: n-Funktionen *mit Nutzlast* in der Datenbank. "Mit der Nutzlast" bedeutet, dass die `Enrollment` Tabelle enthält zusätzliche Daten außer FKs für den verknüpften Tabellen (in diesem Fall wird der Primärschlüssel und `Grade`).

Die folgende Abbildung zeigt, wie diese Beziehungen in einem Entitätsdiagramm aussieht. (Dieses Diagramm generiert wurde, mithilfe von EF Power Tools für EF 6.x. Erstellen das Diagramm ist nicht Teil des Lernprogramms.)

![Student-Kurs m: n-Beziehung](complex-data-model/_static/student-course.png)

Jede Beziehungslinie hat eine 1 an einem Ende und ein Sternchen (*) am anderen Eintrag, der angibt, einer 1: n-Beziehung.

Wenn die `Enrollment` Tabelle haben nicht Grade Informationen enthalten, er dürfte nur die zwei-FKS. enthalten (`CourseID` und `StudentID`). Eine m: n-Jointabelle ohne Nutzlast ist eine reine Jointabelle (PJT) bezeichnet.

Die `Instructor` und `Course` Entitäten verfügen über eine m: n-Beziehung mit einer reinen Jointabelle.

Hinweis: EF 6.x unterstützt keine implizite Verknüpfung von Tabellen für m: n-Beziehungen, aber EF Core. Weitere Informationen finden Sie unter [m: n-Beziehungen in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Die CourseAssignment-Entität

![CourseAssignment-Entität](complex-data-model/_static/courseassignment-entity.png)

Erstellen Sie *Models/CourseAssignment.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Instructor-zu-Kurse

![Instructor-Kurse-m:M](complex-data-model/_static/courseassignment.png)

Die Instructor-Kurse-viele-zu-viele-Beziehung:

* Erfordert eine Jointabelle, die durch eine Entitätenmenge dargestellt werden muss.
* Ist eine reine Jointabelle (Tabelle ohne Nutzlast).

Es ist üblich, benennen Sie eine Joinentität `EntityName1EntityName2`. Die Instructor-Kurse-Join-Tabelle, die mithilfe des folgenden Musters ist z. B. `CourseInstructor`. Allerdings wird empfohlen, unter Verwendung eines Namens, der die Beziehung beschreibt.

Datenmodelle fangen einfache und vergrößert. No-Nutzlast Joins (PJTs) weiterentwickelt häufig um Nutzlast enthalten. Beginnen Sie mit einem aussagekräftigen Entitätsname, muss der Namen ändern, wenn die verknüpften Tabelle ändert. Im Idealfall würden die Joinentität einen eigene natürlichen (möglicherweise einzelne Wort)-Namen in der Business-Domäne haben. Beispielsweise konnte Büchern und Kunden mit einem Joinentität mit dem Namen Bewertungen verknüpft werden. Für die Instructor-Kurse-viele-zu-viele-Beziehung `CourseAssignment` ist die bevorzugte über `CourseInstructor`.

### <a name="composite-key"></a>Zusammengesetzter Schlüssel

FKs sind keine NULL-Werte zulässt. Die beiden-FKS. in `CourseAssignment` (`InstructorID` und `CourseID`) zusammen eindeutig identifizieren jede Zeile der `CourseAssignment` Tabelle. `CourseAssignment`erfordert eine dedizierte PK. Die `InstructorID` und `CourseID` Eigenschaften Funktion, wie eine zusammengesetzte PK. Die einzige Möglichkeit, geben Sie zusammengesetzte PKs EF-Kern ist mit der *fluent-API*. Im nächste Abschnitt wird gezeigt, wie so konfigurieren Sie die zusammengesetzte PK.

Des zusammengesetzten Schlüssels wird sichergestellt, dass:

* Für einen Kurs sind mehrere Zeilen zulässig.
* Für eine Instructor sind mehrere Zeilen zulässig.
* Mehrere Zeilen für die gleiche Dozenten und Kurs ist nicht zulässig.

Die `Enrollment` Joinentität definiert seine eigenen PK Duplikate dieser Art möglich sind. So verhindern Sie solche Duplikate:

* Fügen Sie einen eindeutigen Index für die Felder FK oder
* Konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel ähnelt `CourseAssignment`. Weitere Informationen finden Sie unter [Indizes](https://docs.microsoft.com/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Aktualisieren Sie den Datenbankkontext

Fügen Sie den folgenden hervorgehobenen Code hinzu *Data/SchoolContext.cs*:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Der vorangehende Code fügt das neuen Entitäten und konfiguriert die `CourseAssignment` Entität zusammengesetzten PK.

## <a name="fluent-api-alternative-to-attributes"></a>Fluent-API-Alternative zu Attributen

Die `OnModelCreating` Methode im vorangehenden code verwendet die *fluent-API* EF Kernverhalten konfigurieren. Die API wird "fluent" bezeichnet, da es häufig von einer Reihe von Methodenaufrufen zusammen in einer einzigen Anweisung Vermittlungsstellen verwendet. Die [folgenden Code](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration) ist ein Beispiel für die fluent-API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

In diesem Lernprogramm wird die fluent-API nur für DB-Zuordnung verwendet, die mit Attributen erfolgen kann. Die fluent-API kann jedoch die meisten Formatierung, Überprüfung und Zuordnungsregeln an, die mit Attributen erfolgen angeben.

Einige Attribute wie z. B. `MinimumLength` kann nicht mit der fluent-API angewendet werden. `MinimumLength`Ändern Sie nicht das Schema gilt nur für eine Validierungsregel für die minimale Länge.

Einige Entwickler bevorzugen die fluent-API ausschließlich, damit sie ihre Entitätsklassen "bereinigen" bleibt Attribute und die fluent-API können gemischt werden. Es gibt einige Konfigurationen, die nur mit der fluent-API ausgeführt werden können (das Angeben einer zusammengesetzten PK). Es gibt einige Konfigurationen, die mit Attributen nur ausgeführt werden können (`MinimumLength`). Die empfohlene Vorgehensweise für die Verwendung von fluent-API oder Attribute:

* Wählen Sie eine dieser beiden Ansätze.
* Verwenden Sie die ausgewählten Ansatz einheitlich so weit wie möglich.

Einige der Attribute in diesem Lernprogramm werden zum:

* Nur Überprüfung (z. B. `MinimumLength`).
* Nur EF-kernkonfiguration (z. B. `HasKey`).
* Überprüfung und EF-Core-Konfiguration (z. B. `[StringLength(50)]`).

Weitere Informationen zu Attributen im Vergleich zu fluent-API finden Sie unter [Methoden der Konfiguration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).

## <a name="entity-diagram-showing-relationships"></a>Anzeigen von Entitätsbeziehungen-Diagramm

Die folgende Abbildung zeigt das Diagramm, das EF-Powertools für das abgeschlossene Modell "School" zu erstellen.

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

Die vorhergehende Abbildung zeigt:

* 1: n-Beziehung Zeilenumbrüche (1 bis \*).
* Zu 0 (null) oder eins eine Beziehungslinie (1 zu 0.. 1) zwischen den `Instructor` und `OfficeAssignment` Entitäten.
* 0 (null)-oder-1-n-Beziehung (0.. 1 auf *) zwischen den `Instructor` und `Department` Entitäten.

## <a name="seed-the-db-with-test-data"></a>Ausgangswert für die Datenbank mit der Testdaten

Aktualisieren Sie den Code in *Data/DbInitializer.cs*:

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Der vorangehende Code stellt Seed-Daten für die neuen Entitäten. Die meisten dieser Code erstellt neue Entitätsobjekten und lädt Beispieldaten. Die Beispieldaten werden für Tests verwendet. Der vorangehende Code erstellt die folgenden viele-zu-viele-Beziehungen:

* `Enrollments`
* `CourseAssignment`

Hinweis: [EF Core 2.1](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap) unterstützt [datenseeding](https://github.com/aspnet/EntityFrameworkCore/issues/629).

## <a name="add-a-migration"></a>Fügen Sie eine migration

Erstellen Sie das Projekt. Öffnen Sie ein Befehlsfenster, das in den Projektordner, und geben Sie den folgenden Befehl aus:

```console
dotnet ef migrations add ComplexDataModel
```

Dieser Befehl zeigt eine Warnung bezüglich eines möglichen Datenverlusts.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Wenn die `database update` Befehl ausgeführt wird, wird die folgende Fehlermeldung erzeugt:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

Bei der Ausführung von Migrationen mit vorhandenen Daten werden möglicherweise FK-Einschränkungen, die nicht mit den vorhandenen Daten erfüllt werden. In diesem Lernprogramm wird eine neue Datenbank erstellt, daher gibt es keine FK einschränkungsverletzungen. Finden Sie unter [korrigieren und foreign Key-Einschränkungen mit Legacydaten](#fk) Anleitungen Verstößen der FK auf der aktuellen Datenbank.

## <a name="change-the-connection-string-and-update-the-db"></a>Ändern Sie die Verbindungszeichenfolge und aktualisieren die Datenbank

Der Code in der aktualisierten `DbInitializer` Seed-Daten für die neue Entitäten hinzugefügt. So erzwingen Sie EF-Core, um eine neue leere Datenbank zu erstellen:

* Ändern der Name der Datenbankverbindungszeichenfolge in *appsettings.json* auf ContosoUniversity3. Der neue Name muss ein Name sein, die auf dem Computer verwendet wurde.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
      },
    ```

* Löschen Sie alternativ die DB-verwenden:

    * **Objekt-Explorer von SQL Server** (SSOX).
    * Die `database drop` CLI-Befehl:

   ```console
   dotnet ef database drop
   ```

Führen Sie `database update` im Befehlsfenster:

```console
dotnet ef database update
```

Dieser Befehl wird bei allen Migrationen ausgeführt.

Führen Sie die App aus. Ausführen der app-Ausführung der `DbInitializer.Initialize` Methode. Die `DbInitializer.Initialize` füllt die neue DB.

Öffnen Sie die Datenbank im SSOX:

* Erweitern Sie die **Tabellen** Knoten. Die erstellten Tabellen werden angezeigt.
* Wenn zuvor SSOX geöffnet wurde, klicken Sie auf die **aktualisieren** Schaltfläche.

![Tabellen in SSOX](complex-data-model/_static/ssox-tables.png)

Überprüfen Sie die **CourseAssignment** Tabelle:

* Mit der rechten Maustaste die **CourseAssignment** Tabelle, und wählen Sie **Ansichtsdaten**.
* Überprüfen Sie die **CourseAssignment** Tabelle Daten enthält.

![CourseAssignment Daten in SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="fk"></a>

## <a name="fixing-foreign-key-constraints-with-legacy-data"></a>Beheben von foreign Key-Einschränkungen mit älteren Daten

In diesem Abschnitt ist optional.

Bei der Ausführung von Migrationen mit vorhandenen Daten werden möglicherweise FK-Einschränkungen, die nicht mit den vorhandenen Daten erfüllt werden. Mit Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren. Dieser Abschnitt enthält ein Beispiel für FK einschränkungsverletzungen beheben. Machen Sie keine dieser Änderungen am Code ohne Sicherung. Machen Sie keine dieser Code ändert, wenn Sie im vorherigen Abschnitt abgeschlossen und die Datenbank aktualisiert.

Die *{timestamp}_ComplexDataModel.cs* Datei enthält den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Der vorangehende Code Fügt einen NULL- `DepartmentID` FK auf die `Course` Tabelle. Die Datenbank aus dem vorherigen Lernprogramm enthält Zeilen in `Course`, sodass dieser Tabelle durch Migrationen aktualisiert werden kann.

Vornehmen der `ComplexDataModel` Migration mit vorhandenen Daten zu arbeiten:

* Ändern Sie den Code, um die neue Spalte zu gewähren (`DepartmentID`) einen Standardwert.
* Erstellen Sie eine gefälschte Abteilung, die mit dem Namen "Temp" als Standard-Abteilung zu agieren.

### <a name="fix-the-foreign-key-constraints"></a>Korrigieren Sie die foreign Key-Einschränkungen

Update der `ComplexDataModel` Klassen `Up` Methode:

* Öffnen der *{timestamp}_ComplexDataModel.cs* Datei.
* Kommentieren Sie die Codezeile fügt die `DepartmentID` Spalte die `Course` Tabelle.

[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Fügen Sie den folgenden hervorgehobenen Code hinzu. Der neue Code wechselt nach der `.CreateTable( name: "Department"` blockieren:[!code-csharp[Main](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Mit den vorhergehenden Änderungen, die vorhandenen `Course` Zeilen werden im Zusammenhang mit der Abteilung "Temp" nach der `ComplexDataModel` `Up` Methode ausgeführt wird.

Eine produktionsanwendung würde:

* Einschließen von Code oder Skripts hinzufügen `Department` Zeilen und verwandte `Course` Zeilen mit dem neuen `Department` Zeilen.
* Verwenden Sie nicht die Abteilung "Temp" oder der Standardwert für `Course.DepartmentID`.

Das nächste Lernprogramm behandelt die verknüpfte Daten.

>[!div class="step-by-step"]
[Zurück](xref:data/ef-rp/migrations)
[Weiter](xref:data/ef-rp/read-related-data)
