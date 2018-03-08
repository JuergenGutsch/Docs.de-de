---
title: Testen von Controllerlogik in ASP.NET Core
author: ardalis
description: Informationen Sie zum Testen der Controllerlogik in ASP.NET Core mit Moq und xUnit.
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/testing
ms.openlocfilehash: aa60912e06946bd0df4936d33c88d3bf7b69984c
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="testing-controller-logic-in-aspnet-core"></a>Testen von Controllerlogik in ASP.NET Core

Durch [Steve Smith](https://ardalis.com/)

Controller in ASP.NET MVC-apps sollte klein und konzentriert sich auf die Benutzeroberfläche Bedenken. Große Controller, die mit nicht-UI-Aspekte sind schwieriger zu testen und zu verwalten.

[Anzeigen oder Herunterladen des Beispiels von GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)

## <a name="testing-controllers"></a>Testen von Domänencontrollern

Controller sind ein zentraler Bestandteil jeder ASP.NET Core MVC-Anwendung. Daher sollten Sie vertrauen verfügen, die sie für die app erwartungsgemäß Verhalten. Automatisierte Tests vermittelt Ihnen dieses Vertrauen und Fehler können erkannt werden, bevor sie die Produktion erreichen. Es ist wichtig, die verhindert, dass unnötige Zuständigkeiten innerhalb Ihrer Controller platziert, und vergewissern Sie sich sich der Fokus Tests nur auf Controller Aufgaben.

Controllerlogik sollte minimal sein und nicht mit Schwerpunkt auf Business Logic oder Infrastruktur Bedenken (z. B. Data Access). Testen Sie Controllerlogik, nicht das Framework. Test wie den Controller *verhält sich* basierend auf gültige bzw. ungültige Eingaben. Abhängig vom Ergebnis des Vorgangs Business ausgeführten Domänencontroller-Antworten zu testen.

Typische Controller Verantwortungsbereiche:

* Vergewissern Sie sich `ModelState.IsValid`.
* Eine Fehlerantwort zurück, wenn `ModelState` ist ungültig.
* Rufen Sie eine Geschäftseinheit von Persistenz.
* Eine Aktion für die Geschäftseinheit auszuführen.
* Speichern Sie die Geschäftseinheit Persistenz.
* Zurückgeben einer entsprechenden `IActionResult`.

## <a name="unit-testing"></a>Unittest

[UnitTests](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) umfasst eine Bestandteile einer app isoliert von der Infrastruktur und die Abhängigkeiten testen. Wenn Controllerlogik, nur den Inhalt einer einzelnen Aktion für den Komponententests getestet wird, nicht das Verhalten der zugehörigen Abhängigkeiten oder Framework selbst. Testen Sie als Einheit Sie Ihre Controlleraktionen, stellen Sie sicher, dass Sie sich nur auf das Verhalten konzentrieren. Ein Komponententest Controller wird vermieden, z. B. [Filter](filters.md), [routing](../../fundamentals/routing.md), oder [modellbindung](../models/model-binding.md). Komponententests sind in der Regel durch die Fokussierung auf nur einem Schritt testen, einfach zu schreiben und schnell ausgeführt. Ein gut geschriebener Satz von Komponententests kann ohne Mehraufwand häufig ausgeführt werden. Allerdings Komponententests Probleme erkennen nicht bei der Interaktion zwischen Komponenten, die der Zweck der also [Integrationstests](xref:mvc/controllers/testing#integration-testing).

Wenn Sie benutzerdefinierte Filter, Routen usw. schreiben, sollten Sie den Komponententest werden, jedoch nicht als Teil Ihrer Tests auf einer bestimmten Controlleraktion. Sie sollten in Isolation getestet werden.

> [!TIP]
> [Erstellen und Ausführen von Komponententests mit Visual Studio](https://docs.microsoft.com/visualstudio/test/unit-test-your-code).

Um Komponententests zu demonstrieren, überprüfen Sie die folgenden Controller aus. Es zeigt eine Liste von Sitzungen brainstorming an und ermöglicht neue brainstorming Sitzungen, die mit einer POST-Anforderung erstellt werden:

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?highlight=12,16,21,42,43)]

Liegt im Anschluss an des Controllers der [expliziten Abhängigkeiten Prinzip](http://deviq.com/explicit-dependencies-principle/), erwartet Abhängigkeitsinjektion diesen angeben, mit einer Instanz von `IBrainstormSessionRepository`. Dies erleichtert es relativ zum Testen mithilfe eines simulierten-Objekt, z. B. [Moq](https://www.nuget.org/packages/Moq/). Die `HTTP GET Index` Methode verfügt über keine Schleife "oder" Verzweigung "und" nur, eine Aufrufe-Methode. Zum Testen `Index` -Methode, müssen wir überprüfen, ob eine `ViewResult` zurückgegeben wird, mit einem `ViewModel` aus des Repositorys `List` Methode.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=17-18&range=1-33,76-95)]

Die `HomeController` `HTTP POST Index` (siehe oben) Methode überprüfen sollten:

* Die Aktionsmethode gibt eine ungültige Anforderung `ViewResult` mit den entsprechenden Daten beim `ModelState.IsValid` ist`false`

* Die `Add` im Repository wird aufgerufen, und ein `RedirectToActionResult` wird zurückgegeben, mit die richtigen Argumente beim `ModelState.IsValid` ist "true".

Ungültige Modellstatus getestet werden kann, durch Hinzufügen von Fehlern mit `AddModelError` wie im ersten Test unten gezeigt.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=8,15-16,37-39&range=35-75)]

