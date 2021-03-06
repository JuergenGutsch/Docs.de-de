---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-a-more-complex-data-model-for-an-asp-net-mvc-application
title: "Erstellen eine komplexere Datenmodell für eine ASP.NET MVC-Anwendung | Microsoft Docs"
author: tdykstra
description: Die Contoso-University Beispielwebanwendung veranschaulicht, wie ASP.NET MVC 5-Anwendungen, die mit dem Entity Framework 6 Code First und Visual Studio erstellen...
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/07/2014
ms.topic: article
ms.assetid: 46f7f3c9-274f-4649-811d-92222a9b27e2
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-a-more-complex-data-model-for-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9a89aa8e7dd3b2f6ac18e0b1a9c2a9d64d27189c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
<a name="creating-a-more-complex-data-model-for-an-aspnet-mvc-application"></a>Erstellen eine komplexere Datenmodell für eine ASP.NET MVC-Anwendung
====================
Durch [Tom Dykstra](https://github.com/tdykstra)

[Herunterladen des abgeschlossenen Projekts](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) oder [PDF herunterladen](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> Die Contoso-University Beispielwebanwendung veranschaulicht, wie ASP.NET MVC 5-Anwendungen, die mit dem Entity Framework 6 Code First und Visual Studio 2013. Informationen über die Reihe von Lernprogrammen finden Sie unter [im ersten Lernprogramm, in der Reihe](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


In den vorherigen Lernprogrammen, die Sie mit der ein einfaches Datenmodell, das aus drei Entitäten zusammengesetzt wurde gearbeitet haben. In diesem Lernprogramm fügen Sie weitere Entitäten und Beziehungen, und Sie müssen das Datenmodell anpassen, indem Sie Formatierung, Überprüfung und Zuordnungsregeln für die Datenbank angeben. Sehen Sie zwei Möglichkeiten zum Anpassen des Datenmodells: durch Hinzufügen von Attributen, um Entitätsklassen und durch Hinzufügen von Code zu der Datenbank Context-Klasse.

Wenn Sie fertig sind, werden die Entitätsklassen abgeschlossenen Datenmodell bilden, die in der folgenden Abbildung gezeigt wird:

![School_class_diagram](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image1.png)

## <a name="customize-the-data-model-by-using-attributes"></a>Anpassen des Datenmodells mithilfe von Attributen

In diesem Abschnitt sehen Sie zum Anpassen des Datenmodells mithilfe von Attributen, die die Formatierung, Überprüfung und Zuordnungsregeln für die Datenbank angeben. Und dann in mehreren der folgenden Abschnitte die vollständige Sie erstellen `School` Datenmodell durch Hinzufügen von Attributen auf die Klassen Sie bereits erstellte und das Erstellen von neue Klassen für die verbleibenden Entitätstypen im Modell.

### <a name="the-datatype-attribute"></a>Das DataType-Attribut

Für Student Registrierung Datumsangaben Anzeigen aller Web Pages derzeit der Zeit zusammen mit dem Datum, obwohl alle, können Sie für dieses Feld ist das Datum. Mit Datenattributen für die Anmerkung, stellen Sie eine codeänderung, die das Anzeigeformat in jeder Ansicht behoben werden, die Daten anzeigt. Sehen Sie ein Beispiel dazu, dass Sie ein Attribut zum Hinzufügen der `EnrollmentDate` Eigenschaft in der `Student` Klasse.

In *Models\Student.cs*, Hinzufügen einer `using` -Anweisung für die `System.ComponentModel.DataAnnotations` Namespace und fügen `DataType` und `DisplayFormat` -Attribute verwenden, um die `EnrollmentDate` -Eigenschaft verwenden, wie im folgenden Beispiel gezeigt:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,12-13)]

Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribut wird verwendet, um einen Datentyp angeben, die als Datenbank systeminternen Typ genauer bestimmt ist. In diesem Fall möchten wir nur zum Nachverfolgen der Datum nicht das Datum und die Uhrzeit. Die [DataType-Enumeration](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) bietet für viele Datentypen, wie z. B. *Date "," Time "," PhoneNumber "," Währung "," EmailAddress* und vieles mehr. Das `DataType`-Attribut kann der Anwendung auch ermöglichen, typspezifische Features bereitzustellen. Z. B. eine `mailto:` Link erstellt werden kann, für [DataType.EmailAddress](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx), und ein Datum Selektor kann bereitgestellt werden, für die [DataType.Date](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) in Browsern mit Unterstützung [HTML5](http://html5.org/). Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribute ausgibt HTML 5 [Data -](http://ejohn.org/blog/html-5-data-attributes/) (ausgesprochen *Daten Dash*) Attribute, die HTML 5-Browser zu bringen. Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribute bieten keine Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Feld "Daten" entsprechend der Standardformate basierend auf dem Server angezeigt [CultureInfo](https://msdn.microsoft.com/library/vstudio/system.globalization.cultureinfo(v=vs.110).aspx).

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:


[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample2.cs)]


Die `ApplyFormatInEditMode` Einstellung gibt an, dass die angegebene Formatierung auch angewendet werden soll, wenn der Wert in einem Textfeld, das für die Bearbeitung angezeigt wird. (Sie möchten nicht, die für einige Felder – z. B. für die Currency-Werte nicht empfiehlt das Währungssymbol in das Textfeld für die Bearbeitung.)

Können Sie die [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) Attribut von selbst, aber es ist im Allgemeinen empfiehlt sich, verwenden Sie die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) auch Attribut. Die `DataType` Attribut vermittelt die *Semantik* der Daten als dagegen spricht, wie er auf dem Bildschirm gerendert werden, und bietet die folgenden Vorteile, die Sie nicht mit erhalten `DisplayFormat`:

