---
title: Sitzung und Anwendungsstatus in ASP.NET Core
author: rick-anderson
description: "Ansätze zum Beibehalten der Anwendung und des Benutzerstatus (Sitzung) zwischen Anforderungen."
ms.author: riande
manager: wpickett
ms.date: 10/08/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/app-state
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d4d10ef45d562f34c3f8b5ce025abaf763c862d3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="introduction-to-session-and-application-state-in-aspnet-core"></a>Einführung in die Sitzung und Anwendungsstatus in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), und [Diana LaRose](https://github.com/DianaLaRose)

HTTP ist ein statusfreies Protokoll. Ein Webserver jede HTTP-Anforderung als unabhängige Anforderung behandelt, und Benutzerwerte aus den vorherigen Anforderungen nicht beibehalten werden sollen. Dieser Artikel beschreibt verschiedene Möglichkeiten, Anwendung und der Status der Sitzung zwischen den Anforderungen beizubehalten. 

## <a name="session-state"></a>Sitzungsstatus

Sitzungszustand ist ein Feature in ASP.NET Core, das Sie verwenden können, um Benutzerdaten zu speichern, während der Benutzer Ihre Web-App durchsucht. Bestehend aus einem Wörterbuch oder Hash-Tabelle auf dem Server, bleiben Sitzungsstatus Daten anforderungsübergreifend in einem Browser. Die Sitzungsdaten durch einen Cache gesichert.

ASP.NET Core beibehalten Sitzungszustand, durch die Vergabe des Clients ein Cookie, das die Sitzungs-ID enthält, die mit jeder Anforderung an den Server gesendet wird. Der Server verwendet die Sitzungs-ID, um die Sitzungsdaten abzurufen. Da das Sitzungscookie spezifisch für den Browser ist, kann nicht Browsern Sitzungen gemeinsam nutzen. Sitzungscookies sind nur gelöscht, wenn die Browsersitzung beendet. Wenn ein Cookie für eine Sitzung für abgelaufene empfangen wird, wird eine neue Sitzung, die das gleiche Sitzungscookie verwendet erstellt. 

Der Server behält eine Sitzung für einen begrenzten Zeitraum nach der letzten Anforderung. Sie können das Sitzungstimeout festgelegt oder den Standardwert von 20 Minuten. Status der Sitzung ist ideal zum Speichern von Benutzerdaten, die bezieht sich auf einer bestimmten Sitzung jedoch nicht dauerhaft beibehalten werden müssen. Daten werden gelöscht aus dem Sicherungsspeicher entweder beim Aufrufen von `Session.Clear` oder im Datenspeicher des Ablaufs der Sitzung. Der Server weiß nicht, wenn der Browser geschlossen wird oder wenn das Sitzungscookie gelöscht wird.

> [!WARNING]
> Speichern Sie vertrauliche Daten nicht in der Sitzung an. Der Client möglicherweise nicht den Browser schließen und löschen Sie das Sitzungscookie (und von einigen Browsern erhalten Sitzungscookies über Windows). Darüber hinaus kann eine Sitzung nicht auf einen einzelnen Benutzer beschränkt sein; der nächste Benutzer möglicherweise mit der gleichen Sitzung fortgesetzt.

Die in-Memory-sitzungsanbieters speichert Sitzungsdaten auf dem lokalen Server. Wenn Sie beabsichtigen, Ihre Web-app auf einer Serverfarm auszuführen, müssen Sie persistente Sitzungen verwenden, um jede Sitzung an einen bestimmten Server zu verbinden. Die Websites der Windows Azure-Plattform wird standardmäßig auf persistente Sitzungen (Application Request Routing oder ARR). Allerdings können persistente Sitzungen Auswirkungen auf die Skalierbarkeit und Web-app-Updates erschweren. Eine bessere Option ist die Verwendung des Redis oder verteilten SQL Server zwischengespeichert werden, die nicht persistente Sitzungen erfordern. Weitere Informationen finden Sie unter [arbeiten mit einem verteilten Cache](xref:performance/caching/distributed). Weitere Informationen zum Einrichten der Dienstanbieter, finden Sie unter [konfigurieren Sitzung](#configuring-session) weiter unten in diesem Artikel.


<a name="temp"></a>
## <a name="tempdata"></a>TempData

ASP.NET Core MVC macht die [TempData](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) Eigenschaft auf einen [Controller](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller?view=aspnetcore-2.0). Diese Eigenschaft speichert Daten, bis sie gelesen wurden. Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen. `TempData`ist besonders nützlich für die Umleitung, wenn Daten für mehr als eine einzelne Anforderung benötigt werden. `TempData`wird von TempData-Anbieter, z. B. implementiert mithilfe von Cookies oder Sitzungsstatus.

<a name="tempdata-providers"></a>
### <a name="tempdata-providers"></a>TempData-Anbieter

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

In ASP.NET Core 2.0 und höher, wird standardmäßig der Cookie-basierte TempData-Anbieter verwendet, um TempData in Cookies zu speichern.

Ist die Cookiedaten codiert die [Base64UrlTextEncoder](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder?view=aspnetcore-2.0). Daran, dass das Cookie verschlüsselt und aus Ihren Segmenten zusammengesetzt wird, Größe der einzelne Cookie Grenzwert gefunden in ASP.NET Core 1.x nicht gilt. Die Cookiedaten werden nicht komprimiert werden, da beim Komprimieren von Daten Encryped Sicherheitsprobleme wie führen, kann die [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) und [Verletzung](https://wikipedia.org/wiki/BREACH_(security_exploit)) Angriffe. Weitere Informationen für den Cookie-basierte TempData-Anbieter finden Sie unter [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

In ASP.NET Core 1.0 und 1.1 ist der TempData Sitzungsstatusanbieter die Standardeinstellung.

--------------

### <a name="choosing-a-tempdata-provider"></a>TempData-Anbieter auswählen

Auswählen eines Anbieters TempData umfasst verschiedene Aspekte im Zusammenhang, z. B.:

1. Wird die Anwendung bereits für andere Zwecke Sitzungsstatus verwendet? Wenn dies der Fall ist, hat die Verwendung des TempData sitzungsstatusanbieters ohne zusätzliche Kosten für die Anwendung (abgesehen von der Größe der Daten).
2. Werden die Anwendung TempData nur selten für relativ kleine Datenmengen (bis zu 500 Bytes) verwendet? Wenn also der Cookie-TempData-Anbieter eine kleine Kosten für jede Anforderung hinzufügen, die TempData führt. Wenn dies nicht der Fall ist, wird der TempData Sitzungsstatusanbieter kann nützlich sein, Round-Tripping eine große Menge von Daten in jeder Anforderung vermeiden, bis die TempData genutzt wird.
3. Werden die Anwendung wird in einer Webfarm (mehrere Server) ausgeführt? Wenn dies der Fall ist, es ist keine zusätzliche Konfiguration erforderlich, um die Cookie-TempData-Anbieter verwenden.

> [!NOTE]
> Die meisten Webclients (z. B. Webbrowser) zu erzwingen Grenzwerte für die maximale Größe der einzelnen Cookies, die Gesamtanzahl von Cookies oder beide. Wenn das Cookie TempData-Anbieter verwenden, überprüfen Sie daher, dass die app beim Überschreiten dieser Grenzwerte wird nicht. Betrachten Sie die Gesamtgröße der Daten, für die Kosten der Verschlüsselung Kontoführung und Segmentierung.

Um den TempData-Anbieter für eine Anwendung zu konfigurieren, registrieren Sie eine Implementierung eines Anbieters TempData in `ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddMvc()
        .AddSessionStateTempDataProvider();

    // The Session State TempData Provider requires adding the session state service
    services.AddSession();
}
```

## <a name="query-strings"></a>Abfragezeichenfolgen

Sie können eine begrenzte Menge von Daten aus einer Anforderung in eine andere übergeben, indem Abfragezeichenfolge für die neue Anforderung hinzugefügt. Dies ist nützlich zum Aufzeichnen des Status in einer persistenten Weise, die Links mit eingebetteten Status über e-Mail oder soziale Netzwerke freigegeben werden kann. Allerdings sollten aus diesem Grund Sie nie Abfragezeichenfolgen für vertrauliche Daten verwenden. Zusätzlich zu einfach freigegeben, einschließlich der Daten in Abfragezeichenfolgen kann erstellen Verkaufschancen für [Cross-Site Request Fälschung Websiteübergreifender](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) Angriffe, bei denen Benutzer für den Zugriff auf bösartige Websites während authentifiziert bringen können. Angreifer können dann Benutzerdaten aus Ihrer app zu stehlen oder böswilligen Aktionen im Namen des Benutzers. Alle beibehaltenen Zustand der Anwendung oder die Sitzung muss vor CSRF-Angriffen schützen. Weitere Informationen zu CSRF-Angriffen, finden Sie unter [verhindern Cross-Site Request XSRF/Websiteübergreifender Anforderungsfälschung Angriffe in ASP.NET Core](../security/anti-request-forgery.md).

## <a name="post-data-and-hidden-fields"></a>Bereitgestellte Daten und ausgeblendete Felder

Daten können in ausgeblendeten Formularfelder gespeichert und wieder auf die nächste Anforderung gesendet werden. Dies ist häufig in mehreren Seiten Formularen. Jedoch, da der Client möglicherweise die Daten manipulieren kann, muss der Server immer es erneut überprüfen. 

## <a name="cookies"></a>Cookies

Cookies bieten eine Möglichkeit zum Speichern von benutzerspezifischen Daten in Webanwendungen. Da Cookies mit jeder Anforderung gesendet werden, sollte ihre Größe auf ein Minimum begrenzt. Im Idealfall sollten lediglich ein Bezeichner mit den tatsächlichen Daten, die auf dem Server gespeicherten in einem Cookie gespeichert werden. Die meisten Browser beschränken Cookies auf 4096 Bytes. Darüber hinaus sind nur eine begrenzte Anzahl von Cookies für jede Domäne verfügbar.  

Da Cookies sind leichter manipuliert werden, müssen sie auf dem Server überprüft werden. Die Dauerhaftigkeit des Cookies auf einem Client unterliegt zwar Eingreifen des Benutzers und das Ablaufdatum, sind sie in der Regel die meisten permanenten Form der Dauerhaftigkeit der Daten auf dem Client.

Cookies werden für die Personalisierung, häufig verwendet, in dem Inhalte für einen bekannten Benutzer angepasst. Da der Benutzer nur ermittelt und in den meisten Fällen nicht authentifiziert, können Sie einen Cookie in der Regel sichern, indem Sie den Benutzernamen, Kontoname oder eine eindeutige Benutzer-ID (z. B. eine GUID) im Cookie speichern. Das Cookie können dann die Benutzer Personalisierungsinfrastruktur einer Website zugreifen.

## <a name="httpcontextitems"></a>HttpContext.Items

Die `Items` Auflistung ist ein guter Speicherort zum Speichern von Daten, die erforderlich ist nur bei der Verarbeitung einer bestimmter Anforderung. Der Inhalt der Auflistung werden nach jeder Anforderung verworfen. Die `Items` Auflistung wird am besten als eine Möglichkeit, Komponenten oder Middleware kommunizieren kann, wenn sie zu unterschiedlichen Zeitpunkten ausgeführt, in der Zeit, während eine Anforderung werden und keine direkte Möglichkeit zum Übergeben von Parametern haben verwendet. Weitere Informationen finden Sie unter [arbeiten mit HttpContext.Items](#working-with-httpcontextitems)weiter unten in diesem Artikel.

## <a name="cache"></a>cache

Caching ist eine effiziente Möglichkeit zum Speichern und Abrufen von Daten. Sie können die Lebensdauer für zwischengespeicherte Elemente basierend auf Uhrzeit und andere Faktoren steuern. Erfahren Sie mehr über [Caching](../performance/caching/index.md).

<a name="session"></a>
## <a name="working-with-session-state"></a>Arbeiten mit Sitzungsstatus

### <a name="configuring-session"></a>Konfigurieren der Sitzung

Die `Microsoft.AspNetCore.Session` Paket stellt Middleware für die Verwaltung des Sitzungsstatus bereit. So aktivieren Sie die Sitzung Middleware `Startup` muss enthalten:

- Keines der [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) arbeitsspeichercaches. Die `IDistributedCache` Implementierung ist für Sitzung als Sicherungsspeicher verwendet.
- [AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) aufzurufen, erfordert die NuGet-Paket "Microsoft.AspNetCore.Session".
- [UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) aufrufen.

Der folgende Code zeigt, wie der Sitzungsanbieter in-Memory-eingerichtet wird.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/Startup.cs?highlight=11-19,24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/Startup.cs?highlight=11-19,24)]

---

Sie können die Sitzung von verweisen `HttpContext` nachdem es installiert und konfiguriert wurde.

Wenn Sie versuchen, den Zugriff auf `Session` vor `UseSession` aufgerufen wurde, die Ausnahme `InvalidOperationException: Session has not been configured for this application or request` ausgelöst wird.

Wenn Sie versuchen, ein neues erstellen `Session` (d. h. keine Sitzungscookie erstellt wurde), nachdem Sie das Schreiben in bereits begonnen haben die `Response` stream, der die Ausnahme `InvalidOperationException: The session cannot be established after the response has started` ausgelöst wird. Die Ausnahme finden Sie in der Web-Server-Protokoll; Es wird nicht im Browser angezeigt werden.

### <a name="loading-session-asynchronously"></a>Laden die Sitzung asynchron 

Der Standardanbieter für die Sitzung in ASP.NET Core lädt die Sitzung Datensatz aus der zugrunde liegenden [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) Store asynchron nur, wenn die [ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) -Methode explizit aufgerufen wird, bevor  die `TryGetValue`, `Set`, oder `Remove` Methoden. Wenn `LoadAsync` ist nicht zuerst aufgerufen, die zugrunde liegende Sitzung Datensatz synchron geladen wird, was kann potenziell die Möglichkeit zum Skalieren der app auswirken.

Damit Anwendungen, die dieses Muster zu erzwingen, umschließen die [DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) und [DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) Implementierungen mit Versionen, die eine Ausnahme auszulösen, wenn die `LoadAsync` Methode nicht wird aufgerufen, bevor `TryGetValue`, `Set`, oder `Remove`. Die umschlossenen Versionen im Container zu registrieren.

### <a name="implementation-details"></a>Details zur Implementierung

Sitzung wird ein Cookie verwendet, verfolgen und Anforderungen von einer einzelnen Browsersitzung bestimmen. Standardmäßig lautet dieses Cookie ". AspNet.Session", und verwendet den Pfad"/". Da der Cookie-Standardwert angeben einer Domäne nicht, es nicht zur Verfügung gestellt wird die Skriptdatei auf Clientseite auf der Seite (da `CookieHttpOnly` standardmäßig `true`).

Um Sitzung Standardwerte zu überschreiben, verwenden `SessionOptions`:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/StartupCopy.cs?name=snippet1&highlight=8-12)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/StartupCopy.cs?name=snippet1&highlight=8-12)]

