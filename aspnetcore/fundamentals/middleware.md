---
title: ASP.NET Core Middleware
author: rick-anderson
description: Informationen Sie zu ASP.NET Core Middleware und der Anforderungspipeline.
keywords: ASP.NET Core, Middleware-Pipeline, Delegaten
ms.author: riande
manager: wpickett
ms.date: 10/14/2017
ms.topic: article
ms.assetid: db9a86ab-46c2-40e0-baed-86e38c16af1f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware
ms.openlocfilehash: ad8d207b1e6de396f16d098fb07ddc89bea2c520
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="aspnet-core-middleware-fundamentals"></a>ASP.NET Core Middleware-Grundlagen

<a name="fundamentals-middleware"></a>

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-middleware"></a>Was ist die Middleware

Middleware ist eine Software, die in einer Pipeline der Anwendung zum Verarbeiten von Anforderungen und Antworten eingebaut wird. Jede Komponente:

* Wählt aus, ob die Anforderung an die nächste Komponente in der Pipeline übergeben werden sollen.
* Kann Aufgaben ausführen, bevor und nachdem die nächste Komponente in der Pipeline aufgerufen wird. 

Anforderung Delegaten werden verwendet, um die Pipeline zu erstellen. Die Anforderung Delegaten behandeln jede HTTP-Anforderung.