- Der Browser HTML5-Funktionen aktivieren kann (z. B. zum Anzeigen eines Kalendersteuerelements, dem Gebietsschema entsprechende Währungssymbol, Links per e-Mail einige clientseitige Eingabe Validierung usw..).
- Standardmäßig wird der Browser Rendern von Daten über das richtige Format auf Grundlage Ihrer [Gebietsschema](https://msdn.microsoft.com/library/vstudio/wyzd2bce.aspx).
- Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribut kann MVC auf das Feld nach rechts-Vorlage zum Rendern der Daten auswählen, aktivieren (die [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) verwendet die Vorlage Zeichenfolge). Weitere Informationen finden Sie in Brad Wilsons [ASP.NET MVC 2-Vorlagen](http://bradwilson.typepad.com/blog/2009/10/aspnet-mvc-2-templates-part-1-introduction.html). (Obwohl für MVC 2 geschrieben, gilt dieses Artikels noch auf die aktuelle Version von ASP.NET MVC.)

Bei Verwendung der `DataType` Attribut mit einem Datumsfeld müssen Sie angeben der `DisplayFormat` Attribut auch, um sicherzustellen, dass das Feld in Chrome-Browsern ordnungsgemäß gerendert wird. Weitere Informationen finden Sie unter [dieser Thread StackOverflow](http://stackoverflow.com/questions/12633471/mvc4-datatype-date-editorfor-wont-display-date-value-in-chrome-fine-in-ie).

Weitere Informationen zum Umgang mit anderen Datumsformate in MVC finden Sie unter [MVC 5-Einführung: untersuchen die Methoden bearbeiten und die Ansicht bearbeiten](../introduction/examining-the-edit-methods-and-edit-view.md) , und suchen Sie auf der Seite &quot;Internationalisierung&quot;.

Führen Sie die Indexseite Student erneut aus, und beachten Sie, dass die Zeiten für die Datumsangaben für die Registrierung nicht mehr angezeigt werden. Die gleiche wird "true" für jede Ansicht, die verwendet werden die `Student` Modell.

![Students_index_page_with_formatted_date](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image2.png)

### <a name="the-stringlengthattribute"></a>Das ' StringLengthAttribute '

Sie können auch Regeln für die Überprüfung von Daten und Verwenden von Attributen Überprüfungsfehlermeldungen angeben. Die [StringLength-Attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) legt die maximale Länge in der Datenbank und bietet clientseitige und serverseitige Validierung für ASP.NET MVC. Sie können auch die minimale Zeichenfolgenlänge in diesem Attribut angeben, aber der minimale Wert hat keine Auswirkungen auf das Datenbankschema.

Angenommen, Sie möchten sicherstellen, dass Benutzer nicht mehr als 50 Zeichen für einen Namen eingeben. Um diese Einschränkung hinzuzufügen, fügen Sie [StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) -Attribute verwenden, um die `LastName` und `FirstMidName` Eigenschaften, wie im folgenden Beispiel gezeigt:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample3.cs?highlight=10,12)]

Die [StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) Attribut wird nicht verhindern, dass einen Benutzer Leerzeichen für einen Namen eingeben. Sie können die [der reguläre Ausdruck](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) Attribut Einschränkungen auf die Eingabe anwenden. Der folgende Code ist z. B. erforderlich, das erste Zeichen Großbuchstaben bestehen muss und die übrigen Zeichen ein Buchstabe sein:

`[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]`

Die [MaxLength](https://msdn.microsoft.com/library/System.ComponentModel.DataAnnotations.MaxLengthAttribute.aspx) Attribut verfügt über ähnliche Funktionen, die [StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) -Attribut aber nicht den clientseitigen Validierung.

Führen Sie die Anwendung, und klicken Sie auf die **Studenten** Registerkarte. Sie erhalten die folgende Fehlermeldung:

*Das Modell, sichern den Kontext "SchoolContext" wurde geändert, seit der Erstellung der Datenbank. Erwägen Sie Code First-Migrationen zum Aktualisieren der Datenbank ([https://go.microsoft.com/fwlink/?LinkId=238269](https://go.microsoft.com/fwlink/?LinkId=238269)).*

Das Datenbankmodell auf eine Weise, die eine Änderung im Datenbankschema erfordert geändert wurde, und Entity Framework wurde festgestellt, dass ein. Verwenden Sie Migrationen, um das Schema aktualisieren, ohne Verlust von Daten, die Sie über die Benutzeroberfläche mit der Datenbank hinzugefügt. Wenn Sie Daten geändert, durch die erstellte der `Seed` Methode, die wieder in seinen ursprünglichen Zustand aufgrund von geändert werden die [kann nicht](https://msdn.microsoft.com/library/hh846520(v=vs.103).aspx) -Methode, die Sie verwenden, die `Seed` Methode. ([AddOrUpdate](https://msdn.microsoft.com/library/hh846520(v=vs.103).aspx) ist gleichbedeutend mit einem "Upsert" Vorgang aus der Terminologie von Datenbanken.)

Geben Sie in der Paket-Manager-Konsole (PMC) die folgenden Befehle ein:

[!code-console[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample4.cmd)]

Die `add-migration` Befehl erstellt eine Datei namens  *&lt;Zeitstempel&gt;\_MaxLengthOnNames.cs*. Diese Datei enthält Code, in der `Up` -Methode, die die Datenbank entsprechend der aktuellen Datenmodell aktualisiert wird. Die `update-database` -Befehl ausgeführt haben, diesen Code.

Der Zeitstempel der Dateiname Migrationen vorangestellt wird vom Entity Framework verwendet, um die Migrationen zu bestellen. Sie können mehrere livemigrationen vor ihrer Ausführung erstellen die `update-database` -Befehl, und klicken Sie dann alle die Migrationen werden angewendet, in der Reihenfolge, in dem sie erstellt wurden.

Führen Sie die **erstellen** Seite, und geben Sie entweder der Name mehr als 50 Zeichen. Beim Klicken auf **erstellen**, clientseitige Validierung zeigt eine Fehlermeldung angezeigt.

![Client-side Val-Fehler](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image3.png)

### <a name="the-column-attribute"></a>Das Spaltenattribut

Attribute können auch steuern, wie die Klassen und Eigenschaften in der Datenbank zugeordnet werden. Angenommen, Sie den Namen verwendet haben `FirstMidName` für den Vornamen-Feld verwendet wird, weil das Feld kann auch ein zweiter Vorname enthalten. Die Datenbankspalte benannt werden, möchten jedoch `FirstName`, da der Benutzer, die Ad-hoc-Abfragen für die Datenbank geschrieben werden auf diesen Namen daran gewöhnt sind. Um diese Zuordnung vornehmen, können Sie die `Column` Attribut.

Die `Column` Attribut gibt an, wenn die Datenbank erstellt wurde, ist die Spalte von der `Student` Tabelle, zugeordnet der `FirstMidName` Eigenschaft den Namen `FirstName`. Das heißt, wenn Ihr Code bezieht sich auf `Student.FirstMidName`, die Daten aus stammen oder aktualisiert werden, der `FirstName` Spalte die `Student` Tabelle. Wenn Sie keinen Spaltennamen angeben, werden ihm den gleichen Namen wie den Namen der Eigenschaft zugewiesen.

In der *Student.cs* hinzufügen. eine `using` -Anweisung für [System.ComponentModel.DataAnnotations.Schema](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.aspx) und Hinzufügen der Spalte Name-Attribut, das `FirstMidName` Eigenschaft entsprechend der folgenden hervorgehobenen Code hinzu:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample5.cs?highlight=4,14)]

Das Hinzufügen der [Spaltenattribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute.aspx) das Modell der SchoolContext sichern, damit sie die Datenbank zu entsprechen, wird nicht geändert. Geben Sie die folgenden Befehle aus, in der PMC Migration erstellen:

[!code-console[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample6.cmd)]

In **Server-Explorer**öffnen die *Student* Tabellen-Designer durch Doppelklicken auf die *Student* Tabelle.

![](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image4.png)

Die folgende Abbildung zeigt den ursprünglichen Spaltennamen an, wie sie war, bevor Sie die ersten beiden Migrationen angewendet. Neben dem Spaltennamen von `FirstMidName` auf `FirstName`, geändert, die zwei Namensspalten `MAX` auf 50 Zeichen.

![](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image5.png)

Sie können auch Änderungen Datenbank Zuordnung mithilfe der [Fluent-API](https://msdn.microsoft.com/data/jj591617), wie Sie weiter unten in diesem Lernprogramm sehen.

> [!NOTE]
> Wenn Sie versuchen, die kompiliert werden, bevor Sie alle der Entitätsklassen in den folgenden Abschnitten erstellt haben, erhalten Sie möglicherweise Compilerfehler angezeigt.


## <a name="complete-changes-to-the-student-entity"></a>Nehmen Sie Änderungen vor, um die Student-Entität

![Student_entity](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image6.png)

In *Models\Student.cs*, die von Ihnen hinzugefügte Code zuvor durch den folgenden Code ersetzen. Die Änderungen werden hervorgehoben.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample7.cs?highlight=11,13,15,18,22,25-32)]

### <a name="the-required-attribute"></a>Das erforderliche Attribut

Die [erforderliches Attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx) macht die erforderlichen Felder des Name-Eigenschaften. Die `Required attribute` ist nicht erforderlich für Werttypen wie z. B. "DateTime", "Int", double und float. Werttypen können einen null-Wert zugewiesen werden, damit sie grundsätzlich als Pflichtfelder behandelt werden. Könnten Sie entfernen die [erforderliches Attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx) , und Ersetzen Sie es mit einem minimum Length-Parameter für die `StringLength` Attribut:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample8.cs?highlight=2)]

