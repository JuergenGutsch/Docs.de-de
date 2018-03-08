---
title: ASP.NET Core MVC mit Entity Framework Core - 10-Lernprogramm 1
author: tdykstra
description: 
keywords: ASP.NET Core, Entity Framework Core-Lernprogramm
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: b67c3d4a-f2bf-4132-a48b-4b0d599d7981
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/intro
ms.openlocfilehash: 379802f644b977563b0b50354feb1fb9a4c8fabb
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="getting-started-with-aspnet-core-mvc-and-entity-framework-core-using-visual-studio-1-of-10"></a>Erste Schritte mit ASP.NET Core MVC und Entity Framework Core mithilfe von Visual Studio (1 von 10)

Durch [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Eine Razor-Pages Version dieses Lernprogramms steht [hier](xref:data/ef-rp/intro). Die Version für Razor-Pages ist leichter zu verstehen und deckt mehr EF-Features ab. Es wird empfohlen, Sie führen Sie die [Razor-Pages Version dieses Lernprogramms](xref:data/ef-rp/intro).

Die Contoso-University Beispielwebanwendung veranschaulicht, wie ASP.NET Core 2.0 MVC-Webanwendungen, die mit Entity Framework (EF) Core 2.0 und Visual Studio 2017.

Die beispielanwendung ist eine Website für eine fiktive Contoso-Universität. Es umfasst Funktionen wie Student Zulassung, Kurs Erstellung und Instructor-Zuweisungen. Dies ist die erste Aufgabe in eine Reihe von Lernprogrammen, die erläutern, wie die beispielanwendung Contoso Universität von Grund auf neu erstellt.

[Herunterladen oder Anzeigen der fertigen Anwendung.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

EF Core 2.0 ist die neueste Version von EF jedoch alle Funktionen von EF noch keine 6.x. Informationen zur Wahl zwischen EF 6.x und EF-Kern, finden Sie unter [EF Core Vs. EF6.x](https://docs.microsoft.com/ef/efcore-and-ef6/). Bei Auswahl von EF 6.x ausführen, finden Sie unter [die vorherige Version dieser Reihe von Lernprogrammen](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

> [!NOTE]
> * Die Version 1.1 von ASP.NET Core dieses Lernprogramms finden Sie unter der [VS 2017 Update 2 Version dieses Lernprogramms im PDF-Format](https://github.com/aspnet/Docs/blob/master/aspnetcore/data/ef-mvc/intro/_static/efmvc1.1.pdf).
> * Die Version dieses Tutorials für Visual Studio 2015 finden Sie unter [Visual Studio 2015-Version der ASP.NET Core-Dokumentation im PDF-Format](https://github.com/aspnet/Docs/blob/master/aspnetcore/common/_static/aspnet-core-project-json.pdf).

## <a name="prerequisites"></a>Erforderliche Komponenten

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie ein Problem Sie können nicht aufgelöst werden auftreten, im Allgemeinen finden Sie die Projektmappe durch Vergleichen des Codes: auf die [abgeschlossenes Projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Eine Übersicht über häufige Fehler und deren Lösung finden Sie unter [im Abschnitt Problembehandlung der im letzten Lernprogramm in der Reihe](advanced.md#common-errors). Wenn Sie nicht finden, was Sie es benötigen, können Sie selbst eine Frage veröffentlichen, um StackOverflow.com für [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP] 
> Dies ist eine Reihe von 10 Lernprogramme, von denen jede baut auf was in früheren Lernprogramme erfolgt. Denken Sie speichern eine Kopie des Projekts nach jeder erfolgreichen Abschluss des Lernprogramms. Klicken Sie dann, wenn Probleme auftreten, können Sie über aus dem vorherigen Lernprogramm nicht zurück auf den Anfang die gesamte Reihe starten.

## <a name="the-contoso-university-web-application"></a>Die Contoso-University-Webanwendung

Die Anwendung, die Sie in diesen Lernprogrammen erstellen werden müssen ist eine einfache University-Website.

Benutzer können anzeigen und Studenten, Kurs und Instructor-Informationen aktualisieren. Hier sind einige der Bildschirme, die Sie erstellen müssen.

![Indexseite für Studenten](intro/_static/students-index.png)

![Studenten bearbeiten (Seite)](intro/_static/student-edit.png)

Den Stil der Benutzeroberfläche von diesem Standort wurde in der Nähe was von den integrierten Vorlagen generiert wird beibehalten, damit das Lernprogramm in erster Linie für die Verwendung von Entity Framework konzentrieren kann.

## <a name="create-an-aspnet-core-mvc-web-application"></a>Erstellen Sie eine ASP.NET Core MVC-Webanwendung

Öffnen Sie Visual Studio, und erstellen Sie ein neues ASP.NET Core C#-Webprojekt mit dem Namen "ContosoUniversity".

* Aus der **Datei** klicken Sie im Menü **neu > Projekt**.

* Wählen Sie im linken Bereich **installiert > Visual c# > Web**.

* Wählen Sie die Projektvorlage **ASP.NET Core-Webanwendung** aus.

* Geben Sie **ContosoUniversity** als ein, und klicken Sie auf **OK**.

  ![Dialogfeld "Neues Projekt"](intro/_static/new-project.png)

* Warten Sie, den **neue ASP.NET-Webanwendung mit Core (.NET Core)** Dialogfeld angezeigt werden

* Wählen Sie **ASP.NET Core 2.0** und **Webanwendung (Model-View-Controller)** Vorlage.

  **Hinweis:** dieses Lernprogramm erfordert ASP.NET Core 2.0 und EF Core 2.0 oder höher--stellen sicher, dass **ASP.NET Core 1.1** ist nicht aktiviert.

* Stellen Sie sicher, dass **Authentifizierung** festgelegt ist, um **keine Authentifizierung**.

* Klicken Sie auf **OK**

  ![Dialogfeld „Neues ASP.NET-Projekt“](intro/_static/new-aspnet.png)

## <a name="set-up-the-site-style"></a>Richten Sie den Website-Stil

Einige einfache Änderungen werden das Menü "Vorschauwebsite", die Layout und die Startseite einrichten.

Open *Views/Shared/_Layout.cshtml* und die folgenden Änderungen vornehmen:

* Ändern Sie jedem Vorkommen von "ContosoUniversity" in "Contoso University". Es gibt drei Vorkommen.

* Fügen Sie im Menüeinträge für **Studenten**, **Kurse**, **Lehrkräfte**, und **Abteilungen**, und löschen Sie die **Kontakt** Menüeintrag.

Die Änderungen werden hervorgehoben.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,30,36-39,48)]

In *Views/Home/Index.cshtml*, ersetzen Sie den Inhalt der Datei mit den folgenden Code hinzu, ersetzt wechseln den Text zu ASP.NET und MVC mit Text zu dieser Anwendung:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Drücken Sie STRG + F5, um das Projekt auszuführen, oder wählen **Debuggen > Starten ohne Debugging** aus dem Menü. Sie finden Sie auf der Startseite mit Registerkarten für die Seiten, die Sie in diesen Lernprogrammen erstellen müssen.

![Contoso University-Startseite](intro/_static/home-page.png)

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework Core NuGet-Pakete

Um ein Projekt EF-Core-Unterstützung hinzugefügt haben, installieren Sie den Datenbankanbieter, den verwendet werden soll. Dieses Lernprogramm verwendet SQL Server und anbieterpakets [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Dieses Paket ist in enthalten die [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) Metapackage, daher ist es nicht, es zu installieren.

Dieses Paket und seine Abhängigkeiten (`Microsoft.EntityFrameworkCore` und `Microsoft.EntityFrameworkCore.Relational`) bieten Common Language Runtime-Unterstützung für EF. Fügen Sie ein Paket die Tools sind weiter unten in der [Migrationen](migrations.md) Lernprogramm. 

Informationen zu anderen Datenbankanbieter, die für Entity Framework Core verfügbar sind, finden Sie unter [Datenbankanbieter](https://docs.microsoft.com/ef/core/providers/).

## <a name="create-the-data-model"></a>Erstellen des Datenmodells

Als Nächstes erstellen Sie für die Anwendung von Contoso University Entitätsklassen. Beginnen Sie mit den folgenden drei Entitäten.

![Modelldiagramm Kurs-Registrierung-Student-Daten](intro/_static/data-model-diagram.png)

Es wird eine 1: n-Beziehung zwischen `Student` und `Enrollment` Entitäten, und es wird eine 1: n-Beziehung zwischen `Course` und `Enrollment` Entitäten. Das heißt, eine Student kann in einer beliebigen Anzahl von Kursen registriert sein, und ein Kurs kann eine beliebige Anzahl der Schüler in es registriert haben.

In den folgenden Abschnitten erstellen Sie eine Klasse für jede dieser Entitäten.

### <a name="the-student-entity"></a>Die Student-Entität

![Diagramm der Student-Entität](intro/_static/student-entity.png)

In der *Modelle* Ordner, erstellen Sie eine Klassendatei namens *Student.cs* , und Ersetzen Sie den Code durch den folgenden Code.

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Die `ID` Eigenschaft werden die Primärschlüsselspalte der Datenbanktabelle, die diese Klasse entspricht. Wird standardmäßig das Entity Framework interpretiert eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.

Die `Enrollments` Eigenschaft ist eine Navigationseigenschaft. Navigationseigenschaften halten andere Entitäten, die mit dieser Entität verknüpft sind. In diesem Fall die `Enrollments` Eigenschaft eine `Student entity` enthält alle der `Enrollment` , die verbundenen, Entitäten `Student` Entität. Das heißt, wenn eine bestimmte Student-Zeile in der Datenbank verfügt über zwei verknüpfte Anmeldung Zeilen (Zeilen, die diese Student Primärschlüsselwert in ihre StudentID-Fremdschlüsselspalte enthalten), `Student` Entität `Enrollments` Navigationseigenschaft enthält die zwei `Enrollment` Entitäten.

Wenn eine Navigationseigenschaft über mehrere Entitäten (wie in der m: n- oder 1: n-Beziehungen) enthalten kann, muss dessen Typ eine Liste, in dem Einträge können hinzugefügt, gelöscht, und aktualisiert, z. B. `ICollection<T>`. Sie können angeben, `ICollection<T>` oder ein Typ sein wie z. B. `List<T>` oder `HashSet<T>`. Bei Angabe von `ICollection<T>`, EF erstellt eine `HashSet<T>` Auflistung standardmäßig.

### <a name="the-enrollment-entity"></a>Die Registrierung-Entität

![Diagramm der Enrollment-Entität](intro/_static/enrollment-entity.png)

In der *Modelle* Ordner erstellen *Enrollment.cs* und Ersetzen Sie den vorhandenen Code durch folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Die `EnrollmentID` -Eigenschaft wird der Primärschlüssel sein; diese Entität verwendet die `classnameID` Muster anstelle von `ID` von sich selbst als Sie gesehen haben, der `Student` Entität. Sie würden normalerweise wählen Sie ein Muster und verwenden sie während des gesamten Datenmodells. Hier veranschaulicht die Variation an, dass Sie entweder Muster verwenden können. In einem [späteren Lernprogramm](inheritance.md), sehen Sie, wie mit der ID ohne Classname Implementieren der Vererbung im Datenmodell vereinfacht.

Die `Grade` Eigenschaft ist ein `enum`. Das Fragezeichen nach der `Grade` Typdeklaration gibt an, dass die `Grade` Eigenschaft NULL ist zulässig. Eine Klasse, die null ist unterscheidet sich von einer NULL Grade – null bedeutet, dass eine Klasse ist nicht bekannt ist oder noch nicht zugewiesen wurden, noch.

Die `StudentID` Eigenschaft ist ein Fremdschlüssel, und die entsprechende Navigationseigenschaft ist `Student`. Ein `Enrollment` Entität bezieht sich auf eine `Student` Entität, damit die Eigenschaft nur ein einzelnes enthalten darf `Student` Entität (im Gegensatz zu den `Student.Enrollments` Navigationseigenschaft gelernt früher mehrere halten `Enrollment` Entitäten).

Die `CourseID` Eigenschaft ist ein Fremdschlüssel, und die entsprechende Navigationseigenschaft ist `Course`. Ein `Enrollment` Entität ist mit einem zugeordneten `Course` Entität.

Entity Framework interpretiert eine Eigenschaft als eine foreign Key-Eigenschaft, wenn es heißt `<navigation property name><primary key property name>` (z. B. `StudentID` für die `Student` Navigationseigenschaft seit der `Student` primären Schlüssel der Entität ist `ID`). Fremdschlüsseleigenschaften können auch einfach namens `<primary key property name>` (z. B. `CourseID` seit der `Course` primären Schlüssel der Entität ist `CourseID`).

### <a name="the-course-entity"></a>Die Kurs-Entität

![Diagramm der Kurs-Entität](intro/_static/course-entity.png)

In der *Modelle* Ordner erstellen *Course.cs* und Ersetzen Sie den vorhandenen Code durch folgenden Code:

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Die `Enrollments` Eigenschaft ist eine Navigationseigenschaft. Ein `Course` Entität kann sich auf eine beliebige Anzahl von beziehen `Enrollment` Entitäten.

Z. B. Weitere Informationen zu den `DatabaseGenerated` Attribut in einer [späteren Lernprogramm](complex-data-model.md) in dieser Serie. Im Grunde, Sie können dieses Attribut geben Sie den primären Schlüssel für den Kurs, anstatt die Datenbank, die sie generieren.

## <a name="create-the-database-context"></a>Erstellen Sie den Datenbankkontext

Die Hauptklasse, die Entity Framework-Funktionen für ein Datenmodell für die angegebenen Koordinaten wird der Datenbank-Context-Klasse. Sie können diese Klasse durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellen. Im Code Geben Sie an, welche Entitäten im Datenmodell enthalten sind. Sie können auch bestimmte Entity Framework-Verhalten anpassen. In diesem Projekt die Klasse heißt `SchoolContext`.

Im Projektordner, erstellen Sie einen Ordner namens *Daten*.

In der *Daten* Ordner erstellen Sie eine neue Klassendatei mit dem Namen *SchoolContext.cs*, und Ersetzen Sie den Code durch den folgenden Code:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Dieser Code erstellt eine `DbSet` -Eigenschaft für jede Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.

Haben Sie ausgelassen der `DbSet<Enrollment>` und `DbSet<Course>` -Anweisungen, und es würde funktionieren identisch. Das Entity Framework zählen sie implizit da die `Student` Entitätsverweise der `Enrollment` Entität und die `Enrollment` Entitätsverweise der `Course` Entität.

Wenn die Datenbank erstellt wird, EF Tabellen erstellt, deren Namen identisch mit der `DbSet` Eigenschaftsnamen. Eigenschaftennamen für Sammlungen sind in der Regel Plural (Studenten statt Student), aber Entwickler streiten, ob Tabellennamen oder nicht pluralisiert soll. Für diese Lernprogramme müssen Sie das Standardverhalten überschreiben, indem singular Tabellennamen in der ' DbContext ' angeben. Zu diesem Zweck fügen Sie folgenden hervorgehobenen Code hinzu, nach der letzten DbSet-Eigenschaft.

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>Registrieren Sie den Kontext mit Abhängigkeitsinjektion

ASP.NET Core implementiert [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) standardmäßig. Dienste (z. B. den Datenbankkontext "EF") werden während des Anwendungsstarts Abhängigkeitsinjektion registriert. Komponenten, die diese Dienste (z. B. MVC-Controller) erfordern, werden diese Dienste über Konstruktorparameter bereitgestellt. Den Konstruktor controllercode angezeigt, der weiter unten in diesem Lernprogramm eine Kontextinstanz abruft.

Zum Registrieren `SchoolContext` als Dienst öffnen *Startup.cs*, und fügen Sie die markierten Zeilen, die die `ConfigureServices` Methode.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

Der Name der Verbindungszeichenfolge übergeben an den Kontext durch Aufrufen einer Methode für eine `DbContextOptionsBuilder` Objekt. Für die lokale Entwicklung der [ASP.NET Core Konfigurationssystem](xref:fundamentals/configuration/index) liest die Verbindungszeichenfolge aus der *appsettings.json* Datei.

Hinzufügen `using` -Anweisungen für `ContosoUniversity.Data` und `Microsoft.EntityFrameworkCore` Namespaces, und erstellen Sie das Projekt.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Öffnen der *appsettings.json* Datei, und fügen Sie eine Verbindungszeichenfolge ein, wie im folgenden Beispiel gezeigt.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Die Verbindungszeichenfolge gibt eine SQL Server LocalDB-Datenbank. LocalDB ist eine vereinfachte Version von SQL Server Express-Datenbankmoduls und ist für die Anwendungsentwicklung, nicht für Produktionszwecke vorgesehen. LocalDB bedarfsgesteuert gestartet und im Benutzermodus ausgeführt wird, d. h., es ist keine komplexe Konfiguration. Erstellt standardmäßig LocalDB *mdf* -Datenbankdateien, die in der `C:/Users/<user>` Verzeichnis.

## <a name="add-code-to-initialize-the-database-with-test-data"></a>Fügen Sie Code zum Initialisieren der Datenbank mit Testdaten

Das Entity Framework wird eine leere Datenbank erstellt werden. In diesem Abschnitt schreiben Sie eine Methode, die aufgerufen wird, nachdem die Datenbank erstellt wird, um sie mit Testdaten auszustatten.

Verwenden Sie hier die `EnsureCreated` Methode, um die Datenbank automatisch zu erstellen. In einem [späteren Lernprogramm](migrations.md) sehen Sie, wie Sie modelländerungen behandeln, indem Sie Code First-Migrationen verwenden, um das Datenbankschema statt löschen und Neuerstellen der Datenbank zu ändern.

In der *Daten* Ordner, erstellen Sie eine neue Klassendatei mit dem Namen *DbInitializer.cs* und Ersetzen Sie durch den folgenden Code, wodurch eine Datenbank erstellt werden, bei Bedarf den Vorlagencode und lädt Daten in das neue testen die Datenbank.

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Der Code überprüft, ob alle Studenten in der Datenbank vorhanden sind und falls nicht, es wird vorausgesetzt die Datenbank ist neu und mit Testdaten ausgeführt werden muss. Es lädt Testdaten in Arrays statt `List<T>` Sammlungen, um die Leistung zu optimieren.

In *"Program.cs"*, ändern Sie die `Main` Methode, um die folgenden beim Start der Anwendung auszuführen:

* Die Kontextinstanz eine Datenbank aus der abhängigkeitseinschleusungscontainer abrufen.
* Rufen Sie die Seed-Methode, übergibt Sie an den Kontext.
* Löschen Sie den Kontext aus, wenn der Seed-Methode abgeschlossen ist.

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Hinzufügen `using` Anweisungen:

[!code-csharp[Main](intro/samples/cu/Program.cs?name=snippet_Usings)]

In älteren Lernprogrammen möglicherweise ähnlichen Code in der `Configure` Methode im *Startup.cs*. Wir empfehlen die Verwendung der `Configure` -Methode nur zum Einrichten der Anforderungspipeline. Anwendungsstartcode ist Teil der `Main` Methode.

Jetzt beim ersten der Anwendung ausführen die Datenbank werden erstellt und mit Anfangsdaten gefüllt mit Testdaten. Sobald Sie Ihr Datenmodell ändern, können Sie die Datenbank löschen, aktualisieren die Seed-Methode und beginnen mit einer neuen Datenbank die gleiche Weise. In späteren Lernprogrammen sehen Sie, wie die Datenbank zu ändern, wenn die Daten ändert, modellieren, ohne zu löschen und neu erstellt wird.

## <a name="create-a-controller-and-views"></a>Erstellen Sie einen Controller und Ansichten

Als Nächstes verwenden Sie das Gerüst-Modul in Visual Studio ein MVC-Controller und Ansichten, mit denen werden EF abzufragen, und Speichern von Daten hinzufügen.

Die automatische Erstellung von CRUD-Aktionsmethoden und Ansichten wird als Gerüstbau bezeichnet. Gerüstbau unterscheidet sich von codegenerierung, scaffolded Code als Ausgangspunkt, den Sie ändern können ist, um Ihren eigenen Anforderungen anzupassen, wohingegen generierten Code in der Regel nicht ändern. Wenn Sie generierten Code anzupassen, verwenden Sie partielle Klassen oder den Code generieren, wenn Dinge ändern.

* Mit der rechten Maustaste die **Controller** Ordner **Projektmappen-Explorer** , und wählen Sie **hinzufügen > Neues Gerüstelement**.

Wenn das Dialogfeld **MVC-Abhängigkeiten hinzufügen** angezeigt wird, gehen Sie wie folgt vor:

* [Aktualisieren Sie Visual Studio auf die neuste Version.](https://www.visualstudio.com/downloads/) Dieses Dialogfeld wird in allen Visual Studio Versionen vor Version 15.5 angezeigt.
* Wenn Sie kein Update ausführen können, klicken Sie auf **Hinzufügen**, und führen Sie die Schritte zum Hinzufügen eines Controllers erneut aus.

* In der **Gerüst hinzufügen** (Dialogfeld):

  * Wählen Sie **MVC-Controller mit Ansichten unter Verwendung von Entity Framework**.

  * Klicken Sie auf **Hinzufügen**.

* In der **Controller hinzufügen** (Dialogfeld):

  * In **Modellschemas** wählen **Student**.

  * In **Datenkontextklasse** wählen **SchoolContext**.

  * Übernehmen Sie den Standardnamen **StudentsController** als Namen.

  * Klicken Sie auf **Hinzufügen**.

  ![Gerüst Student](intro/_static/scaffold-student.png)

  Beim Klicken auf **hinzufügen**, das Visual Studio Gerüstbau Modul erstellt eine *StudentsController.cs* Datei- und eine Reihe von Sichten (*cshtml* Dateien), die mit dem Controller funktioniert.

(Das Gerüst-Modul kann den Datenbankkontext auch für Sie erstellen werden, wenn Sie es nicht manuell erstellen zunächst, wie Sie weiter oben in diesem Lernprogramm ausgeführt haben. Sie können angeben, dass eine neue Context-Klasse in der **Controller hinzufügen** Feld, indem Sie auf das Pluszeichen, um rechts von **Datenkontextklasse**.  Klicken Sie dann Visual Studio erstellt die `DbContext` -Klasse und der Controller und Ansichten.)

Sie werden feststellen, dass der Controller übernimmt eine `SchoolContext` als Konstruktorparameter.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Abhängigkeitsinjektion übernimmt der Übergabe einer Instanz von `SchoolContext` in den Controller. Sie konfiguriert haben, die in der *Startup.cs* Datei weiter oben.

Der Controller enthält ein `Index` Aktionsmethode, die alle Teilnehmer in der Datenbank angezeigt. Die Methode ruft eine Liste der Schüler aus der Entitätssammlung Studenten durch Lesen der `Students` Eigenschaft des Context-Datenbankinstanz:

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Sie erfahren über die asynchrone Programmierung Elemente in diesem Code später im Lernprogramm.

Die *Views/Students/Index.cshtml* zeigt diese Liste in einer Tabelle:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Drücken Sie STRG + F5, um das Projekt auszuführen, oder wählen **Debuggen > Starten ohne Debugging** aus dem Menü.

Klicken Sie auf der Registerkarte "Studenten", um die Testdaten anzuzeigen, die die `DbInitializer.Initialize` Methode eingefügt. Je nachdem wie schmale Ihres Browserfensters sehen Sie den `Student` Link Registerkarte am oberen Rand der Seite ", oder Sie müssen sich auf das Symbol" Navigation "in der oberen rechten Ecke auf den Link klicken.

![Contoso University-Startseite schmalen](intro/_static/home-page-narrow.png)

![Indexseite für Studenten](intro/_static/students-index.png)

## <a name="view-the-database"></a>Zeigen Sie die Datenbank an.

Wenn vor dem Start der Anwendung, die `DbInitializer.Initialize` Methodenaufrufe `EnsureCreated`. EF gesehen haben, dass es keine Datenbank wurde, und daher eine, und klicken Sie dann den Rest der Erstellung der `Initialize` Methodencode aufgefüllt, die Datenbank mit Daten. Sie können **Objekt-Explorer von SQL Server** (SSOX) zur Anzeige der Datenbank in Visual Studio.

Schließen Sie den Browser.

Wenn die SSOX-Fenster nicht bereits geöffnet ist, wählen Sie diese aus der **Ansicht** Menü in Visual Studio.

SSOX, klicken Sie auf **(Localdb) \MSSQLLocalDB > Datenbanken**, und klicken Sie dann auf den Eintrag für den Datenbanknamen, die in der Verbindungszeichenfolge in Ihrer *appsettings.json* Datei.

Erweitern Sie die **Tabellen** Knoten, um die Tabellen in der Datenbank finden Sie unter.

![Tabellen in SSOX](intro/_static/ssox-tables.png)

Mit der rechten Maustaste die **Student** Tabelle, und klicken Sie auf **Ansichtsdaten** , finden in den Spalten, die erstellt wurden und die Zeilen, die in die Tabelle eingefügt wurden.

![Studententabelle in SSOX](intro/_static/ssox-student-table.png)

Die *mdf* und *ldf* -Datenbankdateien befinden sich in der *C:\Users\<IhrBenutzername >* Ordner.

Da Sie aufrufen `EnsureCreated` in der Initialisierermethode, die auf app-Start ausgeführt wird, können Sie nun eine Änderung an treffen der `Student` -Klasse, löschen Sie die Datenbank, die Anwendung erneut auszuführen und die Datenbank automatisch neu erstellt, die Änderung entsprechend wäre. Angenommen, Sie fügen ein `EmailAddress` Eigenschaft, um die `Student` -Klasse, sehen Sie ein neues `EmailAddress` Spalte in der Tabelle neu erstellt.

## <a name="conventions"></a>Konventionen

Die Menge an Code, die in Reihenfolge für das Entity Framework zum Erstellen einer vollständigen Datenbank können voraussetzten ist aufgrund der Verwendung von Konventionen oder Annahmen, die das Entity Framework stellt minimal.

* Die Namen der `DbSet` Eigenschaften dienen als Tabellennamen auf Richtigkeit. Für Entitäten, die keinen Verweis durch einen `DbSet` Eigenschaft Entitätsklasse Namen als Tabellennamen verwendet werden.

* Entitätsnamen-Eigenschaft werden für Spaltennamen verwendet.

* Eigenschaften der Entität, die ID oder ClassnameID benannt sind, werden als Eigenschaften des Primärschlüssels erkannt.

* Eine Eigenschaft wird als eine Fremdschlüsseleigenschaft interpretiert, wenn es heißt  *<navigation property name> <primary key property name>*  (z. B. `StudentID` für die `Student` Navigationseigenschaft seit der `Student` primären Schlüssel der Entität ist `ID`). Fremdschlüsseleigenschaften können auch einfach namens  *<primary key property name>*  (z. B. `EnrollmentID` seit der `Enrollment` primären Schlüssel der Entität ist `EnrollmentID`).

Üblichem Verhalten kann überschrieben werden. Beispielsweise können Sie Tabellennamen, Spaltennamen, explizit angeben, wie Sie weiter oben in diesem Lernprogramm gesehen haben. Und Sie können die Spaltennamen und eine Eigenschaft als primary key- oder foreign Key festlegen, wie Sie sehen werden eine [späteren Lernprogramm](complex-data-model.md) in dieser Serie.

## <a name="asynchronous-code"></a>Asynchronen code

Asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.

Ein Webserver verfügt über eine begrenzte Anzahl von Threads zur Verfügung stehen, und in Situationen mit hoher Last möglicherweise alle verfügbaren Threads verwendet. In diesem Fall kann der Server neue Anforderungen nicht verarbeiten, bis die Threads freigegeben werden. Mit synchroner Code möglicherweise viele Threads einrichten gebunden werden, während sie tatsächlich alle Aufgaben durchführt werden nicht, da für e/a auf Abschluss warten, können sie. Wenn ein Prozess für e/a auf den Abschluss wartet, wird der Thread mit asynchronen Code für den Server für die Verarbeitung anderer Anforderungen verwenden freigegeben. Folglich asynchronen Code ermöglicht es, Server-Ressourcen effizienter genutzt werden, und der Server ist aktiviert, um weitere ohne Verzögerungen-Datenverkehr zu bewältigen.

Asynchronen Code eine kleine Menge an Mehraufwand einführen, zur Laufzeit, aber die Leistungseinbußen bei unerheblich, während für Situationen mit hohem Datenverkehr ist mit niedrigem Datenverkehr Situationen möglicher Leistungssteigerungen erhebliche ist.

Im folgenden Code wird die `async` -Schlüsselwort, `Task<T>` Rückgabewert, `await` -Schlüsselwort, und `ToListAsync` Methode stellen den Code, der asynchron ausgeführt werden soll.

[!code-csharp[Main](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Die `async` -Schlüsselwort weist den Compiler an, um Rückrufe für Teile des Methodentextes zu generieren und zum automatischen Erstellen der `Task<IActionResult>` -Objekt, das zurückgegeben wird.

* Der Rückgabetyp `Task<IActionResult>` stellt derzeit ausgeführte Arbeit mit einem Ergebnis vom Typ `IActionResult`.

* Die `await` Schlüsselwort weist den Compiler an die Methode in zwei Teile geteilt. Der erste Teil endet mit dem Vorgang, der asynchron gestartet wird. Der zweite Teil ist eine Rückrufmethode abgelegt, die aufgerufen wird, wenn der Vorgang abgeschlossen ist.

* `ToListAsync`ist die asynchrone Version der `ToList` -Erweiterungsmethode.

Einige Dinge zu beachten, wenn Sie asynchronen Code schreiben, der das Entity Framework verwendet werden:

* Nur die Anweisungen, die dazu führen, dass Abfragen oder Befehle an die Datenbank gesendet werden, werden asynchron ausgeführt. Umfasst, z. B. `ToListAsync`, `SingleOrDefaultAsync`, und `SaveChangesAsync`. Dieser umfasst jedoch nicht, z. B. Anweisungen auszuführen, ändern nur, eine `IQueryable`, wie z. B. `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Ein EF-Kontext ist nicht threadsicher: nicht Versuch, das mehrere Vorgänge parallel auszuführen. Wenn Sie jede asynchrone EF-Methode aufrufen, verwenden Sie immer die `await` Schlüsselwort.

* Wenn Sie die Leistungsvorteile von asynchronem Code nutzen, stellen Sie sicher, dass jede Bibliothek von Paketen möchten Sie verwenden (z. B. für die Auslagerungsdatei), asynchrone auch verwenden, wenn sie keine Entity Framework-Methoden aufrufen, die dazu führen, dass Abfragen an die Datenbank gesendet werden.

Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Übersicht über die asynchrone](https://docs.microsoft.com/dotnet/articles/standard/async).

## <a name="summary"></a>Zusammenfassung

Sie haben nun eine einfache Anwendung erstellt, die das Entity Framework Core und SQL Server Express LocalDB zum Speichern und Anzeigen von Daten verwendet. Im folgenden Lernprogramm erfahren Sie zum Ausführen von grundlegenden CRUD (erstellen, lesen, aktualisieren und löschen) Vorgänge.

>[!div class="step-by-step"]
[Nächste](crud.md)
