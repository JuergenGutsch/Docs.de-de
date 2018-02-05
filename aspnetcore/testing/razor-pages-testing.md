---
title: "Einheit für Razor-Pages und Integrationstests zu legen, die in ASP.NET Core"
author: guardrex
description: "Erfahren Sie, wie Komponententests und die Integration von Tests für Razor-Pages-apps erstellen."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: testing/razor-pages-testing
ms.openlocfilehash: 5891b236306cd3790cbba14919796d6aa894ad53
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="razor-pages-unit-and-integration-testing-in-aspnet-core"></a>Einheit für Razor-Pages und Integrationstests zu legen, die in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

ASP.NET Core unterstützt Einheit und Integrationstests für die Razor-Pages-Apps. Testen der Datenzugriffsebene (DAL), Seite "-Modelle und integrierte Page-Komponenten kann sichergestellt werden:

* Teile einer app Razor-Pages arbeiten unabhängig voneinander und zusammen als eine Einheit während der Erstellung der app an.
* Klassen und Methoden haben Bereiche Verantwortung beschränkt.
* Zusätzlicher Dokumentation vorhanden ist, auf wie die Anwendung Verhalten soll.
* Regressionen, die Fehler, die durch das Updates auf den Code zu geschaltet sind, werden während der automatischen Erstellung und Bereitstellung gefunden.

In diesem Thema wird vorausgesetzt, dass Sie ein grundlegendes Verständnis der Razor-Pages-apps, die Komponententests und die Integration testen. Wenn Sie mit Razor-Pages-apps oder testen Konzepten nicht vertraut sind, finden Sie unter den folgenden Themen:

* [Introduction to Razor Pages (Einführung in Razor-Pages)](xref:mvc/razor-pages/index)
* [Getting started with Razor Pages (Erste Schritte mit Razor-Pages)](xref:tutorials/razor-pages/razor-pages-start)
* [UnitTests von c# in .NET Core mit Dotnet Test- und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Integrationstests](xref:testing/integration-testing)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/razor-pages-testing/sample/) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

Das Beispielprojekt besteht aus zwei Web-apps:

| App         | Projektordner                        | Beschreibung |
| ----------- | ------------------------------------- | ----------- |
| Nachrichten-app | *src/RazorPagesTestingSample*         | Ermöglicht es einem Benutzer hinzufügen, löschen Sie eine, löschen Sie alle und Analysieren von Nachrichten. |
| Test-app    | *tests/RazorPagesTestingSample.Tests* | So testen Sie die Nachrichten-app verwendet.<ul><li>Komponententests: Datenzugriffsebene (DAL), Index-Seitenmodell</li><li>Integrationstests: Seite "Index"</li></ul> |

Die Tests können ausgeführt werden, mithilfe der integrierten Testfunktionen von einer IDE wie [Visual Studio](https://www.visualstudio.com/vs/). Wenn [Visual Studio Code](https://code.visualstudio.com/) oder der Befehlszeile, und führen Sie den folgenden Befehl an einer Eingabeaufforderung in das *tests/RazorPagesTestingSample.Tests* Ordner:

```console
dotnet test
```

## <a name="message-app-organization"></a>Organisation der Nachrichten-app

Die Nachrichten-app ist ein einfaches Razor-Pages Nachrichtensystem, das mit den folgenden Merkmalen:

* Die Indexseite der app (*Pages/Index.cshtml* und *Pages/Index.cshtml.cs*) eine Benutzeroberfläche und die Seite stellt Modellmethoden bereit, um zu steuern, die Hinzufügung, löschen und Analyse von Nachrichten (durchschnittliche Wörtern pro Nachricht) .
* Eine Nachricht wird beschrieben, durch die `Message` Klasse (*Data/Message.cs*) mit zwei Eigenschaften: `Id` (Schlüssel) und `Text` (Nachricht). Die `Text` Eigenschaft ist erforderlich, und auf 200 Zeichen beschränkt.
* Nachrichten werden gespeichert mit [Entity Framework des speicherinternen Datenbank](/ef/core/providers/in-memory/)&#8224;.
* Die app enthält einen Datenzugriffsebene (DAL) in seiner Datenbank Context-Klasse `AppDbContext` (*Data/AppDbContext.cs*). Die DAL-Methoden werden markiert `virtual`, dadurch wird die Methoden für die Verwendung in den Tests imitieren.
* Wenn die Datenbank auf app-Starts leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert. Diese *Seeding Nachrichten* werden auch in Tests verwendet.

&#8224; Das Thema EF [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory), wird erläutert, wie eine in-Memory-Datenbank, die für Tests mit MSTest verwendet. In diesem Thema verwendet die [xUnit](https://xunit.github.io/) Testframework. Testen Konzepte und Test-Implementierungen in anderen Testframeworks sind ähnlich, aber nicht identisch.

Auch wenn die app nicht verwendet die [Repositorymusters](http://martinfowler.com/eaaCatalog/repository.html) ist ein Beispiel für effektive nicht die [Arbeitseinheit (UoW) Muster](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor-Pages unterstützt diese Muster der Entwicklung. Weitere Informationen finden Sie unter [Entwerfen der Infrastruktur Persistenzebene](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design), [implementieren das Repository und die Einheit der Arbeit Muster in einer ASP.NET MVC-Anwendung](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application), und [testen Controllerlogik](/aspnet/core/mvc/controllers/testing) (im Beispiel wird das Repositorymuster implementiert).

## <a name="test-app-organization"></a>Testen von app-Organisation

Die Test-app ist eine Konsolen-app innerhalb der *tests/RazorPagesTestingSample.Tests* Ordner:

| Test-app-Ordners    | Beschreibung |
| ------------------ | ----------- |
| *IntegrationTests* | <ul><li>*IndexPageTest.cs* enthält die Integrationstests für die Indexseite.</li><li>*TestFixture.cs* Testhost zum Testen der Nachrichten-app erstellt.</li></ul> |
| *UnitTests*        | <ul><li>*DataAccessLayerTest.cs* der Komponententests enthält, damit die DAL.</li><li>*IndexPageTest.cs* Komponententests für das Modell Index enthält.</li></ul> |
| *Hilfsprogramme*        | *Utilities.cs* enthält die:<ul><li>`TestingDbContextOptions`die Methode verwendet, um die neue Datenbank Kontextoptionen für die einzelnen DAL-Komponententests erstellen, sodass die Datenbank auf die Baseline-Bedingung für jeden Test zurückgesetzt wird.</li><li>`GetRequestContentAsync`Methode zum Vorbereiten der `HttpClient` sowie Inhalte für Anforderungen, die beim Testen der Integration der Nachrichten-App gesendet werden.</li></ul>

Das Testframework ist [xUnit](https://xunit.github.io/). Das Objekt, das imitieren Framework ist [Moq](https://github.com/moq/moq4). Integrationstests erfolgreich durchgeführt werden, mithilfe der [ASP.NET Core Testhost](xref:testing/integration-testing#the-test-host).

## <a name="unit-testing-the-data-access-layer-dal"></a>UnitTests der Datenzugriffsebene (DAL))

Die Nachricht app hat eine DAL mit vier Methoden in der `AppDbContext` Klasse (*src/RazorPagesTestingSample/Data/AppDbContext.cs*). Jede Methode hat ein oder zwei Komponententests im Test-app.

| Die DAL-Methode               | Funktion                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ruft eine `List<Message>` aus der Datenbank, sortiert nach der `Text` Eigenschaft. |
| `AddMessageAsync`        | Fügt eine `Message` mit der Datenbank.                                          |
| `DeleteAllMessagesAsync` | Löscht alle `Message` Einträge aus der Datenbank.                           |
| `DeleteMessageAsync`     | Löscht eine einzelne `Message` aus der Datenbank durch `Id`.                      |

Komponententests für die DAL erfordern [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) beim Erstellen eines neuen `AppDbContext` für jeden Test. Ein Ansatz zum Erstellen der `DbContextOptions` für jeden Test ist die Verwendung einer [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Das Problem bei diesem Ansatz ist, dass jeder Test die Datenbank im unabhängig von Ihrem Status der vorherige Test sie angehalten wurde erhält. Dies kann problematisch sein, beim Versuch, das atomarische Komponententests schreiben, die nicht miteinander in Konflikt. Erzwingen der `AppDbContext` um einen neuen Datenbankkontext für jeden Test verwenden möchten, geben Sie eine `DbContextOptions` -Instanz, die basierend auf einer neuen Service-Anbieter. Die Test-app zeigt, wie Sie diesen Vorgang mit seiner `Utilities` class-Methode `TestingDbContextOptions` (*tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Mithilfe der `DbContextOptions` in der DAL-Einheit-Tests können Tests mit atomar ausgeführt ein eine neue Datenbankinstanz:

```csharp
using (var db = new AppDbContext(Utilities.TestingDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Jede Testmethode in die `DataAccessLayerTest` Klasse (*UnitTests/DataAccessLayerTest.cs*) folgt einem ähnlichen Muster von Act Assert anordnen:

1. Anordnen: Für den Test die Datenbank konfiguriert ist und/oder das erwartete Ergebnis definiert ist.
1. ACT: Der Test ausgeführt wird.
1. Assert-: Assertionen werden, um zu bestimmen, ob das Testergebnis erfolgreich ist.

Z. B. die `DeleteMessageAsync` Methode ist verantwortlich für das Entfernen einer einzelnen Nachricht erkennbar seine `Id` (*src/RazorPagesTestingSample/Data/AppDbContext.cs*):

[!code-csharp[Main](razor-pages-testing/sample/src/RazorPagesTestingSample/Data/AppDbContext.cs?name=snippet4)]

Es gibt zwei Tests für diese Methode. Ein Test wird überprüft, dass die Methode eine Nachricht löscht, wenn die Nachricht in der Datenbank vorhanden ist. Die andere Methode Tests, die die Datenbank ändern nicht, wenn die Nachricht `Id` für das Löschen nicht vorhanden ist. Die `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Methode wird unten gezeigt:

[!code-csharp[Main](razor-pages-testing/sample_snapshot/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode führt zuerst den Diagrammfelder Schritt, abgewickelt Vorbereitung für den Act-Schritt. Das seeding Nachrichten abgerufen und in gespeicherten `seedMessages`. Das seeding Nachrichten werden in der Datenbank gespeichert. Die Nachricht mit einem `Id` von `1` zum Löschen festgelegt ist. Wenn die `DeleteMessageAsync` Methode ausgeführt wird, wird die erwarteten Nachrichten müssen alle Nachrichten mit Ausnahme der Datensatz mit einer `Id` von `1`. Die `expectedMessages` -Variable stellt diese erwartete Ergebnis dar.

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode fungiert: die `DeleteMessageAsync` Methode ausgeführt wird, übergibt der `recId` von `1`:

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Zum Schluss die Methode erhält das `Messages` aus dem Kontext und vergleicht ihn mit der `expectedMessages` Bestätigung, dass die beiden gleich sind:

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Damit verglichen werden kann, die die beiden `List<Message>` identisch sind:

* Die Nachrichten geordnet `Id`.
* Nachrichtenpaare verglichen werden, auf die `Text` Eigenschaft.

Eine ähnliche Testmethode `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` überprüft das Ergebnis des Versuchs, eine Nachricht zu löschen, die nicht vorhanden ist. In diesem Fall muss die erwarteten Nachrichten in der Datenbank entspricht der tatsächlichen Nachrichten nach der `DeleteMessageAsync` Methode ausgeführt wird. Es darf keine Änderung an der Datenbank-Inhalt:

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-testing-the-page-model-methods"></a>Die Seite Modellmethoden für Komponententests

Einen anderen Satz von Komponententests ist verantwortlich für das Modell Seitenmethoden testen. In der Nachrichten-app befinden sich die Index-Seite-Modelle der `IndexModel` -Klasse im *src/RazorPagesTestingSample/Pages/Index.cshtml.cs*.

| Seite "-Modell-Methode | Funktion |
| ----------------- | -------- | 
| `OnGetAsync` | Ruft die Nachrichten aus der DAL für die Benutzeroberfläche mit der `GetMessagesAsync` Methode. |
| `OnPostAddMessageAsync` | Wenn die `ModelState` gültig ist, werden Aufrufe `AddMessageAsync` zum Hinzufügen einer Nachricht in der Datenbank. | 
| `OnPostDeleteAllMessagesAsync` | Aufrufe `DeleteAllMessagesAsync` alle Nachrichten in der Datenbank gelöscht. |
| `OnPostDeleteMessageAsync` | Führt `DeleteMessageAsync` So löschen Sie eine Nachricht mit der `Id` angegebenen. |
| `OnPostAnalyzeMessagesAsync` | Wenn eine oder mehrere Nachrichten in der Datenbank sind, berechnet die durchschnittliche Anzahl von Wörtern pro Nachricht. |

Die Seite Modellmethoden werden getestet mit sieben Tests in der `IndexPageTest` Klasse (*tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs*). Die Tests verwenden das vertraute anordnen Assert Act-Muster. Diese Tests konzentrieren:

* Bestimmen, ob die Methoden das richtige Verhalten führen Sie bei der `ModelState` ist ungültig.
* Bestätigen die Methoden der richtigen erzeugen `IActionResult`.
* Überprüfen, dass die Eigenschaft Wert Zuweisungen ordnungsgemäß vorgenommen werden.

Diese Gruppe von Tests häufig simulieren Sie die Methoden der DAL, erzeugen die erwartete Daten für den Act-Schritt, in dem eine Seite-Modell-Methode ausgeführt wird. Z. B. die `GetMessagesAsync` Methode der `AppDbContext` ist Mocks Ausgabe erstellt. Wenn eine Seite Modell Methode diese Methode ausgeführt wird, gibt das Mock das Ergebnis zurück. Die Daten stammen nicht aus der Datenbank. Dadurch wird die vorhersagbare, zuverlässige testbedingungen für die Verwendung von der DAL in die Seite Modell Tests erstellt.

Die `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` testen zeigt wie die `GetMessagesAsync` Methode ist für die Seitenmodell Mocks erstellt:

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet1&highlight=3-4)]

Wenn die `OnGetAsync` Methode im Act Schritt ausgeführt wird, ruft er die Seitenmodell `GetMessagesAsync` Methode.

Komponententest Act Schritt (*tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs*):

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet2)]

`IndexPage`Seite "-Modell `OnGetAsync` Methode (*src/RazorPagesTestingSample/Pages/Index.cshtml.cs*):

[!code-csharp[Main](razor-pages-testing/sample/src/RazorPagesTestingSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Die `GetMessagesAsync` Methode in der DAL ist nicht das Ergebnis für den Aufruf dieser Methode zurück. Die mocked Version der Methode gibt das Ergebnis zurück.

In der `Assert` Schritt, die tatsächlichen Nachrichten (`actualMessages`) zugewiesen sind, aus der `Messages` Eigenschaft des Modells Seite. Eine typüberprüfung wird auch ausgeführt, wenn die Nachrichten zugeordnet sind. Die erwarteten und tatsächlichen Nachrichten von verglichen werden ihre `Text` Eigenschaften. Der Test bestätigt, die die beiden `List<Message>` Instanzen enthalten die gleichen Meldungen.

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet3)]

Andere Tests in dieser Gruppe erstellen Seite Model-Objekte, die implizit enthalten die `DefaultHttpContext`, die `ModelStateDictionary`, wird ein `ActionContext` zum Herstellen der `PageContext`, eine `ViewDataDictionary`, und ein `PageContext`. Diese sind nützlich, in die Tests durchzuführen. Die Nachrichten-app z. B. richtet eine `ModelState` Fehler mit `AddModelError` überprüfen, ob ein gültiger `PageResult` wird zurückgegeben, wenn `OnPostAddMessageAsync` ausgeführt wird:

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/UnitTests/IndexPageTest.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="integration-testing-the-app"></a>Integrationstests zu legen, die app

Die Integration testet den Fokus auf Tests, die die app-Komponenten zusammenarbeiten. Integrationstests erfolgreich durchgeführt werden, mithilfe der [ASP.NET Core Testhost](xref:testing/integration-testing#the-test-host). Vollständige Anforderung / Antwort-Lifecycle-Verarbeitung wird getestet. Diese Tests bestätigen Sie, dass die Seite mit den richtigen Status-Code erzeugt und `Location` -Header, wenn festgelegt.

Ein Integrationstests-Beispiel aus dem Beispiel überprüft das Ergebnis für das Anfordern der Indexseite, der Nachrichten-app (*tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs*):

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs?name=snippet1)]

Es ist kein anordnen Schritt vorhanden. Die `GetAsync` Methode wird aufgerufen, auf die `HttpClient` eine GET-Anforderung an den Endpunkt zu senden. Der Test bestätigt, dass das Ergebnis ein Statuscode "200 OK".

Alle POST-Anforderung an die app für die Nachricht erfüllen muss die antiforgery Überprüfung, die automatisch von der app ausgeführt wird [antiforgery Datenschutzsystem](xref:security/data-protection/introduction). Um für einen Test, POST-Anforderung anordnen zu können, müssen die Test-app:

1. Stellen Sie eine Anforderung für die Seite.
1. Analysieren der antiforgery Cookie und Validierung Anforderungstoken aus der Antwort.
1. Stellen Sie die POST-Anforderung mit der antiforgery Cookie und Anforderung token vorhanden.

Die `Post_AddMessageHandler_ReturnsRedirectToRoot` -Methode testen:

* Bereitet eine Nachricht und die `HttpClient`.
* Stellt eine POST-Anforderung an die app an.
* Überprüft, dass die Antwort eine Umleitung zurück an die Indexseite ist.

`Post_AddMessageHandler_ReturnsRedirectToRoot `Methode (*tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs*):

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs?name=snippet2)]

Die `GetRequestContentAsync` Hilfsprogrammmethode verwaltet Vorbereiten des Clients mit dem antiforgery Cookie und Überprüfung von Anforderungstoken. Beachten Sie, wie die Methode empfängt einen `IDictionary` , die die aufrufende Testmethode, um Daten aus der Anforderung zusammen mit dem Überprüfungstoken der Anforderung codiert übergeben zulässt (*tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/Utilities/Utilities.cs?name=snippet2&highlight=1-2,8-9,29)]