### <a name="the-display-attribute"></a>Das Attribut anzeigen

Die `Display` Attribut gibt an, dass die Beschriftung für die Textfelder "Vorname", "Last Name", "Full Name" und "Registrierungsdatum" anstelle des Namens der Eigenschaft in jeder Instanz werden soll (der kein Leerzeichen, die die Wörter geteilt besitzt).

### <a name="the-fullname-calculated-property"></a>Der FullName berechnete Eigenschaft

`FullName`ist eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch Verketten von zwei weitere Eigenschaften erstellt wird. Aus diesem Grund ist es nur eine `get` -Accessor und keine `FullName` Spalte in der Datenbank generiert werden.

## <a name="create-the-instructor-entity"></a>Die Instructor-Entität erstellen

![Instructor_entity](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image7.png)

Erstellen Sie *Models\Instructor.cs*, ersetzen den Code durch den folgenden Code:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample9.cs)]

Beachten Sie, dass mehrere Eigenschaften in werden der `Student` und `Instructor` Entitäten. In der [Implementieren von Vererbung](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md) weiter unten in diesem Lernprogramm müssen Sie diesen Code aus, um die Redundanz auszuschließen Umgestalten.

Deshalb können Sie auch die Instructor-Klasse wie folgt schreiben können, können Sie mehrere Attribute in einer Zeile einfügen:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample10.cs)]