Der erste Test bestätigt, wenn `ModelState` ist ungültig, die gleiche `ViewResult` wird zurückgegeben, als für eine `GET` Anforderung. Beachten Sie, dass der Test nicht versucht, ein ungültiges Modell übergeben. Das wäre nicht trotzdem funktioniert, da wurden die modellbindung ausgeführt wird (obwohl eine [Integrationstest](xref:mvc/controllers/testing#integration-testing) Übung wurden die modellbindung verwenden). In diesem Fall wird nicht die modellbindung getestet wird. Diese Komponententests werden nur durch Testen der Code in der Aktionsmethode.

Im zweite Test wird überprüft, ob bei `ModelState` gültig ist, ein neues `BrainstormSession` (über das Repository) hinzugefügt wird und die Methode gibt ein `RedirectToActionResult` mit den erwarteten Eigenschaften. Mocks erstellt-Aufrufe, die aufgerufen werden, sind normalerweise ignoriert, aber das aufrufende `Verifiable` Aufruf am Ende des Setups können sie im Test überprüft werden. Dies erfolgt mit dem Aufruf von `mockRepo.Verify`, dem schlägt des Tests auf, wenn die erwartete Methode aufgerufen wurde nicht.

> [!NOTE]
> Die Moq-Bibliothek, die in diesem Beispiel verwendete ganz einfach zum Mischen von überprüfbare oder "strict" Mocks mit nicht überprüfbar Mocks (auch als "locker" Mocks oder Stubs bezeichnet). Erfahren Sie mehr über [Anpassen des Verhaltens von Mock mit Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

Eine andere Domänencontroller in der app zeigt die Informationen im Zusammenhang mit einer bestimmten Brainstorming-Sitzung an. Es enthält die Logik für den Umgang mit ungültiger Id-Werte:

[!code-csharp[Main](./testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?highlight=19,20,21,22,25,26,27,28)]

Controlleraktion verfügt über drei Fälle zu testen, eine für jeden `return` Anweisung:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?highlight=27,28,29,46,47,64,65,66,67,68)]

Die app macht die Funktionalität wie eine Web-API (eine Liste mit einer Sitzung Brainstorming und eine Methode zum Hinzufügen von neuer Ideen zu einer Sitzung zugeordneten Ideen):

<a name="ideas-controller"></a>

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?highlight=21,22,27,30,31,32,33,34,35,36,41,42,46,52,65)]