Integrationstests können fehlerhafte Daten auch auf die app zum Testen der app-Antwortverhalten übergeben werden. Die app Nachricht begrenzt Nachrichtenlänge 200 Zeichen (*src/RazorPagesTestingSample/Data/Message.cs*):

[!code-csharp[Main](razor-pages-testing/sample/src/RazorPagesTestingSample/Data/Message.cs?name=snippet1&highlight=7)]

Die `Post_AddMessageHandler_ReturnsSuccess_WhenMessageTextTooLong` testen `Message` explizit im Text mit 201 "X"-Zeichen übergibt. Dies führt zu einem `ModelState` Fehler. Die Bereitstellung zurückgeleitet nicht zur die Indexseite. Es gibt eine 200-OK mit einem `null` `Location` Header (*tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs*):

[!code-csharp[Main](razor-pages-testing/sample/tests/RazorPagesTestingSample.Tests/IntegrationTests/IndexPageTest.cs?name=snippet3&highlight=7,16-17)]

## <a name="see-also"></a>Siehe auch

* [UnitTests von c# in .NET Core mit Dotnet Test- und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Integrationstests](xref:testing/integration-testing)
* [Testen von Controllern](xref:mvc/controllers/testing)
* [Komponententest-Codes](/visualstudio/test/unit-test-your-code) (Visual Studio)
* [xUnit.net](https://xunit.github.io/)
* [Erste Schritte mit xUnit.net (.NET Core/ASP.NET Kerne)](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq-Schnellstart](https://github.com/Moq/moq4/wiki/Quickstart)