### <a name="the-courses-and-officeassignment-navigation-properties"></a>Der Kurse und OfficeAssignment Navigationseigenschaften

Die `Courses` und `OfficeAssignment` Eigenschaften sind Navigationseigenschaften. Wie zuvor erläutert wurde, werden sie in der Regel definiert als [virtuellen](https://msdn.microsoft.com/library/9fkccyh4(v=vs.110).aspx) , damit sie eine Entity Framework-Funktion wird aufgerufen, nutzen können [verzögertes Laden](https://msdn.microsoft.com/magazine/hh205756.aspx). Darüber hinaus, wenn eine Navigationseigenschaft über mehrere Entitäten enthalten kann, dessen Typ muss implementieren die [ICollection&lt;T&gt; ](https://msdn.microsoft.com/library/92t2ye13.aspx) Schnittstelle. Z. B. [IList&lt;T&gt; ](https://msdn.microsoft.com/library/5y536ey6.aspx) qualifiziert, aber nicht [IEnumerable&lt;T&gt; ](https://msdn.microsoft.com/library/9eekhta0.aspx) da `IEnumerable<T>` implementiert keine [hinzufügen ](https://msdn.microsoft.com/library/63ywd54z.aspx).

Ein Kursleiter kann eine beliebige Anzahl von Kurse werden folgende Themen behandelt, sodass `Courses` ist definiert als eine Auflistung von `Course` Entitäten.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample11.cs)]

Unsere Geschäftsregeln Status ein Kursleiter kann nur haben darf höchstens eine Niederlassung, sodass `OfficeAssignment` ist definiert als eine einzelne `OfficeAssignment` Entität (was u. u. nicht `null` , wenn keine Office zugewiesen ist).

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample12.cs)]

## <a name="create-the-officeassignment-entity"></a>Erstellen Sie die Entität OfficeAssignment

![OfficeAssignment_entity](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image8.png)

Erstellen Sie *Models\OfficeAssignment.cs* durch den folgenden Code:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample13.cs)]

Erstellen Sie das Projekt, speichert die Änderungen und stellt sicher, dass Sie alle Kopien getroffen haben, und fügen Sie Fehler, die der Compiler abfangen kann.

### <a name="the-key-attribute"></a>Das Schlüsselattribut

Es ist eine auf 0 (null) oder 1 eine Beziehung zwischen der `Instructor` und `OfficeAssignment` Entitäten. Eine bürozuweisung nur in Bezug auf den ihm zugewiesenen Dozenten vorhanden ist und daher primäre Schlüssel auch die Fremdschlüssel für die `Instructor` Entität. Das Entity Framework nicht automatisch erkannt, aber `InstructorID` wie die primäre Datenbank Schlüssel der Entität, da dessen Namen folgen, nicht die `ID` oder *Classname* `ID` Benennungskonvention. Aus diesem Grund die `Key` Attribut wird verwendet, um sie als Schlüssel zu identifizieren:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample14.cs)]

Sie können auch die `Key` Attribut, wenn die Entität verfügt über einen eigenen Primärschlüssel aber etwas anderes als Eigenschaftenname möchten `classnameID` oder `ID`. Standardmäßig behandelt EF des Schlüssels als nicht-datenbankgeneriert, da die Spalte für eine identifizierende Beziehung ist.

### <a name="the-foreignkey-attribute"></a>Das ForeignKey-Attribut

Wenn eine 1: 0 (null)-oder-1-Beziehung oder eine 1: 1-Beziehung zwischen zwei Entitäten vorhanden ist (solche zwischen `OfficeAssignment` und `Instructor`), EF nicht funktioniert, welche Ende der Beziehung der Prinzipal ist und welche End abhängig ist. 1: 1-Beziehungen haben eine Navigationseigenschaft für den Verweis in jeder Klasse, die andere Klasse. Die [ForeignKey-Attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute.aspx) kann auf die abhängigen Klasse zum Erstellen der Beziehung angewendet werden. Wenn Sie weglassen der [ForeignKey-Attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute.aspx), erhalten Sie die folgende Fehlermeldung, wenn Sie versuchen, die Migration zu erstellen:

*Kann nicht das prinzipalende einer Zuordnung zwischen den Typen "ContosoUniversity.Models.OfficeAssignment" und "ContosoUniversity.Models.Instructor" bestimmt. Das prinzipalende dieser Zuordnung muss mithilfe der fluent-API für oder von datenanmerkungen explizit konfiguriert werden.*

Später in diesem Lernprogramm sehen Sie, wie Sie diese Beziehung mit der fluent-API konfigurieren.

### <a name="the-instructor-navigation-property"></a>Die Navigationseigenschaft für Dozenten