Die `ForSession` Methode gibt eine Liste von `IdeaDTO` Typen. Vermeiden Sie Ihre Domäne Geschäftseinheiten direkt über API-Aufrufe, die seit häufig, die sie enthalten mehr Daten als das Client-API erfordert, und verknüpfen sie unnötig interne Domänenmodell Ihrer app mit der API, die Sie extern verfügbar machen. Zuordnung zwischen Domänenentitäten und die Typen, die über die Verbindung wird nun wieder kann manuell ausgeführt werden (mit einer LINQ `Select` wie hier gezeigt) oder die Verwendung einer Bibliothek wie [AutoMapper](https://github.com/AutoMapper/AutoMapper)

Die Komponententests für die `Create` und `ForSession` API-Methoden:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?highlight=18,23,29,33,38-39,43,50,58-59,68-70,76-78&range=1-83,121-135)]

Wie zuvor erwähnt, so testen Sie das Verhalten der Methode beim `ModelState` ist ungültig, einen Modellfehler mit dem Controller als Teil des Tests hinzufügen. Versuchen Sie nicht, Testen von modellbindung Überprüfung oder das Modell in den Komponententests - nur Testen Ihrer Aktionsmethode-Verhalten, wenn mit einem bestimmten konfrontiert `ModelState` Wert.

Im zweite Test hängt das Repository null zurückgeben, damit das pseudorepository konfiguriert ist, um null zurückzugeben. Besteht keine Notwendigkeit, eine Testdatenbank erstellen (im Arbeitsspeicher oder auf andere Weise) und erstellen Sie eine Abfrage, die diesem Ergebnis zurückgegeben werden: Es kann in einer einzelnen Anweisung ausgeführt werden, wie gezeigt.

Die letzte Test überprüft, ob des Repositorys `Update` -Methode aufgerufen wird. Wie zuvor, heißt das Mock mit `Verifiable` , und klicken Sie dann die mocked des Repositorys `Verify` Methode wird aufgerufen, um zu bestätigen, die überprüfbare Methode ausgeführt wurde. Es ist dabei nicht um eine Einheit Test dafür verantwortlich, um sicherzustellen, dass die `Update` Methode gespeichert, die Daten; die mit einen Integrationstest durchgeführt werden können.

## <a name="integration-testing"></a>Integrationstests

[Integrationstests](../../testing/integration-testing.md) geschieht, um separate Module innerhalb der app-Arbeit ordnungsgemäß zusammen sicherzustellen. Im Allgemeinen alles, was Sie mit einem Komponententest testen können, Sie können auch mit Testen einen Integrationstest, aber umgekehrt nicht "true". Allerdings sind eher Integrationstests sehr viel langsamer als Komponententests. Daher ist es am besten, testen, was Sie mit Komponententests können und Integrationstests für Szenarien, die mehrere Projektmitarbeiter umfassen.

Obwohl sie immer noch nützlich sein können, werden Pseudoobjekten in Integrationstests selten verwendet. In Komponententests ausgeführt werden sind Pseudoobjekten eine effektive Möglichkeit zur Steuerung, wie Projektmitarbeiter außerhalb der Komponententests im Rahmen des Tests Verhalten soll. In einen Integrationstest werden echte Projektmitarbeiter verwendet, um zu bestätigen, dass das gesamte Subsystem zusammen ordnungsgemäß funktioniert.

### <a name="application-state"></a>Anwendungsstatus

Ein wichtiger Aspekt beim Ausführen von Integrationstests ist wie den Zustand der Anwendung festgelegt. Getestet unabhängig voneinander ausgeführt werden sollen, und daher starten jedes Tests sollten mit der app in einem bekannten Zustand. Wenn Ihre app verwenden Sie eine Datenbank oder haben die Persistenz nicht, kann dies ein Problem nicht. Allerdings beibehalten den meisten realen apps ihren Status an eine Art des Datenspeichers, damit die Änderungen durch ein Test einen anderen Test auswirken können, es sei denn, die Datenspeicher zurückgesetzt wird. Unter Verwendung der integrierten `TestServer`, es ist sehr einfach zu ASP.NET Core-apps in unserer Integrationstests Host, aber, die nicht unbedingt gewähren Zugriff auf die Daten werden soll. Wenn Sie eine tatsächliche Datenbank verwenden, ein Ansatz besteht darin, die app auf eine Testdatenbank hergestellt haben die Tests zugreifen und sicherstellen können, werden in einen bekannten Zustand zurückgesetzt, vor jedem Test ausgeführt wird.