Delegaten werden mithilfe von konfiguriert anfordern [ausführen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [Zuordnung](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), und [verwenden](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) Erweiterungsmethoden [c#]. Ein einzelne Anforderung Delegaten kann angegebene Zeile als einer anonymen Methode, die (als Inline-Middleware) sein, oder sie kann in einer wiederverwendbaren Klasse definiert werden. Diese wiederverwendbare Klassen und Inline-anonyme Methoden sind *Middleware*, oder *middlewarekomponenten gemeinsam*. Jede middlewarekomponente in der Anforderungspipeline ist verantwortlich für die nächste Komponente in der Pipeline aufrufen oder die Kette verkürzte, falls zutreffend.

[Migrieren von HTTP-Module auf Middleware](../migration/http-modules.md) erläutert den Unterschied zwischen der Anforderung Pipelines in ASP.NET Core und den Vorgängerversionen und stellt Beispiele für weitere Middleware.

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a>Erstellen eine middlewarepipeline mit IApplicationBuilder

Die Anforderungspipeline ASP.NET Core besteht aus einer Sequenz von Anforderung-Delegaten, die nach der anderen aufgerufen, wie in diesem Diagramm (den Thread der Ausführung folgt der schwarzen Pfeile) gezeigt:

![Anforderung verarbeiten des Musters mit der eine Anforderung, die empfangen, die Verarbeitung durch drei Middlewares und die Antwort, die die Anwendung. Jede Middleware seiner Logik ausgeführt und die Anforderung an die nächste Middleware bei der Anweisung::Next() übergibt. Nachdem die dritte Middleware die Anforderung verarbeitet ist Linkshändiger durch die vorherigen beiden Middlewares für die zusätzliche Verarbeitung, nachdem die::Next() Anweisungen jedes wiederum vor dem Verlassen der Anwendung als Antwort an den Client zurück.](middleware/_static/request-delegate-pipeline.png)

Jeder Delegat kann Vorgänge vor und nach dem nächsten Delegaten ausführen. Ein Delegat können auch eine Anforderung nicht an dem nächsten Delegaten übergeben Sie die verkürzte der Anforderungspipeline aufgerufen wird. Verkürzte ist häufig wünschenswert, da unnötige Arbeit vermieden werden. Die Middleware für statische Dateien kann z. B. eine Anforderung für eine statische Datei zurückgeben und Kurzschluss den Rest der Pipeline. Behandlung von Ausnahmen Delegaten müssen zu einem frühen Zeitpunkt in der Pipeline aufgerufen werden, sodass sie Ausnahmen abfangen können, die in späteren Phasen der Pipeline auftreten.

Die einfachste mögliche ASP.NET Core app richtet ein Anfrage-Delegat, der alle Anforderungen verarbeitet. Eine tatsächliche Anforderungspipeline ist in diesem Fall enthalten. Stattdessen wird eine anonyme Funktion als Antwort auf jede HTTP-Anforderung aufgerufen.

[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]

Die erste [app. Führen Sie](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) Delegat wird die Pipeline beendet.

Sie können mehrere Anforderung Delegaten zusammen mit verketten [app. Verwendung](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions). Die `next` Parameter darstellt, den nächsten Delegaten in der Pipeline. (Beachten Sie, dass Sie die Pipeline mit Circuit können *nicht* Aufrufen der *Weiter* Parameter.) Sie können in der Regel Aktionen vor und nach der nächsten Delegat ausführen, wie in diesem Beispiel veranschaulicht:

[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> Rufen Sie nicht `next.Invoke` nachdem die Antwort an den Client gesendet wurde. Änderungen an `HttpResponse` nach dem Start der Antwort wird eine Ausnahme ausgelöst. Änderungen, z. B. das Festlegen von Headern, Statuscode "" usw., werden z. B. eine Ausnahme ausgelöst. Schreiben in den Antworttext nach dem Aufruf `next`:
> - Kann ein Protokoll verletzen. Schreiben Sie z. B. mehr als die genannten `content-length`.
> - Das Textformat kann zu einer Beschädigung. Schreiben Sie z. B. eine HTML-Fußzeile in einer CSS-Datei.
>
> [HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) ist ein Hinweis nützlich, um anzugeben, ob der Header gesendet wurden, und/oder der Text wurde in den geschrieben.

## <a name="ordering"></a>Sortieren

Die Reihenfolge, die Middleware Komponenten, in hinzugefügt werden der `Configure` Methode definiert, die Reihenfolge, in dem sie aufgerufen werden für Anforderungen, und die umgekehrte Reihenfolge für die Antwort. Diese Reihenfolge ist wichtig für die Sicherheit, Leistung und Funktionalität.

Die Configure-Methode (siehe unten) Fügt die folgenden Middleware-Komponenten:

1. Ausnahme/Fehlerbehandlung
2. Statische Dateiserver
3. Authentifizierung
4. MVC

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)


```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseAuthentication();               // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

-----------

Im obigen Code `UseExceptionHandler` ist die erste middlewarekomponente, die der Pipeline hinzugefügt – aus diesem Grund, fängt Sie alle Ausnahmen, die in späteren Aufrufen auftreten.

Die Middleware für statische Dateien wird einem frühen Zeitpunkt in der Pipeline aufgerufen, damit Anforderungen verarbeitet und Kurzschluss, ohne die verbleibenden Komponenten durchlaufen werden kann. Stellt die Middleware für statische Dateien **keine** autorisierungsüberprüfungen vorgenommen. Alle Dateien von bedient, u. a. die *"Wwwroot"*, sind öffentlich verfügbar. Finden Sie unter [arbeiten mit statischen Dateien](xref:fundamentals/static-files) eine Methode, um statische Dateien zu sichern.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)


Wenn die Anforderung nicht von der Middleware für statische Dateien behandelt wird, erfolgt eine Übergabe auf an die Identity-Middleware (`app.UseAuthentication`), die die Authentifizierung durchführt. Identität nicht authentifizierte Anforderungen keinen Kurzschluss ausführt. Obwohl Identität Anforderungen authentifiziert hat, tritt auf, Autorisierung (und Ablehnung) erst nach MVC einem bestimmten Razor-Seite oder Controller und Aktion auswählt.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Wenn die Anforderung nicht von der Middleware für statische Dateien behandelt wird, erfolgt eine Übergabe auf an die Identity-Middleware (`app.UseIdentity`), die die Authentifizierung durchführt. Identität nicht authentifizierte Anforderungen keinen Kurzschluss ausführt. Obwohl Identität Anforderungen authentifiziert hat, tritt auf, Autorisierung (und Ablehnung) erst nach MVC einen bestimmten Controller und Aktion auswählt.

-----------

Das folgende Beispiel zeigt eine Middleware, die Reihenfolge, in dem Anforderungen für statische Dateien vom die Middleware für statische Dateien vor der Komprimierung-Middleware Antwort behandelt werden. Statische Dateien werden nicht komprimiert, mit der diese Reihenfolge der Middleware. Das MVC-Antworten von [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) können komprimiert werden.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name="middleware-run-map-use"></a>

### <a name="use-run-and-map"></a>Verwenden Sie, führen Sie aus und zuordnen

Sie konfigurieren Sie das HTTP-Pipeline mit `Use`, `Run`, und `Map`. Die `Use` Methode kann die Pipeline Kurzschluss (d. h., wenn er nicht aufgerufen wird eine `next` Anforderung Delegaten). `Run`ist eine Konvention, und einige Komponenten Middleware können ausgesetzt `Run[Middleware]` Methoden, die am Ende der Pipeline ausgeführt.

`Map*`Erweiterungen werden als eine Konvention verwendet, für die Pipeline zu verzweigen. [Zuordnung](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) Verzweigungen die Anforderungspipeline auf Übereinstimmungen von den angegebenen Anforderungspfad basierend. Wenn der Anforderungspfad mit dem angegebenen Pfad beginnt, wird die Verzweigung ausgeführt.

[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]

Die folgende Tabelle zeigt die Anforderungen und Antworten von `http://localhost:1234` mit dem vorherigen Code:

| Anforderung | Antwort |
| --- | --- |
| localhost:1234 | Hallo von nicht-Map-Delegat.  |
| Localhost:1234 / "map1" | Test 1 zuordnen |
| Localhost:1234 / map2 | Test 2 für Zuordnung |
| Localhost:1234 / map3 | Hallo von nicht-Map-Delegat.  |

Wenn `Map` wird verwendet, werden die übereinstimmenden Pfad Segment(s) aus entfernt `HttpRequest.Path` und auf angefügten `HttpRequest.PathBase` für jede Anforderung.

[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) Verzweigungen die Anforderungspipeline auf dem Ergebnis des angegebenen Prädikats basierend. Jedes Prädikat des Typs `Func<HttpContext, bool>` kann zum Zuordnen von Anforderungen in eine neue Verzweigung der Pipeline verwendet werden. Im folgenden Beispiel wird ein Prädikat zum Erkennen des Vorhandenseins von Abfragezeichenfolgen-Variable verwendet `branch`:

[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]

Die folgende Tabelle zeigt die Anforderungen und Antworten von `http://localhost:1234` mit dem vorherigen Code:

| Anforderung | Antwort |
| --- | --- |
| localhost:1234 | Hallo von nicht-Map-Delegat.  |
| Localhost:1234 /? Verzweigung = Master | Verzweigung verwendet = Master|

`Map`unterstützt das schachteln, z. B.:

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

`Map`kann auch mehrere Segmente gleichzeitig, zum Beispiel entsprechen:

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a>Integrierte middleware

ASP.NET Core umfasst die folgenden Middleware-Komponenten:

| Middleware | Beschreibung |
| ----- | ------- |
| [Authentifizierung](xref:security/authentication/identity) | Bietet Unterstützung für die Authentifizierung an. |
| [CORS](xref:security/cors) | Konfiguriert die Cross-Origin Resource Sharing. |
| [Zwischenspeichern von Antworten](xref:performance/caching/middleware) | Bietet Unterstützung für das Zwischenspeichern von Antworten. |
| [Antwort-Komprimierung](xref:performance/response-compression) | Bietet Unterstützung für die Komprimierung von Antworten an. |
| [Routing](xref:fundamentals/routing) | Definiert, und schränkt die Routen der Anforderung. |
| [Sitzung](xref:fundamentals/app-state) | Bietet Unterstützung für die Verwaltung von benutzersitzungen. |
| [Statische Dateien](xref:fundamentals/static-files) | Bietet Unterstützung für statische Dateien und die Verzeichnissuche bedient. |
| [URL-umschreibende Middleware](xref:fundamentals/url-rewriting) | Bietet Unterstützung für das Umschreiben von URLs und das Umleiten von Anforderungen. |

<a name="middleware-writing-middleware"></a>

## <a name="writing-middleware"></a>Schreiben von middleware

Middleware wird in der Regel in einer Klasse gekapselt und mit einer Erweiterungsmethode verfügbar gemacht. Betrachten Sie die folgenden Middleware, die aus der Abfragezeichenfolge die Kultur für die aktuelle Anforderung festlegt:

[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]

Hinweis: Im obigen Beispielcode dient zur Veranschaulichung erstellen eine middlewarekomponente. Finden Sie unter [ Globalisierung und Lokalisierung](xref:fundamentals/localization) für ASP.NET Core des integrierten lokalisierungsunterstützung.

Sie können die Middleware testen, indem Sie in der Kultur, z. B. Übergabe `http://localhost:7997/?culture=no`.

Der folgende Code wechselt der Middleware-Delegat in einer Klasse:

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]

Die folgende Erweiterungsmethode macht die Middleware über [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

Der folgende Code Ruft die Middleware aus `Configure`:

[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

Middleware befolgen die [expliziten Abhängigkeiten Prinzip](http://deviq.com/explicit-dependencies-principle/) , verfügbar machen, dessen Abhängigkeiten in ihren Konstruktor. Middleware wird einmal pro erstellt *Lebensdauer der Anwendung*. Finden Sie unter *Abhängigkeiten pro Anforderung* unten für den Fall müssen Sie Dienste mit Middleware innerhalb einer Anforderung freigeben.

Middleware-Komponenten können ihre Abhängigkeiten von Abhängigkeitsinjektion über Konstruktorparameter aufgelöst werden. [`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)kann auch weitere Parameter direkt akzeptieren.

### <a name="per-request-dependencies"></a>Abhängigkeiten pro Anforderung

Da Middleware, beim Starten der app, nicht pro Anforderung erstellt wurde, *im Bereich einer* Lebensdauerdienste von Middleware Konstruktoren verwendet werden nicht für andere Typen Abhängigkeit eingefügter freigegeben, während jede Anforderung. Wenn Sie freigeben müssen eine *im Bereich einer* service zwischen Ihrer Middleware und anderen Projekttypen, fügen Sie diese Dienste für die `Invoke` Methodensignatur. Die `Invoke` Methode kann zusätzliche Parameter annehmen, die vom Abhängigkeitsinjektion aufgefüllt werden. Zum Beispiel:

```c#
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="resources"></a>Ressourcen

* [Beispielcode, der in diesem Dokument verwendet wird.](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [Migrating HTTP Modules to Middleware (Migration von HTTP-Modulen zu Middleware)](../migration/http-modules.md)
* [Application Startup (Starten von Anwendungen)](startup.md)
* [Erforderliche Funktionen](request-features.md)