Die `Instructor` Entität verfügt über einen `OfficeAssignment` Navigationseigenschaft (da eine bürozuweisung möglicherweise nicht über ein Kursleiter verfügen), und die `OfficeAssignment` Entität hat eine NULL- `Instructor` Navigationseigenschaft (da eine bürozuweisung kann nicht ohne einen Kursleiter--vorhanden `InstructorID` ist NULL-Werte zulässt). Wenn ein `Instructor` Entität besitzt einen zugehörigen `OfficeAssignment` Entität, jede Entität hat einen Verweis auf die andere in die Navigationseigenschaft.

Sie setzen konnte eine `[Required]` Attribut für die Instructor-Navigationseigenschaft, um anzugeben, dass eine verwandte Dozenten muss, aber Sie nicht, da der InstructorID Fremdschlüssel (also auch die Taste, um diese Tabelle) NULL-ist dies.

## <a name="modify-the-course-entity"></a>Ändern Sie die Kurs-Entität

![Course_entity](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image9.png)

In *Models\Course.cs*, die von Ihnen hinzugefügte Code zuvor durch den folgenden Code ersetzen:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample15.cs)]

Die Kurs-Entität verfügt über eine Fremdschlüsseleigenschaft `DepartmentID` die verweist auf den zugehörigen `Department` Entität und verfügt über eine `Department` Navigationseigenschaft. Das Entity Framework erfordern nicht, dass Sie eine foreign Key-Eigenschaft mit dem Datenmodell hinzufügen, wenn eine Navigationseigenschaft für einer verknüpften Entität sind. EF erstellt automatisch Fremdschlüssel in der Datenbank, wo sie benötigt werden. Jedoch müssen die Fremdschlüssel im Datenmodell kann Updates vornehmen, einfacher und effizienter. Beispielsweise wird beim Abrufen einer Entität Kurs zu bearbeiten, die `Department` Entität ist null. Wenn Sie es nicht geladen werden, also bei der Aktualisierung von der Entität Kurs haben zuerst abgerufen der `Department` Entität. Wenn die Fremdschlüsseleigenschaft `DepartmentID` enthalten ist in das Datenmodell, müssen Sie beim Abrufen der `Department` Entität vor dem aktualisieren.

### <a name="the-databasegenerated-attribute"></a>Das DatabaseGenerated-Attribut

Die [DatabaseGenerated Attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute.aspx) mit der [keine](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedoption(v=vs.110).aspx) Parameter auf die `CourseID` Eigenschaft gibt an, dass Primärschlüsselwerte sind vom Benutzer bereitgestellte anstatt von der Datenbank generiert.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample16.cs)]

Standardmäßig nimmt das Entity Framework die Primärschlüsselwerte von der Datenbank generiert werden. Das ist in den meisten Fällen erwünscht. Beachten Sie jedoch bei `Course` Entitäten, die Sie diverse benutzerdefiniertes Kurs, z. B. eine Reihe von 1000 für eine Abteilung, eine Reihe 2000 für eine andere Abteilung verwenden und so weiter.

### <a name="foreign-key-and-navigation-properties"></a>Foreign Key- und Navigationseigenschaften

Die foreign Key-Eigenschaften und Navigationseigenschaften in den `Course` Entität wider, die folgenden Beziehungen:

- Ein Kurs wird an eine Abteilung zugewiesen, daher besteht eine `DepartmentID` Fremdschlüssel und einem `Department` Navigationseigenschaft für die oben genannten Gründe. 

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample17.cs)]
- Ein Kurs kann eine beliebige Anzahl von Lernende, haben also die `Enrollments` Navigationseigenschaft ist eine Sammlung: 

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample18.cs)]
- Ein Kurs kann von mehreren Lehrkräfte vermittelten werden daher die `Instructors` Navigationseigenschaft ist eine Sammlung: 

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample19.cs)]

## <a name="create-the-department-entity"></a>Erstellen Sie die Entität Department

![Department_entity](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image10.png)

Erstellen Sie *Models\Department.cs* durch den folgenden Code:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample20.cs)]

### <a name="the-column-attribute"></a>Das Spaltenattribut