---

Der Server verwendet die `IdleTimeout` -Eigenschaft können Sie bestimmen, wie lange eine Sitzung im Leerlauf befinden kann, bevor Sie seinen Inhalt abgebrochen werden. Diese Eigenschaft ist unabhängig von den Ablauf der Cookies. Jede Anforderung, die die Sitzung Middleware (aus gelesen oder geschrieben) durchlaufen setzt das Timeout an.

Da `Session` ist *nicht sperrende*, wenn zwei Anforderungen, die beide versuchen, zum Ändern des Inhalts der Sitzung, für das letzte Lesezeichen überschreibt das erste. `Session`wird als implementiert eine *kohärente Sitzung*, was bedeutet, dass der gesamte Inhalt zusammen gespeichert werden. Zwei Anforderungen, die verschiedene Teile der Sitzung (verschiedene Schlüssel) zu ändern, werden möglicherweise weiterhin auswirken.

### <a name="setting-and-getting-session-values"></a>Festlegen und Abrufen von Sitzungsdaten

Sitzung erfolgt über die `Session` Eigenschaft `HttpContext`. Diese Eigenschaft ist ein [ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession) Implementierung.

Im folgende Beispiel wird gezeigt, einstellungs- und ein "Int" und eine Zeichenfolge:

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?range=8-27,49)]

