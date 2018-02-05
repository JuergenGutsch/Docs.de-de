---
title: "Razor-Pages mit EF - Parallelität - 8 von 8-Kern"
author: rick-anderson
description: "Dieses Lernprogramm zeigt, wie Konflikte zu behandeln, wenn mehrere Benutzer gleichzeitig derselben Entität aktualisieren."
manager: wpickett
ms.author: riande
ms.date: 11/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/concurrency
ms.openlocfilehash: 1c6cdefa1410839606711d7460a8f4d0f1d6c72b
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
En-us /

# <a name="handling-concurrency-conflicts---ef-core-with-razor-pages-8-of-8"></a>Parallelitätskonflikte - EF-Core mit Razor-Pages (8 von 8)

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), und [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

Dieses Lernprogramm veranschaulicht, wie Konflikte behandelt wird, wenn mehrere Benutzer gleichzeitig (gleichzeitig) eine Entität aktualisieren. Wenn Probleme können nicht zu lösen auftreten, laden Sie die [abgeschlossene Anwendung für diese Stufe](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part8).

## <a name="concurrency-conflicts"></a>Parallelitätskonflikte

Parallelitätskonflikt tritt auf, wenn:

* Ein Benutzer navigiert zu der Seite "Bearbeiten" für eine Entität.
* Ein anderer Benutzer aktualisiert dieselbe Entität, bevor die erste Änderung des Benutzers mit der Datenbank geschrieben werden.

Wenn Parallelität Erkennung, aktiviert ist wenn gleichzeitige Updates auftreten:

* Das letzte Update gewinnt. D. h. werden die letzten Aktualisieren von Werten mit der Datenbank gespeichert.
* Die erste von der aktuellen Updates verloren.

### <a name="optimistic-concurrency"></a>Optimistische Nebenläufigkeit

Vollständige Parallelität ermöglicht Parallelitätskonflikte durchgeführt werden soll, und klicken Sie dann reagiert entsprechend Wenn sie verwenden. Z. B. Andrea besucht die Abteilung bearbeiten (Seite) und ändert sich das Budget für die englischen Abteilung von $350,000.00 in 0,00.

![Ändern Sie das Budget auf 0](concurrency/_static/change-budget.png)

Bevor Andrea klickt **speichern**, John derselben Seite besuchen, und ändert sich der Start Date-Felds von 9/1/2007, 9/1/2013.

![Ändern von Startdatum in 2013](concurrency/_static/change-date.png)

Andrea klickt **speichern** erste und sieht ihr ändern, wenn der Browser die Indexseite anzeigt.

![Budget in NULL geändert](concurrency/_static/budget-zero.png)

John klickt **speichern** auf eine Bearbeitungsseite, die ein Budget von $350,000.00 weiterhin angezeigt. Was daraufhin geschieht richtet sich nach der Behandlung von Parallelitätskonflikten.

Vollständige Parallelität umfasst die folgenden Optionen:

* Sie können Nachverfolgen von ein Benutzer geändert hat, dessen Eigenschaft und nur die entsprechenden Spalten in der Datenbank zu aktualisieren.

 In diesem Szenario würden keine Daten verloren. Unterschiedliche Eigenschaften wurden durch die beiden Benutzer aktualisiert. Sie können das nächste Mal, das eine Person die englische Abteilung durchsucht, Janes und Peters Änderungen sehen. Diese Methode zur Aktualisierung reduzieren die Anzahl der Konflikte, die zu Datenverlusten führen können. Dieser Ansatz: * Datenverlust nicht vermieden werden, wenn die gleiche Eigenschaft konkurrierende geändert werden.
        * Wird im Allgemeinen nicht besonders praktisch in einer Web-app. Sie erfordert erhebliche Zustand beibehalten, um nachzuverfolgen, um alle abgerufenen Werte und neue Werte. Verwalten von großen Datenmengen Zustand kann die app-Leistung beeinträchtigen.
        * Können app-Komplexität, die im Vergleich zur Erkennung der Parallelität für eine Entität zu erhöhen.

* Sie können Peters Änderung Janes Änderung überschreiben lassen.

 Das nächste Mal eine Person durchsucht die englische Abteilung, sehen sie, 9/1/2013 und die abgerufenen $350,000.00-Wert. Dieser Ansatz nennt man eine *Client gewinnt* oder *Last in Wins* Szenario. (Alle Werte aus dem Client haben Vorrang vor was im Datenspeicher ist.) Wenn Sie die Codierung für Parallelitätsbehandlung nicht tun, Wins-Client wird automatisch durchgeführt.

* Sie können verhindern, dass Peters Änderung in der Datenbank aktualisiert. Die app in der Regel würden: * Zeigt eine Fehlermeldung an.
        * Zeigen Sie den aktuellen Zustand der Daten.
        * Ermöglicht dem Benutzer, die Änderungen erneut anzuwenden.

 Hierbei spricht einen *Store Wins* Szenario. (Der Datenspeicher Werte haben Vorrang vor den Werten, die vom Client gesendet.) In diesem Lernprogramm implementieren Sie die Wins-Store-Szenario. Diese Methode wird sichergestellt, dass keine Änderungen überschrieben werden, ohne dass ein Benutzer wird benachrichtigt.

## <a name="handling-concurrency"></a>Behandeln von Parallelität 

Wenn eine Eigenschaft konfiguriert ist, als ein [parallelitätstoken](https://docs.microsoft.com/ef/core/modeling/concurrency):

* EF Core stellt sicher, dass die Eigenschaft nicht geändert wurde, nachdem es abgerufen wurde. Die Überprüfung tritt auf, wenn [SaveChanges](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) oder [SaveChangesAsync](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) aufgerufen wird.
* Wenn die Eigenschaft geändert wurde, nachdem es abgerufen wurde, eine [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) ausgelöst wird. 

Die DB- und Datenmodell must be configured auslösen unterstützen `DbUpdateConcurrencyException`.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Erkennen von Konflikten bei der Parallelität für eine Eigenschaft

Parallelitätskonflikte erkannt werden können, auf der Eigenschaftenebene mit der [ConcurrencyCheck](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) Attribut. Das Attribut kann mehreren Eigenschaften für das Modell angewendet werden. Weitere Informationen finden Sie unter [Daten Anmerkungen-ConcurrencyCheck](https://docs.microsoft.com/ef/core/modeling/concurrency#data-annotations).

Die `[ConcurrencyCheck]` Attribut wird nicht in diesem Lernprogramm verwendet.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Erkennen von Konflikten bei der Parallelität in einer Zeile

Erkennen der Parallelitätskonflikte, eine [Rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) nachverfolgung Spalte mit dem Modell hinzugefügt wird.  `rowversion` :

* SQL Server bezieht. Andere Datenbanken bietet eine ähnliche Funktion möglicherweise nicht.
* Wird verwendet, um zu bestimmen, dass eine Entität nicht geändert wurde, seit dem Abruf aus der Datenbank. 

Die Datenbank generiert einen sequenziellen `rowversion` Anzahl, die jede Zeile erhöht wurde aktualisiert. In einer `Update` oder `Delete` Befehl, der `Where` -Klausel enthält den abgerufenen Wert des `rowversion`. Wenn die Zeile aktualisiert wird, hat sich geändert:

 * `rowversion`entspricht nicht den abgerufenen Wert.
 * Die `Update` oder `Delete` Befehle eine Zeile nicht finden kann, da die `Where` -Klausel enthält das abgerufene `rowversion`.
 * Ein `DbUpdateConcurrencyException` ausgelöst wird.

In EF Kern ausgeführt, wenn keine Zeilen durch aktualisiert wurden eine `Update` oder `Delete` Befehls, eine Parallelitätsausnahme ausgelöst wird.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Hinzufügen einer Überwachung-Eigenschaft auf die Entität Department

In *Models/Department.cs*, Hinzufügen einer Überwachung-Eigenschaft, die mit dem Namen RowVersion:

[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Die [Zeitstempel](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.timestampattribute) Attribut gibt an, dass diese Spalte, in enthalten ist der `Where` -Klausel der `Update` und `Delete` Befehle. Das Attribut heißt `Timestamp` da frühere Versionen von SQL Server eine SQL verwendet `timestamp` -Datentyp, bevor SQL `rowversion` Typ ersetzt.

Die fluent-API kann auch die Tracking-Eigenschaft angeben:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Der folgende Code zeigt einen Teil der T-SQL-von EF Core generiert, wenn der Name der Abteilung aktualisiert wird:

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

Das vorherige Codebeispiel markiert die `WHERE` Klausel mit `RowVersion`. Wenn der DB `RowVersion` entspricht nicht der `RowVersion` Parameter (`@p2`), keine Zeilen aktualisiert werden.

Die folgende hervorgehobene Code zeigt das T-SQL, die überprüft, dass genau eine Zeile aktualisiert wurde:

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](https://docs.microsoft.com/sql/t-sql/functions/rowcount-transact-sql) gibt die Anzahl der von der letzten Anweisung betroffenen Zeilen zurück. In keine Zeilen aktualisiert werden, EF Core löst eine `DbUpdateConcurrencyException`.

Sie können sehen, dass die T-SQL EF Core im Ausgabefenster von Visual Studio generiert.

### <a name="update-the-db"></a>Aktualisieren der Datenbank

Hinzufügen der `RowVersion` Eigenschaft ändert das DB-Modell, die eine Migration erforderlich ist.

Erstellen Sie das Projekt. Geben Sie Folgendes in einem Befehlsfenster aus:

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

Die vorherigen Befehle:

* Fügt der *Migrationen / {Time stamp}_RowVersion.cs* Migrationsdatei.
* Updates der *Migrations/SchoolContextModelSnapshot.cs* Datei. Das Update fügt die folgenden hervorgehobenen Code in die `BuildModel` Methode:

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* Führt Migrationen, um die Datenbank zu aktualisieren.

<a name="scaffold"></a>
## <a name="scaffold-the-departments-model"></a>Das Gerüst für die Abteilungen-Modell erstellen

* Beenden Sie Visual Studio.
* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).
* Führen Sie den folgenden Befehl aus:

 ```console
dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
 ```

Die vorherigen Befehl Gerüste der `Department` Modell. Öffnen Sie das Projekt in Visual Studio.

Erstellen Sie das Projekt. Der Build generiert Fehler wie folgt:

`1>Pages/Departments/Index.cshtml.cs(26,37,26,43): error CS1061: 'SchoolContext' does not
 contain a definition for 'Department' and no extension method 'Department' accepting a first
 argument of type 'SchoolContext' could be found (are you missing a using directive or
 an assembly reference?)`

 Globales ändern `_context.Department` auf `_context.Departments` (d. h. hinzufügen ein "s" `Department`). 7 Vorkommen gefunden und aktualisiert.

### <a name="update-the-departments-index-page"></a>Aktualisieren Sie die Abteilungen Indexseite

Das Gerüst Modul erstellt eine `RowVersion` Spalte für die Seite "Index", aber dieses Feld darf nicht angezeigt werden. In diesem Lernprogramm, das letzte Byte der `RowVersion` wird angezeigt, um die Parallelität zu verstehen. Das letzte Byte ist nicht unbedingt eindeutig. Eine wirkliche app wäre nicht anzeigen `RowVersion` oder das letzte Byte der `RowVersion`.

Aktualisieren Sie die Seite "Index":

* Abteilungen Index ersetzt.
* Ersetzen Sie das Markup mit `RowVersion` mit das letzte Byte der `RowVersion`.
* FullName FirstMidName ersetzt.

Das folgende Markup zeigt aktualisierte Seite:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualisieren Sie das Modell bearbeiten

Update *pages\departments\edit.cshtml.cs* durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Um ein Problem Parallelität erkennen die [OriginalValue](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) wird aktualisiert und enthält die `rowVersion` Wert aus der Entität, die es abgerufen wurde. EF Core generiert einen SQL-Befehl UPDATE mit einer WHERE-Klausel mit der ursprünglichen `RowVersion` Wert. Wenn keine Zeilen, durch den Befehl UPDATE betroffen sind (keine Zeilen vorhanden sind, die ursprüngliche `RowVersion` Wert), wird eine `DbUpdateConcurrencyException` Ausnahme wird ausgelöst.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-)]

Im vorangehenden Code `Department.RowVersion` ist der Wert die Entität abgerufen wurde. `OriginalValue`ist der Wert in der DB bei `FirstOrDefaultAsync` in diese Methode aufgerufen wurde.

Der folgende Code Ruft die Client-Werte (die Werte für diese Methode bereitgestellten) und die DB-Werte:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Der folgende Code Fügt eine benutzerdefinierte Fehlermeldung für jede Spalte, die DB wurde unterscheiden, was Werte zu anweisungsbereitstellung `OnPostAsync`:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Die folgenden hervorgehobenen Code legt die `RowVersion` Wert in den neuen Wert aus der Datenbank abgerufen. Das nächste Mal, die der Benutzer klickt auf **speichern**, nur Parallelitätsfehlern, die auftreten, seit die letzten Anzeige Bearbeitungsseite abgefangen wird.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Die `ModelState.Remove` Anweisung ist erforderlich, da `ModelState` hat die alte `RowVersion` Wert. Der Razor-Page der `ModelState` Wert für ein Feld Vorrang vor den Modell-Eigenschaftswerte hat, wenn beide vorhanden sind.

## <a name="update-the-edit-page"></a>Aktualisieren Sie die Seite "Bearbeiten"

Update *Pages/Departments/Edit.cshtml* durch Folgendes Markup:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Die vorangehenden Markup:

* Updates der `page` -Direktive aus `@page` auf `@page "{id:int}"`.
* Fügt eine ausgeblendete Zeilenversion. `RowVersion`muss hinzugefügt werden, damit Postback den Wert gebunden wird.
* Zeigt das letzte Byte der `RowVersion` zu Debugzwecken.
* Ersetzt `ViewData` mit starker Typisierung `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testen von Parallelitätskonflikten der Seite "Bearbeiten"

Öffnen Sie zwei Instanzen von Browsern Bearbeitung auf Englisch-Abteilung:

* Führen Sie die app, und wählen Sie Abteilungen.
* Mit der rechten Maustaste die **bearbeiten** Link für die englischen Abteilung, und wählen **in neuer Registerkarte öffnen**.
* Klicken Sie in der ersten Registerkarte auf die **bearbeiten** Link für die englischen Abteilung.

Die zwei browserregisterkarten werden dieselben Informationen angezeigt.

Ändern Sie den Namen in der ersten Registerkarte ' Browser ', und klicken Sie auf **speichern**.

![Abteilung bearbeiten Seite 1 nach der Änderung](concurrency/_static/edit-after-change-1.png)

Der Browser zeigt die Indexseite mit den geänderten Wert und die aktualisierte RowVersion-Indikator. Beachten Sie die aktualisierte RowVersion-Indikator, wird er auf das zweite Postback auf der anderen Registerkarte angezeigt.

Ändern Sie ein anderes Feld in der zweiten Registerkarte.

![Abteilung bearbeiten Seite 2 nach der Änderung](concurrency/_static/edit-after-change-2.png)

Klicken Sie auf **Speichern**. Fehlermeldungen für alle Felder, die die DB-Werte nicht übereinstimmen:

![Abteilung bearbeiten Seite Fehlermeldung](concurrency/_static/edit-error.png)

Dieses Browserfenster möchten nicht das Namensfeld ändern. Kopieren Sie den aktuellen Wert (Sprachen) in das Feld "Name". Registerkarte "aus. Die clientseitige Validierung entfernt die Fehlermeldung angezeigt.

![Abteilung bearbeiten Seite Fehlermeldung](concurrency/_static/cv.png)

Klicken Sie auf **speichern** erneut aus. Der Wert in der zweiten Registerkarte eingegebene wird gespeichert. Daraufhin werden die gespeicherten Werte in der Indexseite.

## <a name="update-the-delete-page"></a>Aktualisieren Sie die Seite "löschen"

Aktualisieren Sie das Modell löschen, durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Die Seite "löschen" erkennt Parallelitätskonflikte, wenn die Entität geändert wurde, nachdem es abgerufen wurde. `Department.RowVersion`ist die Zeilenversion an, wenn es sich bei die Entität abgerufen wurde. Wenn EF Kern den SQL-DELETE-Befehl erstellt, enthält Sie eine WHERE-Klausel mit `RowVersion`. Wenn wirkt sich die Ergebnisse der SQL-DELETE-Befehl in 0 (null) Zeilen:

* Die `RowVersion` in den SQL Delete-passt nicht Befehl `RowVersion` in der Datenbank.
* Eine DbUpdateConcurrencyException Ausnahme wird ausgelöst.
* `OnGetAsync`wird aufgerufen, mit der `concurrencyError`.

### <a name="update-the-delete-page"></a>Aktualisieren Sie die Seite "löschen"

Update *Pages/Departments/Delete.cshtml* durch den folgenden Code:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,36,51)]


Das vorhergehende Markup nimmt folgende Änderungen:

* Updates der `page` -Direktive aus `@page` auf `@page "{id:int}"`.
* Fügt eine Fehlermeldung an.
* Ersetzt FirstMidName mit FullName in den **Administrator** Feld.
* Änderungen `RowVersion` das letzte Byte an.
* Fügt eine ausgeblendete Zeilenversion. `RowVersion`muss hinzugefügt werden, damit Postback den Wert gebunden wird.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Testen von Parallelitätskonflikten der Seite "löschen"

Erstellen Sie eine Test-Abteilung.

Öffnen Sie zwei Instanzen von Browsern von Delete für die Test-Abteilung:

* Führen Sie die app, und wählen Sie Abteilungen.
* Mit der rechten Maustaste die **löschen** Link für den Test-Abteilung, und wählen **in neuer Registerkarte öffnen**.
* Klicken Sie auf die **bearbeiten** Link für die Test-Abteilung.

Die zwei browserregisterkarten werden dieselben Informationen angezeigt.

Ändern Sie das Budget in der ersten Registerkarte ' Browser ', und klicken Sie auf **speichern**.

Der Browser zeigt die Indexseite mit den geänderten Wert und die aktualisierte RowVersion-Indikator. Beachten Sie die aktualisierte RowVersion-Indikator, wird er auf das zweite Postback auf der anderen Registerkarte angezeigt.

Löschen Sie die Test-Abteilung, auf der zweiten Registerkarte. Ein Parallelitätsfehler ist, werden mit den aktuellen Werten aus der Datenbank angezeigt. Auf **löschen** wird die Entität gelöscht, es sei denn, `RowVersion` updated.department wurde gelöscht wurde.

Finden Sie unter [Vererbung](xref:data/ef-mvc/inheritance) zum erben von einem Datenmodell.

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Parallelitätstoken in EF Core](https://docs.microsoft.com/ef/core/modeling/concurrency)
* [Behandeln von Parallelität in EF Core](https://docs.microsoft.com/ef/core/saving/concurrency)

>[!div class="step-by-step"]
[Vorherige](xref:data/ef-rp/update-related-data)
