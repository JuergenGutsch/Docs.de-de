---
title: "Verwenden JavaScriptServices für einseitige Anwendungen erstellen"
author: scottaddie
description: Weitere Informationen Sie zu den Vorteilen der Verwendung von JavaScriptServices zum Erstellen von einer einzelnen Seite Anwendung (SPA) durch ASP.NET Core gesichert wird.
manager: wpickett
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 08/02/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/spa-services
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3c38a1730e43586f37cd773bb8daa418736952f
ms.sourcegitcommit: b3d46df910fb679edb8dd47234db6b4da604eedb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/10/2017
---
# <a name="using-javascriptservices-for-creating-single-page-applications-with-aspnet-core"></a>Verwenden JavaScriptServices für einseitige Anwendungen mit ASP.NET Core erstellen

Durch [Scott Addie](https://github.com/scottaddie) und [Fiyaz Hasan](http://fiyazhasan.me/)

Eine einzelne Seite Anwendung (SPA) ist eine beliebte Web Anwendungstyp aufgrund der inhärenten leistungsstarke, optimierte Benutzeroberfläche. Integrieren von clientseitigen SPA-Frameworks oder-Bibliotheken, z. B. [Angular](https://angular.io/) oder [reagieren](https://facebook.github.io/react/), mit serverseitigen Frameworks wie ASP.NET Core schwierig sein kann. [JavaScriptServices](https://github.com/aspnet/JavaScriptServices) wurde entwickelt, um die Unstimmigkeiten in den Integrationsprozess zu reduzieren. Dadurch werden nahtlos zwischen den Client- und Server-Technologie Stapel.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/spa-services/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a>Was ist JavaScriptServices?

JavaScriptServices ist eine Auflistung von clientseitigen Technologien zum ASP.NET Core. Das Ziel besteht darin zu ASP.NET Core als Entwicklers bevorzugte serverseitige Plattform zum Erstellen von SPAs zu positionieren.

JavaScriptServices besteht aus drei unterschiedlichen NuGet-Pakete:
* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)
* [Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)

Diese Pakete sind nützlich, wenn Sie:
* JavaScript wird auf dem Server ausgeführt.
* Verwenden Sie einen SPA-Framework oder Bibliothek
* Erstellen Sie die clientseitige Medienobjekte mit Webpaketdatei

Ein Großteil der Fokus in diesem Artikel wird auf die mithilfe des Pakets SpaServices platziert.

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a>Was ist SpaServices?

SpaServices wurde erstellt, um ASP.NET Core als Entwicklers bevorzugte serverseitige Plattform zum Erstellen von SPAs zu positionieren. SpaServices ist nicht erforderlich, um SPAs mit ASP.NET Core entwickeln, und Sie in einem bestimmten Client-Framework ist nicht gesperrt.

Bietet eine SpaServices nützlich Infrastruktur wie z. B.:
* [Serverseitige prerendering](#server-prerendering)
* [Webpaketdatei Dev Middleware](#webpack-dev-middleware)
* [Ersetzen eines Moduls im laufenden Systembetrieb](#hot-module-replacement)
* [Routing-Hilfsprogramme](#routing-helpers)

Erweitern Sie diese Infrastrukturkomponenten zusammen des entwicklungsworkflows und der Common Language Runtime-Umgebung. Die Komponenten können einzeln übernommen werden.

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a>Voraussetzungen für die Verwendung von SpaServices

Zum Arbeiten mit SpaServices installieren Sie die folgenden Schritte aus:
* [Node.js](https://nodejs.org/) (Version 6 oder höher) mit Npm
    * Um diese Komponenten installiert sind und verwendbaren zu überprüfen, führen Sie Folgendes über die Befehlszeile ein:

    ```console
    node -v && npm -v
    ```

Hinweis: Wenn Sie auf ein Azure-Website bereitstellen, Sie brauchen dies hier tun &mdash; Node.js ist installiert und in den serverumgebungen verfügbar.

* [.NET Core SDK](https://www.microsoft.com/net/download/core) 1.0 (oder höher)
    * Wenn Sie auf Windows nutzen, kann dies durch Auswählen von Visual Studio 2017 installiert **.NET Core plattformübergreifende Entwicklung** arbeitsauslastung.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet-Paket

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a>Serverseitige prerendering

Eine universelle (auch bekannt als funktionierenden) ist ein JavaScript-Anwendung kann sowohl auf den Server und dem Client ausgeführt. Stellen eine universelle Plattform für diese Anwendung Entwicklungsstil, Angular reagieren und anderen beliebten Frameworks. Die Idee ist zuerst Rendern die Framework-Komponenten auf dem Server über Node.js, und klicken Sie dann weitere Ausführung an den Client zu delegieren.

ASP.NET Core [Tag Hilfsprogramme](xref:mvc/views/tag-helpers/intro) gebotenen SpaServices die Implementierung eines serverseitigen Prerendering vereinfachen, indem Sie die JavaScript-Funktionen auf dem Server aufrufen.

### <a name="prerequisites"></a>Erforderliche Komponenten

Installieren Sie Folgendes:
* [ASPNET-Prerendering](https://www.npmjs.com/package/aspnet-prerendering) Npm-Paket:

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a>Konfiguration

Die Tag-Hilfsprogramme werden in des Projekts über Namespace Registrierung erkennbar gemacht *_ViewImports.cshtml* Datei:

[!code-cshtml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Diese Hilfsprogramme Tag Abstraktion der eigenheiten der direkten Kommunikation mit Low-Level-APIs durch die Nutzung einer HTML-ähnlichen Syntax in der Razor-Ansicht:

[!code-cshtml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="the-asp-prerender-module-tag-helper"></a>Die `asp-prerender-module` Helper kennzeichnen

Die `asp-prerender-module` Tag-Hilfsprogramms, die im vorherigen Codebeispiel verwendet führt *ClientApp/dist/main-server.js* auf dem Server über Node.js. Aus Gründen der Klarheit *Main server.js* Datei ist ein Element der Aufgabe Transpilation TypeScript und JavaScript in der [Webpaketdatei](http://webpack.github.io/) Buildprozess. Webpaketdatei definiert einen Eintrag Point-Alias des `main-server`; und das Abhängigkeitsdiagramm für diesen Alias Durchlauf beginnt bei der *ClientApp/Boot-server.ts* Datei:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Im folgenden Beispiel Angular der *ClientApp/Boot-server.ts* Datei nutzt die `createServerRenderer` Funktion und `RenderResult` Typ der `aspnet-prerendering` Npm-Paket zum Rendern der Server über Node.js zu konfigurieren. Das HTML-Markup für serverseitiges Rendering an einen Funktionsaufruf Resolve übergeben wird, die in einer stark typisierten JavaScript umschlossen wird bestimmt `Promise` Objekt. Die `Promise` des Objekts "significance" ist, dass es das HTML-Markup der Seite für dateiinjektion in das DOM Platzhalterelement asynchron bereitstellt.

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="the-asp-prerender-data-tag-helper"></a>Die `asp-prerender-data` Helper kennzeichnen

Verbindung mit der `asp-prerender-module` Tag-Hilfsobjekt, der `asp-prerender-data` Tag Helper können verwendet werden, um die Kontextinformationen für die serverseitige JavaScript aus der Razor-Ansicht zu übergeben. Z. B. das folgende Markup Benutzerdaten übergibt die `main-server` Modul:

[!code-cshtml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Die empfangenen Daten `UserName` Argument wird mithilfe der integrierten JSON-Serialisierungsprogramms serialisiert und befindet sich in der `params.data` Objekt. Im folgenden Beispiel Angular werden verwendet, die Daten so erstellen Sie eine personalisierte Begrüßung innerhalb einer `h1` Element:

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Hinweis: Tag Hilfsprogramme übergebene Eigenschaftennamen mit dargestellt **"PascalCase"** Notation. Vergleichen Sie für JavaScript ist, in denen die gleichen Eigenschaftennamen mit dargestellt werden, die **camelCase ändern möchten**. Die JSON-Serialisierung Standardkonfiguration ist verantwortlich für diesen Unterschied.

Informationen zum Erweitern auf dem vorherigen Beispiel Daten können werden vom Server an die Ansicht übergeben von hydrating der `globals` Eigenschaft bereitgestellt, um die `resolve` Funktion:

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Die `postList` Array definiert, die innerhalb der `globals` Objekt wird an des Browsers globale angefügt `window` Objekt. Diese Variable Korrelationsverlust globalen Bereich eliminiert Duplizierung des Aufwands, insbesondere, wie er bezieht sich auf das Laden der gleichen Daten einmal auf dem Server und erneut auf dem Client.

![Globale postList Variable Fensterobjekt angefügt](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a>Webpaketdatei Dev Middleware

[Webpaketdatei Dev Middleware](https://webpack.github.io/docs/webpack-dev-middleware.html) bietet einen optimierte Entwicklung-Workflow, bei dem Webpaketdatei Ressourcen bei Bedarf erstellt. Die Middleware wird automatisch kompiliert und die clientseitige Ressourcen dient, wenn eine Seite im Browser geladen wird. Der alternative Ansatz ist die Webpaketdatei manuell über das Projekt Npm Buildskript aufrufen, wenn eine Drittanbieter Abhängigkeit oder des benutzerdefinierten Codes ändert. Ein Npm Skript erstellen, der *"Package.JSON"* Datei wird im folgenden Beispiel gezeigt:

[!code-json[Main](../client-side/spa-services/sample/SpaServicesSampleApp/package.json?range=5)]

### <a name="prerequisites"></a>Erforderliche Komponenten

Installieren Sie Folgendes:
* [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a>Konfiguration

Webpaketdatei Dev Middleware registriert ist, in der HTTP-Anforderungspipeline über den folgenden Code in die *Startup.cs* Datei `Configure` Methode:

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=webpack-middleware-registration&highlight=4)]

Die `UseWebpackDevMiddleware` Erweiterungsmethode aufgerufen werden muss, bevor [registrieren, statische Datei hosting](xref:fundamentals/static-files) über die `UseStaticFiles` Erweiterungsmethode. Registrieren Sie die Middleware aus Gründen der Sicherheit nur, wenn die app in den Entwicklungsmodus ausgeführt wird.

Die *webpack.config.js* Datei `output.publicPath` -Eigenschaft teilt die Middleware zum Überwachen der `dist` Ordner Änderungen:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a>Ersetzen eines Moduls im laufenden Systembetrieb

Denken Sie an der Webpaketdatei [Hot Austausch eines Controllermoduls](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html) (HMR)-Funktion als Weiterentwicklung der [Webpaketdatei Dev Middleware](#webpack-dev-middleware). HMR führt dieselben Vorteile, aber es weiter optimiert des entwicklungsworkflows durch automatisches Aktualisieren der Seiteninhalt nach dem Kompilieren der Änderungen. Verwechseln Sie dies mit einer Aktualisierung des Browsers, die mit dem aktuellen im Speicher enthaltenen Status und die Debugsitzung von der SPA beeinträchtigen würde. Es ist ein Livelink zwischen der Webpaketdatei Dev Middleware-Dienst und den Browser, was bedeutet, dass Änderungen an den Browser per Push übertragen werden.

### <a name="prerequisites"></a>Erforderliche Komponenten

Installieren Sie Folgendes:
* [Webpaketdatei während des Betriebs Middleware](https://www.npmjs.com/package/webpack-hot-middleware) Npm-Paket:

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a>Konfiguration

Die Komponente HMR muss registriert werden, in MVCs-HTTP-Anforderungspipeline in die `Configure` Methode:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Wie wurde mit "true" [Webpaketdatei Dev Middleware](#webpack-dev-middleware), `UseWebpackDevMiddleware` Erweiterungsmethode aufgerufen werden muss, bevor die `UseStaticFiles` Erweiterungsmethode. Registrieren Sie die Middleware aus Gründen der Sicherheit nur, wenn die app in den Entwicklungsmodus ausgeführt wird.

Die *webpack.config.js* Datei definieren, muss ein `plugins` array, auch wenn es leer gelassen wird:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Nach dem Laden der app im Browser, bietet die Entwicklertools Konsole Registerkarte Bestätigung des HMR-Aktivierung:

![Im laufenden Systembetrieb Austausch eines Controllermoduls verbindungsmeldung](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a>Routing-Hilfsprogramme

In den meisten ASP.NET Core-basierte SPAs sollten Sie clientseitige neben dem routing serverseitige routing. Die SPA und MVC-routing-Systeme können ohne Störung möglich sind unabhängig voneinander arbeiten. Vorhanden ist, jedoch eine Kante Groß-/Kleinschreibung machen Herausforderungen: 404 HTTP-Antworten zu identifizieren.

Betrachten Sie das Szenario, in dem ein Standarddokument Route des `/some/page` verwendet wird. Angenommen Sie, die Anforderung nicht-Musterübereinstimmung eine serverseitige Route, aber die entsprechende Muster entspricht einer Route für die clientseitige. Betrachten Sie nun eine eingehende Anforderung für `/images/user-512.png`, die im Allgemeinen davon ausgeht, eine Bilddatei auf dem Server. Wenn diesem Pfad für die angeforderte Ressource keine serverseitigen Route oder eine statische Datei übereinstimmt, ist es unwahrscheinlich, dass die clientseitige Anwendung behandelt werden würde, in der Regel einen 404 HTTP-Statuscode zurückgegeben werden soll.

### <a name="prerequisites"></a>Erforderliche Komponenten

Installieren Sie Folgendes:
* Das routing Npm-Paket für die clientseitige. Verwenden Sie Angular als Beispiel:

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a>Konfiguration

Eine Erweiterungsmethode mit dem Namen `MapSpaFallbackRoute` werden in der `Configure` Methode:

[!code-csharp[Main](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=mvc-routing-table&highlight=7-9)]

Tipp: Routen werden in der Reihenfolge ausgewertet, in denen sie konfiguriert sind. Folglich die `default` Route im vorangehenden Codebeispiel wird zuerst für den Mustervergleich verwendet wird.

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a>Erstellen eines neuen Projekts

JavaScriptServices bietet vorkonfiguriert, dass Anwendungsvorlagen. SpaServices wird in dieser Vorlagen in Verbindung mit verschiedenen Frameworks und Bibliotheken wie Angular, Aurelia Knockout, reagieren und Vue verwendet.

Diese Vorlagen können über die .NET Core-CLI installiert werden, durch den folgenden Befehl ausführen:

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Eine Liste der verfügbaren SPA-Vorlagen wird angezeigt:

| Vorlagen                                 | Kurzname | Sprache | Tags        |
|:------------------------------------------|:-----------|:---------|:------------|
| MVC ASP.NET Core mit Angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core mit Aurelia             | aurelia    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core mit Knockout.js         | Knockout   | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core mit React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core mit React.js und Redux  | reactredux | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core mit Vue.js              | VUE        | [C#]     | Web/MVC/SPA | 

Zum Erstellen eines neuen Projekts mithilfe einer der SPA-Vorlagen enthalten die **Kurzname** der Vorlage in der `dotnet new` Befehl. Der folgende Befehl erstellt eine Angular-Anwendung mit ASP.NET Core MVC für die Serverseite konfiguriert:

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a>Legen Sie den Modus der Common Language Runtime-Konfiguration

Zwei primäre Runtime Konfigurationsmodi vorhanden sind:
* **Entwicklung**:
    * Enthält Zuordnungen von Quelle zur Erleichterung des Debuggings.
    * Den clientseitige Code für die Leistung nicht optimiert werden.
* **Produktion**:
    * Schließt Quelle Karten an.
    * Optimiert die clientseitigen Code über Bündelung und Minimierung.

ASP.NET Core verwendet eine Umgebungsvariable namens `ASPNETCORE_ENVIRONMENT` zum Speichern des Konfigurationsmodus. Finden Sie unter  **[Einrichten der Umgebung](xref:fundamentals/environments#setting-the-environment)**  für Weitere Informationen.

### <a name="running-with-net-core-cli"></a>Ausführen von mit .NET Core CLI

Stellen Sie die erforderlichen NuGet und Npm-Pakete durch den folgenden Befehl ausführen, auf der Stammebene des Projekts wieder her:

```console
dotnet restore && npm i
```

Erstellen und Ausführen der Anwendung:

```console
dotnet run
```

Starten der Anwendung auf "localhost" gemäß der [Common Language Runtime-Konfigurationsmodus](#runtime-config-mode). Navigieren zu `http://localhost:5000` die Startseite im Browser angezeigt wird.

### <a name="running-with-visual-studio-2017"></a>Mit Visual Studio 2017 ausführen

Öffnen der *csproj* von generierten Datei die `dotnet new` Befehl. Die erforderlichen NuGet und Npm-Pakete werden auf Projekt öffnen automatisch wiederhergestellt. Diese Wiederherstellung kann einige Minuten dauern, und die Anwendung ist bereit, die ausgeführt werden, wenn er abgeschlossen wurde. Klicken Sie auf die grüne Schaltfläche ausführen, oder drücken Sie `Ctrl + F5`, und der Browser geöffnet wird, zur Startseite der Anwendung. Die Anwendung ausgeführt wird, auf "localhost" gemäß der [Common Language Runtime-Konfigurationsmodus](#runtime-config-mode). 

<a name="app-testing"></a>

## <a name="testing-the-app"></a>Testen der app

SpaServices Vorlagen sind für die clientseitige Tests mithilfe von vorkonfiguriert [Karma](https://karma-runner.github.io/1.0/index.html) und [Jasmine](https://jasmine.github.io/). Jasmine ist eine beliebte UnitTest-Framework für JavaScript, während der Karma ein Testprogramm für diese Tests ist. Karma ist so konfiguriert, dass die Bearbeitung der [Webpaketdatei Dev Middleware](#webpack-dev-middleware) , dass der Entwickler ist nicht erforderlich sind, beenden, und führen Sie den Test, jedes Mal, wenn Änderungen vorgenommen werden. Ob er den Code für den Testfall oder den Testfall selbst ausgeführt wird, wird der Test automatisch ausgeführt.

Verwendung der Angular-Anwendung als Beispiel zwei Jasmintee Testfälle sind bereits deckte die `CounterComponent` in der *counter.component.spec.ts* Datei:

[!code-typescript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Öffnen Sie die Eingabeaufforderung im Stammverzeichnis des Projekts, und führen Sie den folgenden Befehl aus:

```console
npm test
```

Startet das Skript die Karma-Testprogramm, die in definierten Einstellungen liest die *karma.conf.js* Datei. Neben anderen Einstellungen der *karma.conf.js* identifiziert die Testdateien auszuführenden über seine `files` Array:

[!code-javascript[Main](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a>Veröffentlichen der Anwendung

Kombinieren die generierte clientseitige Bestand und der veröffentlichten ASP.NET Core-Artefakte in ein Paket kann jetzt bereitstellen kann sehr aufwändig sein. Glücklicherweise orchestriert SpaServices dieses Prozesses für die gesamte Veröffentlichung mit einem benutzerdefinierten MSBuild-Ziel mit dem Namen `RunWebpack`:

[!code-xml[Main](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Das MSBuild-Ziel hat die folgenden Verantwortungen:
1. Wiederherstellen der Npm-Pakete
1. Erstellen eines Builds Produktions-Grade der Ressourcen von Drittanbietern, die clientseitige
1. Erstellen eines Builds Produktions-Grade benutzerdefinierte clientseitige Objekte
1. Kopieren Sie die Webpaketdatei generierte Objekte in dem Veröffentlichungsordner

Das MSBuild-Ziel wird aufgerufen, wenn ausgeführt:

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Angular Docs](https://angular.io/docs)