Wenn Sie die folgenden Erweiterungsmethoden hinzufügen, können Sie festlegen und Abrufen von serialisierbare Objekte Sitzung:

[!code-csharp[Main](app-state/sample/src/WebAppSession/Extensions/SessionExtensions.cs)]

Im folgende Beispiel wird gezeigt, wie festgelegt und ein serialisierbares Objekt abgerufen wird:

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet2)]


## <a name="working-with-httpcontextitems"></a>Arbeiten mit HttpContext.Items

Die `HttpContext` Abstraktion bietet Unterstützung für ein Dictionary-Auflistung des Typs `IDictionary<object, object>`namens `Items`. Diese Sammlung ist verfügbar, ab dem Anfang einer *HttpRequest* und am Ende jeder Anforderung verworfen wird. Sie können darauf zugreifen, durch Zuweisen eines Werts zu einem schlüsselgebundenen Eintrag oder durch den Wert für einen bestimmten Schlüssel anfordern.

Im folgenden Beispiel wird [Middleware](middleware.md) fügt `isVerified` auf die `Items` Auflistung.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Weiter unten in der Pipeline kann eine andere Middleware darauf zugreifen:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Verified request? " + 
        context.Items["isVerified"]);
});
```

Für die Middleware, die nur von einer einzigen Anwendung verwendet werden, `string` Schlüssel sind zulässig. Allerdings sollten Middleware, die zwischen Anwendungen gemeinsam genutzt werden, eindeutige Objektschlüssel verwenden, um alle Konflikte zu vermeiden. Wenn Sie Middleware entwickeln, die für mehrere Anwendungen arbeiten müssen, verwenden Sie einen eindeutiges Objektschlüssel in die Middleware-Klasse, die wie folgt definiert:

```csharp
public class SampleMiddleware
{
    public static readonly object SampleKey = new Object();

