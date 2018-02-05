---
title: Razor-Pages mit Entity Framework Core - 8-Lernprogramm 1
author: rick-anderson
description: Veranschaulicht das Erstellen einer Razor-Pages-app, die mit Entity Framework Core
manager: wpickett
ms.author: riande
ms.date: 11/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/intro
ms.openlocfilehash: 091f34da347d52ba8e3e87779ddc4aeb790c2800
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="getting-started-with-razor-pages-and-entity-framework-core-using-visual-studio-1-of-8"></a>Erste Schritte mit Razor-Pages und Entity Framework Core mithilfe von Visual Studio (1 von 8)

Durch [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Die Web-app von Contoso University Beispiel veranschaulicht, wie ASP.NET Core 2.0 MVC-Webanwendungen, die mit Entity Framework (EF) Core 2.0 und Visual Studio 2017.

Die Beispiel-app ist eine Website für eine fiktive Contoso-Universität. Es umfasst Funktionen wie Student Zulassung, Kurs Erstellung und Instructor-Zuweisungen. Diese Seite ist die erste Aufgabe in eine Reihe von Lernprogrammen, die erläutern, wie die Universität von Contoso-Beispiel-app zu erstellen.

[Herunterladen Sie, oder zeigen Sie abgeschlossene Anwendung an.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Anweisungen zum Herunterladen](xref:tutorials/index#how-to-download-a-sample).

## <a name="prerequisites"></a>Erforderliche Komponenten

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

Vertrautheit mit [Razor-Pages](xref:mvc/razor-pages/index). Neue Programmierer sollten abschließen [erste Schritte mit Razor-Pages](xref:tutorials/razor-pages/razor-pages-start) vor dem Starten dieser Serie.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie ein Problem Sie können nicht aufgelöst werden auftreten, im Allgemeinen finden Sie die Projektmappe durch Vergleichen des Codes: auf die [Phase abgeschlossen](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) oder [abgeschlossenes Projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu-final). Eine Übersicht über häufige Fehler und deren Lösung finden Sie unter [im Abschnitt Problembehandlung der im letzten Lernprogramm in der Reihe](xref:data/ef-mvc/advanced#common-errors). Wenn Sie nicht finden, was müssen Sie es, Sie können eine Frage zu [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) für [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Diese Reihe von Lernprogrammen baut auf was in früheren Lernprogramme erfolgt. Denken Sie speichern eine Kopie des Projekts nach jeder erfolgreichen Abschluss des Lernprogramms. Wenn Probleme auftreten, können Sie über aus dem vorherigen Lernprogramm nicht zurück auf den Anfang starten. Alternativ können Sie Herunterladen einer [Phase abgeschlossen](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) und beginnen Sie erneut mit der abgeschlossenen Phase.

## <a name="the-contoso-university-web-app"></a>Die Contoso-University Web-app

Die in diesen Lernprogrammen erstellte app ist eine grundlegende University-Website.

Benutzer können anzeigen und Studenten, Kurs und Instructor-Informationen aktualisieren. Hier sind einige der Bildschirme, die im Lernprogramm erstellt.

![Indexseite für Studenten](intro/_static/students-index.png)

![Studenten bearbeiten (Seite)](intro/_static/student-edit.png)

Den Stil der Benutzeroberfläche von dieser Website liegt nahe bei was von den integrierten Vorlagen generiert wird. Das Lernprogramm Schwerpunkt liegt auf dem EF-Core mit Razor-Pages, nicht die Benutzeroberfläche.

## <a name="create-a-razor-pages-web-app"></a>Erstellen Sie eine Web-app mit Razor-Pages

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Erstellen Sie eine neue ASP.NET Core-Webanwendung. Nennen Sie das Projekt **ContosoUniversity**. Es ist wichtig, den Projektnamen *ContosoUniversity* daher die Namespaces überein, wenn Code kopieren/einfügen.
 ![neue ASP.NET Core-Webanwendung](intro/_static/np.png)
* Wählen Sie in der Dropdownliste **ASP.NET Core 2.0** aus, und klicken Sie anschließend auf **Webanwendung**.
 ![Webanwendung (Razor-Pages)](../../mvc/razor-pages/index/_static/np2.png)

Drücken Sie **F5**, um die App im Debugmodus auszuführen, oder **STRG+F5** zur Ausführung ohne Anfügen des Debuggers.

## <a name="set-up-the-site-style"></a>Richten Sie den Website-Stil

Einige Änderungen richten Sie das Menü "Vorschauwebsite", die Layout und die Startseite.

Open *Pages/_Layout.cshtml* und die folgenden Änderungen vornehmen:

* Ändern von jedem Vorkommen von "ContosoUniversity" in "Contoso-Universität." Es gibt drei Vorkommen.

* Fügen Sie im Menüeinträge für **Studenten**, **Kurse**, **Lehrkräfte**, und **Abteilungen**, und löschen Sie die **Kontakt** Menüeintrag.

Die Änderungen werden hervorgehoben. (Alle Markup ist *nicht* angezeigt.)

[!code-html[](intro/samples/cu/Pages/_Layout.cshtml?highlight=6,29,35-38,47&range=1-50)]

In *Pages/Index.cshtml*, ersetzen Sie den Inhalt der Datei mit den folgenden Code hinzu, ersetzt wechseln den Text zu ASP.NET und MVC mit Text über diese app:

[!code-html[](intro/samples/cu/Pages/Index.cshtml)]

Drücken Sie STRG+F5, um das Projekt auszuführen. Die Startseite ist mit Registerkarten erstellt, die in den folgenden Lernprogrammen angezeigt:

![Contoso University-Startseite](intro/_static/home-page.png)

## <a name="create-the-data-model"></a>Erstellen des Datenmodells

Erstellen Sie Entitätsklassen für die Contoso-University-app. Beginnen Sie mit den folgenden drei Entitäten:

![Modelldiagramm Kurs-Registrierung-Student-Daten](intro/_static/data-model-diagram.png)

Es wird eine 1: n-Beziehung zwischen `Student` und `Enrollment` Entitäten. Es wird eine 1: n-Beziehung zwischen `Course` und `Enrollment` Entitäten. Eine Student kann in eine beliebige Anzahl von Kurse registrieren. Ein Kurs kann eine beliebige Anzahl der Schüler in es registriert haben.

In den folgenden Abschnitten wird eine Klasse für jede dieser Entitäten erstellt.

### <a name="the-student-entity"></a>Die Student-Entität

![Diagramm der Student-Entität](intro/_static/student-entity.png)

Erstellen einer *Modelle* Ordner. In der *Modelle* Ordner, erstellen Sie eine Klassendatei namens *Student.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Die `ID` Eigenschaft wird die Primärschlüsselspalte der Datenbanktabelle (DB), die diese Klasse entspricht. Standardmäßig EF Core interpretiert eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.

Die `Enrollments` Eigenschaft ist eine Navigationseigenschaft. Navigationseigenschaften Verknüpfen mit anderen Entitäten, die zu dieser Entität verknüpft sind. In diesem Fall die `Enrollments` Eigenschaft eine `Student entity` enthält alle der `Enrollment` , die verbundenen, Entitäten `Student`. Z. B. wenn eine Student-Zeile in der Datenbank verfügt über zwei verknüpfte Zeilen, die Registrierung der `Enrollments` Navigationseigenschaft enthält diese zwei `Enrollment` Entitäten. Eine verknüpfte `Enrollment` Zeile ist eine Zeile, diese Student Primärschlüsselwert in enthält, der `StudentID` Spalte. Nehmen wir beispielsweise an den Studenten mit ID = 1 verfügt über zwei Zeilen der `Enrollment` Tabelle. Die `Enrollment` Tabelle verfügt über zwei Zeilen mit `StudentID` = 1. `StudentID`ist ein Fremdschlüssel in der `Enrollment` Tabelle, der angibt, die Studenten in die `Student` Tabelle.

Wenn eine Navigationseigenschaft über mehrere Entitäten enthalten kann, die Navigationseigenschaft muss vom Listentyp sein, z. B. `ICollection<T>`. `ICollection<T>`kann angegeben werden, oder ein Typ sein wie z. B. `List<T>` oder `HashSet<T>`. Wenn `ICollection<T>` ist EF Core verwendet, erstellt werden. eine `HashSet<T>` Auflistung standardmäßig. Navigationseigenschaften, die mehrere Entitäten enthalten, stammen von m: n- und 1: n-Beziehungen.

### <a name="the-enrollment-entity"></a>Die Registrierung-Entität

![Diagramm der Enrollment-Entität](intro/_static/enrollment-entity.png)

In der *Modelle* Ordner erstellen *Enrollment.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Die `EnrollmentID` Eigenschaft ist der Primärschlüssel. Diese Entität verwendet die `classnameID` Muster anstelle von `ID` wie die `Student` Entität. In der Regel Entwickler wählen Sie ein Muster und verwenden Sie es in das Datenmodell. In einem späteren Lernprogramm wird mit der ID ohne Classname angezeigt, die zum Implementieren der Vererbung im Datenmodell zu vereinfachen.

Die `Grade` Eigenschaft ist ein `enum`. Das Fragezeichen nach der `Grade` Typdeklaration gibt an, dass die `Grade` Eigenschaft NULL ist zulässig. Eine Klasse, die null ist unterscheidet sich von einer NULL Grade – null bedeutet, dass eine Klasse ist nicht bekannt ist oder noch nicht zugewiesen wurden, noch.

Die `StudentID` Eigenschaft ist ein Fremdschlüssel, und die entsprechende Navigationseigenschaft ist `Student`. Ein `Enrollment` Entität ist mit einem zugeordneten `Student` Entität, damit die Eigenschaft eine einzelnen enthält `Student` Entität. Die `Student` Entität unterscheidet sich von der `Student.Enrollments` Navigationseigenschaft, die enthält mehrere `Enrollment` Entitäten.

Die `CourseID` Eigenschaft ist ein Fremdschlüssel, und die entsprechende Navigationseigenschaft ist `Course`. Ein `Enrollment` Entität ist mit einem zugeordneten `Course` Entität.

EF Core interpretiert eine Eigenschaft als Fremdschlüssel, wenn es heißt `<navigation property name><primary key property name>`. Beispielsweise`StudentID` für die `Student` Navigationseigenschaft, da die `Student` primären Schlüssel der Entität ist `ID`. Fremdschlüsseleigenschaften können auch benannte `<primary key property name>`. Beispielsweise `CourseID` seit der `Course` primären Schlüssel der Entität ist `CourseID`.

### <a name="the-course-entity"></a>Die Kurs-Entität

![Diagramm der Kurs-Entität](intro/_static/course-entity.png)

In der *Modelle* Ordner erstellen *Course.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Die `Enrollments` Eigenschaft ist eine Navigationseigenschaft. Ein `Course` Entität kann sich auf eine beliebige Anzahl von beziehen `Enrollment` Entitäten.

Die `DatabaseGenerated` Attribut ermöglicht der app, geben Sie den Primärschlüssel, sondern mit der Datenbank generiert wurde.

## <a name="create-the-schoolcontext-db-context"></a>Erstellen Sie den Datenbankkontext SchoolContext

Die Hauptklasse, die EF-Kernfunktionalität für ein Datenmodell für die angegebenen Koordinaten wird der DB-Context-Klasse. Der Datenkontext abgeleitet `Microsoft.EntityFrameworkCore.DbContext`. Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind. In diesem Projekt die Klasse heißt `SchoolContext`.

Im Projektordner, erstellen Sie einen Ordner namens *Daten*.

In der *Daten* Ordner erstellen *SchoolContext.cs* durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Dieser Code erstellt eine `DbSet` -Eigenschaft für jede Entitätenmenge. In der EF-Core-Terminologie:

* In der Regel eine Entitätenmenge entspricht einem DB-Tabelle.
* Eine Entität entspricht einer Zeile in der Tabelle.

`DbSet<Enrollment>`und `DbSet<Course>` kann ausgelassen werden. EF Core nimmt diese implizit da die `Student` Entitätsverweise der `Enrollment` Entität, und die `Enrollment` Entitätsverweise der `Course` Entität. Behalten Sie für dieses Lernprogramm `DbSet<Enrollment>` und `DbSet<Course>` in der `SchoolContext`.

Wenn die Datenbank erstellt wird, EF Core Tabellen erstellt, deren Namen identisch mit der `DbSet` Eigenschaftsnamen. Eigenschaftennamen für Sammlungen sind in der Regel im plural (Studenten statt Student). Entwickler streiten, ob Tabellennamen im plural sein soll. Für diese Lernprogramme ist das Standardverhalten durch Angabe von singular Tabellennamen in der DbContext überschrieben. Um im singular Tabellennamen anzugeben, fügen Sie den folgenden hervorgehobenen Code hinzu:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>Registrieren Sie den Kontext mit Abhängigkeitsinjektion

ASP.NET Core enthält [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection). Dienste (z. B. EF Core Datenbankkontext) werden während des Anwendungsstarts Abhängigkeitsinjektion registriert. Komponenten, die diese Dienste (z. B. Razor-Pages) erfordern, werden diese Dienste über Konstruktorparameter bereitgestellt. Der Konstruktorcode, die eine Instanz des DB-Kontext abruft, wird später in diesem Lernprogramm gezeigt.

Zum Registrieren `SchoolContext` als Dienst öffnen *Startup.cs*, und fügen Sie die markierten Zeilen, die die `ConfigureServices` Methode.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

Der Name der Verbindungszeichenfolge übergeben an den Kontext durch Aufrufen einer Methode für eine `DbContextOptionsBuilder` Objekt. Für die lokale Entwicklung der [ASP.NET Core Konfigurationssystem](xref:fundamentals/configuration/index) liest die Verbindungszeichenfolge aus der *appsettings.json* Datei.

Hinzufügen `using` -Anweisungen für `ContosoUniversity.Data` und `Microsoft.EntityFrameworkCore` Namespaces. Erstellen Sie das Projekt.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Öffnen der *appsettings.json* Datei, und fügen Sie eine Verbindungszeichenfolge ein, wie im folgenden Code gezeigt:

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

Verwendet die vorherigen Verbindungszeichenfolge `ConnectRetryCount=0` verhindern [SQLClient](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/sqlclient-for-the-entity-framework) reagiert.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Die Verbindungszeichenfolge gibt eine SQL Server-LocalDB-Datenbank. LocalDB ist eine vereinfachte Version von SQL Server Express-Datenbankmoduls und ist für die Entwicklung von Apps, nicht für Produktionszwecke vorgesehen. LocalDB bedarfsgesteuert gestartet und im Benutzermodus ausgeführt wird, d. h., es ist keine komplexe Konfiguration. Erstellt standardmäßig LocalDB *mdf* DB Dateien in den `C:/Users/<user>` Verzeichnis.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Fügen Sie Code zum Initialisieren der Datenbank mit der Testdaten

EF Core erstellt eine leere DB. In diesem Abschnitt eine *Ausgangswert* Methode geschrieben wird, um sie mit Testdaten auszustatten.

In der *Daten* Ordner, erstellen Sie eine neue Klassendatei mit dem Namen *DbInitializer.cs* und fügen Sie den folgenden Code hinzu:

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Der Code überprüft, ob alle Studenten in der Datenbank vorhanden sind. Wenn keine Schüler in der Datenbank vorhanden sind, wird die Datenbank mit Testdaten mit Anfangsdaten gefüllt. Es lädt Testdaten in Arrays statt `List<T>` Sammlungen, um die Leistung zu optimieren.

Die `EnsureCreated` -Methode erstellt automatisch die Datenbank für den DB-Kontext. Wenn die Datenbank vorhanden ist, `EnsureCreated` zurück, ohne die Datenbank zu ändern.

In *"Program.cs"*, ändern Sie die `Main` Methode, um die folgenden Aktionen ausführen:

* Eine Instanz des DB-Kontext aus der abhängigkeitseinschleusungscontainer abrufen.
* Rufen Sie die Seed-Methode, übergibt Sie an den Kontext.
* Löschen Sie den Kontext aus, wenn Seed-Methode abgeschlossen ist.

Der folgende Code zeigt die aktualisierte *"Program.cs"* Datei.

[!code-csharp[Main](intro/samples/cu/ProgramOriginal.cs?name=snippet)]

Beim ersten der app ausführen wird die Datenbank erstellt und mit Anfangsdaten gefüllt mit Testdaten. Wenn das Datenmodell aktualisiert wird:
* Löschen Sie die Datenbank an.
* Aktualisieren der Seed-Methode.
* Führen Sie die app und eine neue per Seeding hinzugefügte Datenbank wird erstellt. 

In späteren Lernprogrammen wird die Datenbank die Daten ändert, modellieren, ohne zu löschen und Neuerstellen der Datenbank aktualisiert.

<a name="pmc"></a>
## <a name="add-scaffold-tooling"></a>Die Tools sind Gerüst hinzufügen

In diesem Abschnitt wird die Paket-Manager-Konsole (PMC) verwendet, die Visual Studio Code-Generierung Webpaket hinzufügen. Dieses Paket ist für die Ausführung des Gerüstbaumoduls erforderlich.

Öffnen Sie das Menü **Extras**, und wählen Sie **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.

Geben Sie in der Paket-Manager-Konsole (PMC) die folgenden Befehle ein:

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Utils
```

Mit dem vorherige Befehl fügt das NuGet-Pakete in der Datei *.csproj:

[!code-csharp[Main](intro/samples/cu/ContosoUniversity1_csproj.txt?highlight=7-8)]

<a name="scaffold"></a>
## <a name="scaffold-the-model"></a>Das Gerüst für das Modell erstellen

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).
* Führen Sie die folgenden Befehle ein:


 ```console
dotnet restore
dotnet aspnet-codegenerator razorpage -m Student -dc SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
 ```
 
Wenn der folgende Fehler generiert wird:

```text
Unhandled Exception: System.IO.FileNotFoundException: 
Could not load file or assembly 
'Microsoft.VisualStudio.Web.CodeGeneration.Utils, 
Version=2.0.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60'.
The system cannot find the file specified.
```

Führen Sie den Befehl erneut aus, und einen Kommentar am unteren Rand der Seite.

Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).


Erstellen Sie das Projekt. Der Build generiert Fehler wie folgt:

 `1>Pages\Students\Index.cshtml.cs(26,38,26,45): error CS1061: 'SchoolContext' does not contain a definition for 'Student'`

 Globales ändern `_context.Student` auf `_context.Students` (d. h. hinzufügen ein "s" `Student`). 7 Vorkommen gefunden und aktualisiert. Wir hoffen, beheben Sie [dieses Fehlers](https://github.com/aspnet/Scaffolding/issues/633) in der nächsten Version.

[!INCLUDE[model4tbl](../../includes/RP/model4tbl.md)]

 <a name="test"></a>
### <a name="test-the-app"></a>Testen der App

Führen Sie die app, und wählen Sie die **Studenten** Link. Abhängig von der Breite des Browserfensters die **Studenten** Link am oberen Rand der Seite angezeigt wird. Wenn die **Studenten** Link nicht sichtbar ist, klicken Sie auf das Symbol "Navigation" in der oberen rechten Ecke.

![Contoso University-Startseite schmalen](intro/_static/home-page-narrow.png)

Testen der **erstellen**, **bearbeiten**, und **Details** Links.

## <a name="view-the-db"></a>Zeigen Sie die Datenbank an.

Wenn die app gestartet wird, `DbInitializer.Initialize` Aufrufe `EnsureCreated`. `EnsureCreated`erkennt, ob die Datenbank vorhanden ist, und bei Bedarf erstellt. Wenn es befinden sich keine Schüler in der Datenbank zu den `Initialize` Methode fügt den Studenten.

Open **Objekt-Explorer von SQL Server** (SSOX) aus der **Ansicht** Menü in Visual Studio.
Klicken Sie im SSOX, auf **(Localdb) \MSSQLLocalDB > Datenbanken > ContosoUniversity1**.

Erweitern Sie die **Tabellen** Knoten.

Mit der rechten Maustaste die **Student** Tabelle, und klicken Sie auf **Ansichtsdaten** an die erstellten Spalten und Zeilen in die Tabelle eingefügt.

Die *mdf* und *ldf* Datenbankdateien befinden sich in der *C:\Users\\ <yourusername>*  Ordner.

`EnsureCreated`wird auf app-Start aufgerufen, die den folgenden Workflow ermöglicht:

* Löschen Sie die Datenbank an.
* Ändern des DB-Schemas (z. B. Hinzufügen einer `EmailAddress` Feld).
* Führen Sie die App aus.

`EnsureCreated`erstellt eine Datenbank mit der`EmailAddress` Spalte.

## <a name="conventions"></a>Konventionen

Der Anteil am Code in der Reihenfolge für EF Core zum Erstellen einer vollständigen Datenbank ist aufgrund der Verwendung der Konventionen oder die Annahmen, die EF Core gerichteten minimal.

* Die Namen der `DbSet` Eigenschaften dienen als Tabellennamen auf Richtigkeit. Für Entitäten, die keinen Verweis durch einen `DbSet` Eigenschaft Entitätsklasse Namen als Tabellennamen verwendet werden.

* Entitätsnamen-Eigenschaft werden für Spaltennamen verwendet.

* Eigenschaften der Entität, die ID oder ClassnameID benannt sind, werden als Eigenschaften des Primärschlüssels erkannt.

* Eine Eigenschaft wird als eine Fremdschlüsseleigenschaft interpretiert, wenn es heißt  *<navigation property name> <primary key property name>*  (z. B. `StudentID` für die `Student` Navigationseigenschaft seit der `Student` primären Schlüssel der Entität ist `ID`). Können den Namen von Fremdschlüsseleigenschaften  *<primary key property name>*  (z. B. `EnrollmentID` seit der `Enrollment` primären Schlüssel der Entität ist `EnrollmentID`).

Üblichem Verhalten kann überschrieben werden. Beispielsweise können die Tabellennamen explizit angegeben werden wie zuvor in diesem Lernprogramm gezeigt. Die Spaltennamen können explizit festgelegt werden. Primärschlüssel und Fremdschlüssel können explizit festgelegt werden.

## <a name="asynchronous-code"></a>Asynchronen code

Asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.

Ein Webserver verfügt über eine begrenzte Anzahl von Threads zur Verfügung stehen, und in Situationen mit hoher Last möglicherweise alle verfügbaren Threads verwendet. In diesem Fall kann der Server neue Anforderungen nicht verarbeiten, bis die Threads freigegeben werden. Mit synchroner Code möglicherweise viele Threads einrichten gebunden werden, während sie tatsächlich alle Aufgaben durchführt werden nicht, da für e/a auf Abschluss warten, können sie. Wenn ein Prozess für e/a auf den Abschluss wartet, wird der Thread mit asynchronen Code für den Server für die Verarbeitung anderer Anforderungen verwenden freigegeben. Folglich asynchronen Code ermöglicht es, Server-Ressourcen effizienter genutzt werden, und der Server ist aktiviert, um weitere ohne Verzögerungen-Datenverkehr zu bewältigen.

Asynchronen Code zur Laufzeit eine kleine Menge an Mehraufwand führen. Mit niedrigem Datenverkehr in Situationen die Leistungseinbußen sind zu vernachlässigen, während für Situationen mit hohem Datenverkehr, der möglicher Leistungssteigerungen erhebliche.

Im folgenden Code wird die `async` -Schlüsselwort, `Task<T>` Rückgabewert, `await` -Schlüsselwort, und `ToListAsync` Methode stellen den Code, der asynchron ausgeführt werden soll.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Die `async` Schlüsselwort weist den Compiler an:

  * Generieren Sie Rückrufe für Teile des Methodentextes.
  * Erstellen Sie automatisch die [Aufgabe](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task?view=netframework-4.7) -Objekt, das zurückgegeben wird. Weitere Informationen finden Sie unter [Rückgabetyp Task](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Die implizite Rückgabetyp `Task` stellt derzeit ausgeführte Arbeit dar.

* Die `await` Schlüsselwort weist den Compiler an die Methode in zwei Teile geteilt. Der erste Teil endet mit dem Vorgang, der asynchron gestartet wird. Der zweite Teil ist eine Rückrufmethode abgelegt, die aufgerufen wird, wenn der Vorgang abgeschlossen ist.

* `ToListAsync`ist die asynchrone Version der `ToList` -Erweiterungsmethode.

Einige Aspekte, die beim asynchronen Code schreiben, die mithilfe von EF Core geachtet werden:

* Nur die Anweisungen, die dazu führen, dass Abfragen oder Befehle an die Datenbank gesendet werden, werden asynchron ausgeführt. Umfasst, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, und `SaveChangesAsync`. Dieser umfasst jedoch nicht Anweisungen auszuführen, ändern nur, eine `IQueryable`, wie z. B. `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Ein Kontext EF Core ist nicht threadsicher: nicht Versuch, das mehrere Vorgänge parallel auszuführen. 

* Um die Leistungsvorteile von asynchronem Code nutzen zu können, stellen Sie sicher, dass die Bibliothek Pakete (z. B. Paging) Async verwenden, wenn sie EF-Core-Methoden aufrufen, die Abfragen mit der Datenbank zu senden.

Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Übersicht über die asynchrone](https://docs.microsoft.com/dotnet/articles/standard/async).

In den nächsten Lernprogrammen, grundlegende CRUD (erstellen, lesen, aktualisieren und löschen) Vorgänge werden überprüft.

>[!div class="step-by-step"]
[Nächste](xref:data/ef-rp/crud)