In dieser beispielanwendung verwende ich Entity Framework Core-InMemoryDatabase-Unterstützung, damit ich nur aus meinem Testprojekt hergestellt werden konnte. Stattdessen ich verfügbar machen eine `InitializeDatabase` Methode aus der app `Startup` -Klasse, die ich aufgerufen werden, wenn die app gestartet wird, wird jedoch der `Development` Umgebung. Meine Integrationstests automatisch hiervon profitieren, solange sie die Umgebung, um festgelegt `Development`. Muss ich mir keine Gedanken machen, die Datenbank zurückgesetzt, da jedes Mal die InMemoryDatabase zurückgesetzt wird, wenn die app wird neu gestartet.

Die `Startup` Klasse:

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Startup.cs?highlight=19,20,34,35,43,52)]

Sehen Sie die `GetTestSession` in der folgenden Integrationstests häufig verwendete Methode.

### <a name="accessing-views"></a>Zugreifen auf Ansichten

Jeder Testklasse Integration konfiguriert die `TestServer` ASP.NET Core-app ausgeführt wird. Standardmäßig `TestServer` hostet die Web-app in den Ordner, in dem er ausgeführt – in diesem Fall die Test-Projektordner wird. Daher, wenn Sie versuchen, Controlleraktionen zu testen, die zurückgeben `ViewResult`, dieser Fehler vermutlich angezeigt:

```
The view 'Index' wasn't found. The following locations were searched:
(list of locations)
```

Um dieses Problem zu beheben, müssen Sie das Stammverzeichnis des Servers Inhalt, zu konfigurieren, damit sie die Ansichten für das getestete Projekt suchen kann. Dies erfolgt durch einen Aufruf von `UseContentRoot` in die `TestFixture` Klasse, die im folgenden gezeigt:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/TestFixture.cs?highlight=30,33)]

Die `TestFixture` Klasse dient zum Konfigurieren und erstellen die `TestServer`, das Einrichten einer `HttpClient` für die Kommunikation mit der `TestServer`. Jede der Integration testet verwendet die `Client` Eigenschaft zum Herstellen einer Verbindung mit dem Testserver und führen Sie eine Anforderung.

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/HomeControllerTests.cs?highlight=20,26,29,30,31,35,38,39,40,41,44,47,48)]

Im ersten Test weiter oben wird die `responseString` enthält die tatsächlichen HTML gerendert, aus der Sicht, die überprüft werden kann, um zu bestätigen sie die erwarteten Ergebnisse enthält.

Im zweite Test erstellt einen Formular-POST mit einen eindeutigen Sitzungsnamen an und sendet es an die app, und stellt sicher, dass die erwarteten Umleitung zurückgegeben wird.

### <a name="api-methods"></a>API-Methoden

Wenn Ihre app verfügbar, Web-APIs, es eine gute Idee macht, automatisierte Tests bestätigen sie erwartungsgemäß ausgeführt. Die integrierte `TestServer` erleichtert das Web-APIs zu testen. Wenn Ihre API-Methoden wurden die modellbindung verwenden, sollten Sie immer überprüfen `ModelState.IsValid`, und Integrationstests erfolgreich sind, die richtige Stelle, um sicherzustellen, dass die modellvalidierung ordnungsgemäß funktioniert.

Die folgende Gruppe von Tests Ziel der `Create` Methode in der [IdeasController](xref:mvc/controllers/testing#ideas-controller) oben gezeigte Klasse:

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/ApiIdeasControllerTests.cs)]

Im Gegensatz zu Integrationstests von Aktionen, die HTML-Ansichten zurückgibt, können Web-API-Methoden, die Ergebnisse in der Regel als stark typisierte Objekte deserialisiert werden, wie in der letzten Test oben gezeigt. In diesem Fall der Test das Ergebnis, das deserialisiert eine `BrainstormSession` -Instanz und bestätigt, dass das Konzept auf die Auflistung von Ideen ordnungsgemäß hinzugefügt wurde.

Weitere Beispiele für Integrationstests finden Sie in diesem Artikel [Beispielprojekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample).