    public async Task Invoke(HttpContext httpContext)
    {
        httpContext.Items[SampleKey] = "some value";
        // additional code omitted
    }
}
```

Andere Codezugriff können in gespeicherten `HttpContext.Items` mithilfe des Schlüssels, der von der Middleware-Klasse verfügbar gemacht werden:

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        string value = HttpContext.Items[SampleMiddleware.SampleKey];
    }
}
```

Dieser Ansatz hat außerdem den Vorteil des Entfernens der Wiederholung von "Magic-Zeichenfolgen" an mehreren Stellen im Code.

<a name="appstate-errors"></a>

## <a name="application-state-data"></a>Anwendungszustandsdaten

Verwendung [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) um Daten für alle Benutzer verfügbar zu machen:

1. Definieren Sie einen Dienst, der Daten enthält (z. B. eine Klasse, die mit dem Namen `MyAppData`).

```csharp
public class MyAppData
{
    // Declare properties/methods/etc.
} 
```
2. Hinzufügen der Dienstklasse zum `ConfigureServices` (z. B. `services.AddSingleton<MyAppData>();`).
3. Verwenden Sie die Datendienstklasse in jedem Controller:

```csharp
public class MyController : Controller
{
    public MyController(MyAppData myService)
    {
        // Do something with the service (read some data from it, 
        // store it in a private field/property, etc.)
    }
} 
```