Zuvor Sie verwendet die [Spaltenattribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute.aspx) namenszuordnung Spalte zu ändern. Im Code für die `Department` Entität, die `Column` Attribut wird verwendet, um SQL datentypzuordnung so, dass die Spalte definiert wird mithilfe der SQL Server ändern [Money](https://msdn.microsoft.com/library/ms179882.aspx) Typ in der Datenbank:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample21.cs)]

Die Zuordnung ist im Allgemeinen nicht erforderlich, da das Entity Framework in der Regel wählt die entsprechenden SQL Server-Datentyp, der basierend auf dem CLR-Typ, den Sie für die Eigenschaft zu definieren. Die CLR `decimal` wird zugeordnet zu SQL Server `decimal` Typ. Jedoch in diesem Fall wissen Sie, dass die Spalte Währungsbeträge, halten und die [Money](https://msdn.microsoft.com/library/ms179882.aspx) Datentyp für, die besser geeignet ist. Weitere Informationen zu CLR-Datentypen und wie sie in SQL Server-Datentypen entsprechen, finden Sie unter [SqlClient für Entity Framework-Typen](https://msdn.microsoft.com/library/bb896344.aspx).

### <a name="foreign-key-and-navigation-properties"></a>Foreign Key- und Navigationseigenschaften

Die folgenden Beziehungen entsprechen den foreign key- und Navigation-Eigenschaften:

- Eine Abteilung kann oder besitzen möglicherweise nicht von einem Administrator und ein Administrator ist immer einen Kursleiter. Aus diesem Grund die `InstructorID` Eigenschaft dient als Fremdschlüssel für die `Instructor` Entität und ein Fragezeichen hinzugefügt, nachdem die `int` Geben Sie die Bezeichnung, markieren Sie die Eigenschaft als NULL-Werte zulässt. Die Navigationseigenschaft heißt `Administrator` jedoch enthält ein `Instructor` Entität: 

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample22.cs)]
- Eine Abteilung möglicherweise viele Kurse, daher besteht eine `Courses` Navigationseigenschaft: 

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample23.cs)]

 > [!NOTE]
 > Gemäß der Konvention ermöglicht das Entity Framework kaskadierendes Delete für die NULL-Fremdschlüssel und für viele-zu-viele-Beziehungen. Dies kann zu zirkuläre Cascade Delete Regeln führen, der eine Ausnahme verursacht wird, wenn Sie versuchen, eine Migration hinzuzufügen. Angenommen, Sie definiert haben die `Department.InstructorID` Eigenschaft als NULL-Werte zulässt, Sie erhalten die folgende Ausnahme angezeigt: "die referenzielle Beziehung führt ein zyklischer Verweis, der nicht zulässig ist." Bei Bedarf Ihre Geschäftsregeln `InstructorID` Eigenschaft nicht NULL-Werte zulässt, müssten Sie die folgende fluent-API-Anweisung verwenden, um kaskadierte Löschung auf der Beziehung zu deaktivieren: 

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample24.cs)]


## <a name="modify-the-enrollment-entity"></a>Ändern Sie die Registrierung-Entität

![Enrollment_entity](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image11.png)

 In *Models\Enrollment.cs*, die von Ihnen hinzugefügte Code zuvor durch den folgenden Code ersetzen

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample25.cs?highlight=1,15)]

### <a name="foreign-key-and-navigation-properties"></a>Foreign Key- und Navigationseigenschaften

Die foreign Key-Eigenschaften und Navigationseigenschaften reflektieren die folgenden Beziehungen:

- Ein Anmeldungsdatensatz ist für eine einzelnes Kurs, es ist also eine `CourseID` foreign Key-Eigenschaft und eine `Course` Navigationseigenschaft: 

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample26.cs)]
- Ein Anmeldungsdatensatz ist für einen einzelnen Studenten, es ist also eine `StudentID` foreign Key-Eigenschaft und eine `Student` Navigationseigenschaft: 

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample27.cs)]

### <a name="many-to-many-relationships"></a>n:n-Beziehungen

Es ist eine m: n-Beziehung zwischen der `Student` und `Course` Entitäten, und die `Enrollment` Entität als Jointabelle m: n--Funktionen *mit Nutzlast* in der Datenbank. Dies bedeutet, dass die `Enrollment` Tabelle enthält zusätzliche Daten als Fremdschlüssel für den verknüpften Tabellen (in diesem Fall kann ein primärer Schlüssel und ein `Grade` Eigenschaft).