## <a name="common-errors-when-working-with-session"></a>Häufige Fehler bei der Arbeit mit der Sitzung

* "Dienst für Typ"Microsoft.Extensions.Caching.Distributed.IDistributedCache"aufgelöst, bei dem Versuch,"Microsoft.AspNetCore.Session.DistributedSessionStore"aktivieren kann."

  Dies wird normalerweise verursacht durch wegen eines Fehlers beim Konfigurieren von mindestens einer `IDistributedCache` Implementierung. Weitere Informationen finden Sie unter [arbeiten mit einem verteilten Cache](xref:performance/caching/distributed) und [im Arbeitsspeicher zwischenspeichern](xref:performance/caching/memory).

* Im Ereignisprotokoll die der Sitzung Middleware ein Fehler, um auftritt eine Sitzung beibehalten (zum Beispiel: Wenn die Datenbank nicht verfügbar ist), es auffängt und protokolliert die Ausnahme. Die Anforderung wird dann in der Regel wird weiterhin Dies führt zu sehr zu unvorhersehbarem Verhalten.

Ein typisches Beispiel:

Eine Person speichert einen Warenkorb gelegt, in der Sitzung. Der Benutzer fügt ein Element aus, aber das Commit schlägt fehl. Die app weiß nicht über den Fehler, damit es die Meldung gibt Aufschluss über "das Element wurde" wird die nicht "true", hinzugefügt.

Die empfohlene Methode zum Suchen nach solchen Fehlern besteht im Aufrufen `await feature.Session.CommitAsync();` von app-Code, wenn Sie fertig sind in der Sitzung geschrieben. Anschließend können Sie tun, wie Sie mit dem Fehler. Es funktioniert die gleiche Weise wie beim Aufrufen von `LoadAsync`.


### <a name="additional-resources"></a>Zusätzliche Ressourcen


* [ASP.NET Core 1.x: Beispielcode in diesem Dokument verwendeten](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSession)
* [ASP.NET Core 2.x: Beispielcode in diesem Dokument verwendeten](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSessionDotNetCore2.0App)