Die folgende Abbildung zeigt, wie diese Beziehungen in einem Entitätsdiagramm aussieht. (Dieses Diagramm generiert wurde, mithilfe der [Entity Framework Power Tools](https://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d), erstellen das Diagramm ist nicht Teil des Lernprogramms wird gerade verwendet hier als veranschaulicht.)

![Student Course_many zu many_relationship](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image12.png)

Jede Beziehungslinie verfügt über eine 1 an einem Ende und ein Sternchen (\*), der am anderen, die angibt, einer 1: n-Beziehung.

Wenn die `Enrollment` Tabelle haben nicht Grade Informationen enthalten, er dürfte nur zwei Fremdschlüssel enthalten `CourseID` und `StudentID`. In diesem Fall würde er in einer m: n-Jointabelle entsprechen *ohne Nutzlast* (oder eine *reine Jointabelle*) in der Datenbank und würde nicht müssen Sie eine Modellklasse überhaupt dafür zu erstellen. Die `Instructor` und `Course` Entitäten verfügen über diese Art von m: n-Beziehung, und wie Sie sehen können, ist keine Entitätsklasse, zwischen ihnen:

![Instructor Course_many zu many_relationship](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image13.png)

Eine Jointabelle wird jedoch in der Datenbank erforderlich, wie im folgenden Diagramm dargestellt:

![Instructor Course_many zu many_relationship_tables](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image14.png)

Das Entity Framework erstellt automatisch die `CourseInstructor` Tabelle, und Sie lesen und aktualisieren sie indirekt durch Lesen und Aktualisieren der `Instructor.Courses` und `Course.Instructors` Navigationseigenschaften.

## <a name="entity-diagram-showing-relationships"></a>Anzeigen von Entitätsbeziehungen-Diagramm

Die folgende Abbildung zeigt das Diagramm, das den Entity Framework-Power-Tools für das abgeschlossene Modell "School" zu erstellen.

![School_data_model_diagram](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image15.png)

Neben den Zeilen der m: n-Beziehung (\* auf \*) und die Zeilen 1: n-Beziehung (1 bis \*), sehen Sie die zu 0 (null) oder eins eine Beziehungslinie (1 zu 0.. 1) zwischen den `Instructor` und `OfficeAssignment` Entitäten und 0 (null)-oder-1-n-Beziehung (0.. 1 zu \*) zwischen den Dozenten "und" Department-Entitäten.

## <a name="customize-the-data-model-by-adding-code-to-the-database-context"></a>Passen Sie das Datenmodell an, indem Sie Code hinzufügen, um den Datenbankkontext

Als Nächstes fügen Sie die neue Entitäten, die die `SchoolContext` Klasse, und passen Sie einige der Zuordnung mit [fluent-API](https://msdn.microsoft.com/data/jj591617) aufrufen. Die API lautet "fluent", da es häufig von Vermittlungsstellen eine Reihe von Methodenaufrufen zusammen in einer einzigen Anweisung, wie im folgenden Beispiel verwendet:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample28.cs)]

In diesem Lernprogramm verwenden Sie die fluent-API nur für datenbankzuordnung, die Sie mit Attributen nicht möglich. Allerdings auch können die fluent-API Sie die meisten Formatierung, Überprüfung und Zuordnungsregeln an, denen Sie durchführen können, mithilfe von Attributen angeben. Einige Attribute wie z. B. `MinimumLength` kann nicht mit der fluent-API angewendet werden. Wie bereits erwähnt, `MinimumLength` ändert nicht das Schema gilt nur für eine Validierungsregel Client und Server-Seite

Einige Entwickler bevorzugen die fluent-API ausschließlich, damit sie ihre Entitätsklassen "bereinigen" bleibt Sie können Attribute und fluent-API kombinieren, wenn werden sollen, und es gibt einige Anpassungen, die nur mithilfe der fluent-API ausgeführt werden können, aber im Allgemeinen wird empfohlen, wählen eine dieser beiden Ansätze und konsistent, so weit wie möglich verwenden.

Zum Hinzufügen neuen Entitäten auf die Daten modellieren, und führen Sie die datenbankzuordnung, die Sie mit Attributen haben nicht, ersetzen Sie den Code in *DAL\SchoolContext.cs* durch den folgenden Code:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample29.cs)]

Die neue Anweisung in der [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx) Methode konfiguriert die m: n-Jointabelle:

- Für die m: n-Beziehung zwischen der `Instructor` und `Course` Entitäten, die Code gibt an, die Tabellen- und Spaltennamen für die Jointabelle. Code zuerst kann die m: n-Beziehung für Sie konfigurieren ohne diesen Code, aber wenn Sie ihn nicht aufrufen, erhalten Sie Standardnamen wie z. B. `InstructorInstructorID` für die `InstructorID` Spalte.

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample30.cs)]

Der folgende Code bietet ein Beispiel, wie Sie fluent-API anstelle von Attributen verwendet haben können, geben Sie die Beziehung zwischen der `Instructor` und `OfficeAssignment` Entitäten:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample31.cs)]

Weitere Informationen dazu, welche Aufgaben im Hintergrund "fluent-API" Anweisungen ausführen, finden Sie unter der [Fluent-API](https://blogs.msdn.com/b/aspnetue/archive/2011/05/04/entity-framework-code-first-tutorial-supplement-what-is-going-on-in-a-fluent-api-call.aspx) Blogbeitrag.

## <a name="seed-the-database-with-test-data"></a>Ausgangswert für die Datenbank mit Testdaten

Ersetzen Sie den Code in der *Migrations\Configuration.cs* Datei durch den folgenden Code um Seed-Daten für die neuen Entitäten zu gewährleisten Sie erstellt haben.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample32.cs)]

Im ersten Lernprogramm haben Sie gesehen, Großteil dieser Code einfach aktualisiert oder erstellt neue Entitätsobjekten und lädt Beispieldaten in die Eigenschaften nach Bedarf zu Testzwecken. Beachten Sie jedoch wie die `Course` Entität besitzt eine m: n-Beziehung mit der `Instructor` Entität behandelt wird:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample33.cs)]

Beim Erstellen einer `Course` -Objekt, initialisieren Sie die `Instructors` Navigationseigenschaft als eine leere Auflistung, die mit dem Code `Instructors = new List<Instructor>()`. Dadurch hinzuzufügende `Instructor` dies verbundenen Entitäten `Course` mithilfe der `Instructors.Add` Methode. Wenn Sie eine leere Liste erstellt haben, Sie wäre nicht hinzufügen können diese Beziehungen, da die `Instructors` Eigenschaft würde null sein und müssen Sie keine `Add` Methode. Sie können auch die Listen-Initialisierung an den Konstruktor hinzufügen.

## <a name="add-a-migration-and-update-the-database"></a>Fügen Sie eine Migration und Aktualisierung der Datenbank

Geben Sie an der Systemmonitor die `add-migration` Befehl (nicht die `update-database` Befehl noch):

`add-Migration ComplexDataModel`

Falls Sie versucht haben, führen Sie die `update-database` an diesem Punkt Befehl (nicht gerade noch), erhalten Sie die folgende Fehlermeldung:

*Die ALTER TABLE-Anweisung steht in Konflikt mit der FOREIGN KEY-Einschränkung "FK\_Dbo. Kurs\_Dbo. Abteilung\_"DepartmentID" ". Der Konflikt trat in der Datenbank "ContosoUniversity", Tabelle "Dbo. Abteilung", Spalte"DepartmentID".*

In einigen Fällen, wenn Sie Migrationen mit vorhandenen Daten ausführen, müssen Sie zum Einfügen von Stubdaten in die Datenbank foreign Key-Einschränkungen nicht erfüllen, und ist Folgendes müssen Sie dies jetzt tun. Der generierte Code in der ComplexDataModel `Up` Methode fügt einen NULL- `DepartmentID` Fremdschlüssel für die `Course` Tabelle. Da es bereits Zeilen in der `Course` Tabelle, wenn der Code ausgeführt wird, die `AddColumn` Vorgang schlägt fehl, da SQL Server nicht weiß, welcher Wert in die Spalte einfügen, die darf nicht null sein. So ändern Sie den Code, um geben der neuen Spalte einen Standardwert, und erstellen Sie eine Abteilung ein Stub mit dem Namen "Temp" fungiert als die standardmäßige Abteilung aus diesem Grund haben. Folglich vorhandenen `Course` Zeilen werden alle werden im Zusammenhang mit der Abteilung "Temp" nach der `Up` Methode ausgeführt wird. Sie können sie mit der richtigen Abteilungen in verknüpfen die `Seed` Methode.

Bearbeiten der &lt; *Zeitstempel&gt;\_ComplexDataModel.cs* Datei, kommentieren Sie die Codezeile, die die Course-Tabelle die Spalte "DepartmentID" hinzufügt, und fügen Sie folgenden hervorgehobenen Code (die kommentierten Zeile wird ebenfalls hervorgehoben):

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample34.cs?highlight=14-18)]

Wenn die `Seed` Methode ausgeführt wird, er fügt Zeilen in der `Department` Tabelle und es werden Verknüpfen vorhandener `Course` Zeilen in diese neue `Department` Zeilen. Wenn Kurse in der Benutzeroberfläche noch nicht hinzugefügt werden, Sie würden dann nicht mehr benötigen die Abteilung "Temp" oder den Standardwert auf der `Course.DepartmentID` Spalte. Damit wird die Möglichkeit, dass jemand Kurse hinzugefügt haben, mithilfe der Anwendung, Sie würden auch aktualisieren möchten die `Seed` Methodencode, um sicherzustellen, dass alle `Course` Zeilen (nicht nur die von früheren Ausführungen des eingefügt, die `Seed` Methode) haben gültige `DepartmentID` Werte, bevor Sie die standardmäßigen entfernt werden aus der Spalte Wert und die Abteilung "Temp" zu löschen.

Sie nach dem Bearbeiten der &lt; *Zeitstempel&gt;\_ComplexDataModel.cs* Datei, geben Sie die `update-database` in die PMC die Migration auszuführenden Befehl.

[!code-powershell[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample35.ps1)]

> [!NOTE]
> Es ist möglich, andere Fehlermeldungen erhalten, bei der Migration von Daten und festlegen, dass schemaänderungen. Wenn Sie Fehler bei der Migration zu, die Sie nicht beheben können erhalten, können Sie den Datenbanknamen in der Verbindungszeichenfolge ändern oder löschen Sie die Datenbank. Der einfachste Ansatz besteht darin, benennen Sie die Datenbank in *"Web.config"* Datei. Das folgende Beispiel zeigt den Namen in CU geändert\_Test:
> 
> [!code-xml[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample36.xml?highlight=1)]
> 
> Mit einer neuen Datenbank, es sind keine Daten zu migrieren, und die `update-database` Befehl ist weitaus höheren Wahrscheinlichkeit in ohne Fehler abgeschlossen werden. Informationen zum Löschen der Datenbank finden Sie unter [Gewusst wie: Löschen einer Datenbank aus Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).
> 
> Schlägt dies fehl, wird eine andere Schritte darin, die Sie ausprobieren können die Datenbank erneut zu initialisieren, durch Eingabe des folgenden Befehls in die PMC:
> 
> `update-database -TargetMigration:0`


Öffnen Sie die Datenbank im **Server-Explorer** wie zuvor war, und erweitern Sie die **Tabellen** Knoten, um anzuzeigen, dass alle Tabellen erstellt wurden. (Falls noch **Server-Explorer** aus früheren Zeitpunkt öffnen, klicken Sie auf die **aktualisieren** Schaltfläche.)

![](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image16.png)

Sie haben eine Modellklasse für erstellen die `CourseInstructor` Tabelle. Wie zuvor erläutert, ist dies eine Jointabelle für die m: n-Beziehung zwischen der `Instructor` und `Course` Entitäten.

Mit der rechten Maustaste die `CourseInstructor` Tabelle, und wählen Sie **Tabellendaten anzeigen** , stellen Sie sicher, dass sie Daten als Ergebnis des aufweist der `Instructor` Entitäten, die Sie hinzugefügt, um haben die `Course.Instructors` Navigationseigenschaft.

![Table_data_in_CourseInstructor_table](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image17.png)

## <a name="summary"></a>Zusammenfassung

Sie haben jetzt eine komplexere Datenmodell und die entsprechende Datenbank. Im folgenden Lernprogramm erfahren Sie mehr über verschiedene Möglichkeiten, verwandte Daten zuzugreifen.

Lassen Sie Sie Feedback auf wie in diesem Lernprogramm mögen und was wir weiter verbessern können. Sie können auch neue Themen am anfordern [Me wie mit Code anzeigen](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

Links zu anderen Entity Framework-Ressourcen finden Sie in der [ASP.NET Data Access - Ressourcen empfohlen](../../../../whitepapers/aspnet-data-access-content-map.md).

>[!div class="step-by-step"]
[Zurück](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)
[Weiter](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)
