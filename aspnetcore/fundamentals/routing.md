---
title: Routing in ASP.NET Core
author: ardalis
description: "Ermitteln Sie, wie ASP.NET Core Routingfunktion für eine eingehende Anforderung an eine Routenhandler Zuordnung zuständig ist."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/routing
ms.openlocfilehash: 5dd8bee7228587d7e13f128bc8f16102fb70a412
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="routing-in-aspnet-core"></a>Routing in ASP.NET Core

Durch [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), und [Rick Anderson](https://twitter.com/RickAndMSFT)

Routingfunktion ist verantwortlich für die Zuordnung einer eingehenden Anforderungs an eine Routenhandler. Routen werden in der ASP.NET app definiert und konfiguriert, wenn die app wird gestartet. Eine Route kann optional Extrahieren von Werten aus der URL, die in der Anforderung enthalten sind, und diese Werte können dann für die anforderungsverarbeitung verwendet werden. Mit Routeninformationen zur aus der ASP.NET app ist die Routingfunktion auch zum Generieren von URLs, die Routenhandler zuordnen können. Aus diesem Grund kann routing eine Routenhandler basierend auf einer URL oder einen bestimmten Routenhandler basierend auf Routeninformationen-Handler entsprechende URL gefunden werden.

>[!IMPORTANT]
> Dieses Dokument behandelt die low-Level ASP.NET Core routing. ASP.NET Core MVC-routing, finden Sie unter [Routing an Controlleraktionen](../mvc/controllers/routing.md)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="routing-basics"></a>Routing-Grundlagen

Routing verwendet *Routen* (Implementierungen von [IRouter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.routing.irouter)) an:

* Zuordnen von eingehenden Anforderungen an *Handler weiterleiten*

* Generieren von URLs, die in Antworten verwendet werden.

Im Allgemeinen gibt es eine app eine einzelne Auflistung von Routen. Wenn eine Anforderung eingeht, wird die Auflistung von Routen in der Reihenfolge verarbeitet. Die eingehende Anforderung für eine Route, die durch Aufrufen die Anforderungs-URL entspricht sieht die `RouteAsync` -Methode für jedes verfügbare Route in der Auflistung von Routen. Eine Antwort kann dagegen routing zum Generieren von URLs (z. B. für Umleitung oder Links) basierend auf Routeninformationen verwenden und somit zu vermeiden hartcodierter URLs, was, Verwaltbarkeit hilft.

Routing verbunden ist, um die [Middleware](middleware.md) pipeline nach dem `RouterMiddleware` Klasse. [ASP.NET MVC](../mvc/overview.md) routing für die middlewarepipeline als Teil der Konfiguration hinzugefügt. Zur Verwendung als eigenständige Komponente routing finden Sie unter [mithilfe-routing-Middleware](#using-routing-middleware).

<a name="url-matching-ref"></a>

### <a name="url-matching"></a>URL-Abgleich

Der Prozess, durch welche routing sendet eine eingehende Anforderung zum, Abgleich von URL ist eine *Handler*. Dieser Prozess wird im Allgemeinen basierend auf Daten in der URL-Pfad, aber erweitert werden kann, so, dass alle Daten in der Anforderung. Die Fähigkeit, dispatch-Anforderungen an die Handler zu trennen ist entscheidend skalieren die Größe und Komplexität einer Anwendung.

Eingehende Anforderungen Geben Sie die `RouterMiddleware`, welche Aufrufe die `RouteAsync` -Methode für jede Route in der Sequenz. Die `IRouter` Instanz auswählt, ob *behandeln* der Anforderung durch Festlegen der `RouteContext.Handler` zu einer nicht-Null `RequestDelegate`. Wenn eine Route einen Handler für die Anforderung festlegt, wird Route, die Verarbeitung beendet und den Ereignishandler zur Verarbeitung der Anforderung aufgerufen. Wenn alle Routen versucht werden soll, und kein Handler, für die Anforderung gefunden wird, die Middleware ruft *Weiter* und die nächste Middleware in der Anforderungspipeline aufgerufen wird.

Die primäre Eingabe `RouteAsync` ist die `RouteContext.HttpContext` mit der aktuellen Anforderung verknüpft sind. Die `RouteContext.Handler` und `RouteContext.RouteData` sind Ausgaben, die nach einer Route entspricht festgelegt werden.

Eine Übereinstimmung bei `RouteAsync` wird außerdem die Eigenschaften der Festlegen der `RouteContext.RouteData` auf die entsprechenden Werte basierend auf der Verarbeitung der Anforderung bisher. Wenn eine Route einer Anforderung entspricht dem `RouteContext.RouteData` enthält wichtige Informationen zu den *Ergebnis*.

`RouteData.Values`ist ein Wörterbuch von *Routenwerte* nutzen, die aus der Route. Diese Werte werden in der Regel durch versehen die URL bestimmt und können verwendet werden, um Benutzereingaben akzeptieren oder für weiter verteilen Entscheidungen in der Anwendung.

`RouteData.DataTokens`wird ein Eigenschaftenbehälter, der zusätzliche Daten, die im Zusammenhang mit der übereinstimmenden Route an. `DataTokens`Dient zur Zuordnung von Status zu unterstützen, die Daten mit jede Route, damit die Anwendung später basierend Entscheidungen kann auf der route verglichen. Diese Werte sind Entwickler definiert, und führen Sie **nicht** beeinflussen das Verhalten des Routings in keiner Weise. Darüber hinaus können in Datentoken Boyer Werte eines beliebigen Typs, im Gegensatz zu Routenwerte, sein, die problemlos konvertiert in und aus Zeichenfolgen.

`RouteData.Routers`ist eine Liste der Routen, die in den Abgleich erfolgreich der Anforderungs Layoutdurchlauf. Routen können in einer anderen geschachtelt sein und die `Routers` -Eigenschaft entspricht den Pfad in der logischen Struktur des Routen, die in einer Übereinstimmung geführt haben. Im Allgemeinen das erste Element im `Routers` wird die Auflistung von Routen sowie für URL-Generierung verwendet werden soll. Das letzte Element im `Routers` der Routenhandler, die abgeglichen wird.

### <a name="url-generation"></a>URL-Generierung

URL-Generierung ist der Prozess, durch welche routing einen URL-Pfad basierend auf einem Satz von Routenwerten erstellen können. Dies ermöglicht eine logische Trennung zwischen Ihrer Handler und die URLs, die darauf zugreifen.

URL-Generierung folgt einen ähnlichen iterativen Prozess, startet aber durch Benutzer oder Framework-Code Aufrufe in die `GetVirtualPath` -Methode der routenauflistung. Jede *Route* dann seine `GetVirtualPath` nacheinander aufgerufen, bis ein Wert ungleich Null `VirtualPathData` zurückgegeben wird.

Die primäre Eingaben `GetVirtualPath` sind:

* `VirtualPathContext.HttpContext`

* `VirtualPathContext.Values`

* `VirtualPathContext.AmbientValues`

Routen werden in erster Linie die Routenwerte gebotenen verwenden die `Values` und `AmbientValues` entscheiden, wo es möglich, eine URL zu generieren ist und welche Werte enthalten. Die `AmbientValues` sind der Satz von Routenwerte, die von einem Abgleich die aktuelle Anforderung mit dem routing System erzeugt wurden. Im Gegensatz dazu `Values` die Routenwerte, die angeben, wie die gewünschte URL für den aktuellen Vorgang generiert werden. Die `HttpContext` wird bereitgestellt, für den Fall, dass eine Route, Dienste oder zusätzliche Daten, die mit dem aktuellen Kontext verknüpft sind abrufen muss.

Tipp: Stellen Sie sich `Values` als eine Reihe von Außerkraftsetzungen für die `AmbientValues`. URL-Generierung versucht, die Routenwerte aus der aktuellen Anforderung, die zum Generieren von URLs für Links, die mit dem gleichen Route oder Routenwerte erleichtern wiederverwenden.

Die Ausgabe des `GetVirtualPath` ist eine `VirtualPathData`. `VirtualPathData`ist eine parallele Ausführung von `RouteData`; er enthält die `VirtualPath` für die Ausgabe-URL als auch die einige zusätzlichen Eigenschaften, die von der Route festgelegt werden soll.

Die `VirtualPathData.VirtualPath` Eigenschaft enthält die *virtuellen Pfad* erzeugt, die von der Route. Je nach Ihren Anforderungen müssen Sie möglicherweise den Pfad weiter zu verarbeiten. Wenn die generierte URL in HTML gerendert werden sollen müssen Sie z. B. den Basispfad der Anwendung voranstellen.

Die `VirtualPathData.Router` ist ein Verweis auf die Route, die die URL wurde erfolgreich generiert.

Die `VirtualPathData.DataTokens` Eigenschaften ist ein Wörterbuch von zusätzlichen Daten, die im Zusammenhang mit der Route, die die URL generiert. Dies ist die parallele Ausführung von `RouteData.DataTokens`.

### <a name="creating-routes"></a>Erstellen von Routen

Das Routing bietet die `Route` Klasse als die standardmäßige Implementierung des `IRouter`. `Route`verwendet die *routenvorlage* Syntax, um Muster zu definieren, die für den URL-Pfad entsprechen Wenn `RouteAsync` aufgerufen wird. `Route`die gleichen routenvorlage zum Generieren einer URL verwendet. wenn `GetVirtualPath` aufgerufen wird.

Die meisten Anwendungen werden durch Aufrufen von Routen erstellen `MapRoute` oder eines der ähnlicher Erweiterungsmethoden, die auf definierten `IRouteBuilder`. Alle diese Methoden erstellt eine Instanz des `Route` und die routenauflistung hinzuzufügen.

Hinweis: `MapRoute` einen Routenparameter Handler - benötigt er fügt nur die Routen, die von verarbeitet werden die `DefaultHandler`. Da der Standardhandler ist ein `IRouter`, er kann entscheiden, nicht zur Verarbeitung der Anforderung. ASP.NET MVC ist beispielsweise in der Regel so konfiguriert, wie ein Standardhandler, der nur verarbeitet, bei denen ein verfügbaren Controller und Aktion anfordert. Weitere Informationen zu MVC-routing finden Sie unter [Routing an Controlleraktionen](../mvc/controllers/routing.md).

Dies ist ein Beispiel für eine `MapRoute` Aufruf verwendet wird, von einem typischen ASP.NET MVC-Route-Definition:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Diese Vorlage wird einen URL-Pfad, z. B. entsprechen `/Products/Details/17` und extrahieren Sie die Routenwerte `{ controller = Products, action = Details, id = 17 }`. Die Routenwerte hängen von der URL-Pfad in Segmente aufteilen und zum Abgleich jedes Segment mit dem *Routenparameter* Name in der routenvorlage. Routenparameter heißen. Sie sind durch Schließen den Namen des Parameters in geschweifte Klammern definiert `{ }`.

Die Vorlage, die oben genannten übereinstimmen könnten auch die URL-Pfad `/` und es wird die Werte `{ controller = Home, action = Index }`. Dies liegt daran, dass die `{controller}` und `{action}` Routenparameter weisen Standardwerte, und die `id` Routenparameter ist optional. Ein Gleichheitszeichen `=` Zeichen gefolgt von einem Wert, nachdem die Namen der Route-Parameter einen Standardwert für den Parameter definiert. Ein Fragezeichen `?` nach der Namen der Route-Parameter den Parameter als optional definiert. Weiterleiten von Parametern mit einem Standardwert *immer* ein routenwert erzeugen, wenn die Route übereinstimmt: optionale Parameter wird kein routenwert erzeugen, wenn es keine entsprechende URL-OData-Pfadsegment wurde.

Finden Sie unter [Route-vorlagenreferenz](#route-template-reference) für eine ausführliche Beschreibung der Route Vorlagenfeatures und Syntax.

Dieses Beispiel umfasst ein *weiterleiten Einschränkung*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Diese Vorlage wird einen URL-Pfad, z. B. entsprechen `/Products/Details/17`, aber nicht `/Products/Details/Apples`. Die Route Parameterdefinition `{id:int}` definiert eine *weiterleiten Einschränkung* für die `id` Routenparameter. Implementieren Sie die routeneinschränkungen `IRouteConstraint` , und überprüfen Sie die Routenwerte, um sie zu überprüfen. In diesem Beispiel wird der routenwert `id` müssen in eine ganze Zahl konvertiert werden. Finden Sie unter [Route-Einschränkung-Reference](#route-constraint-reference) für eine ausführlichere Erläuterung der routeneinschränkungen, die vom Framework bereitgestellt werden.

Zusätzliche Überladungen der `MapRoute` akzeptieren Sie die Werte für `constraints`, `dataTokens`, und `defaults`. Von diesen zusätzlichen Parametern `MapRoute` werden als Datentyp definierte `object`. Die typische Nutzung dieser Parameter ist ein anonym typisiertes Objekt übergeben, in dem die Eigenschaftsnamen des anonymen Typs Übereinstimmung Parameternamen weiterzuleiten.

Die beiden folgenden Beispiele erstellen Sie entsprechende Routen:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Tipp: Die Inline-Syntax zum Definieren von Einschränkungen und Standardwerte kann für einfache Routen bequemer sein. Es gibt jedoch Funktionen, z. B. Datentoken, die nicht von Inline-Syntax unterstützt werden.

In diesem Beispiel wird veranschaulicht, ein paar weitere Funktionen:

```csharp
routes.MapRoute(
  name: "blog",
  template: "Blog/{*article}",
  defaults: new { controller = "Blog", action = "ReadArticle" });
```

Diese Vorlage wird einen URL-Pfad, z. B. entsprechen `/Blog/All-About-Routing/Introduction` und extrahiert die Werte `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Die Standardroute Werte für `controller` und `action` werden von der Route erstellt, obwohl keine entsprechende Routenparameter in der Vorlage vorhanden sind. Standardwerte können in der routenvorlage angegeben werden. Die `article` Routenparameter ist definiert als eine *sammelfehlermeldung* durch die Darstellung der ein Sternchen `*` vor dem Namen der Route-Parameter. Catchall-Routenparameter erfassen den Rest des URL-Pfads, und können auch die leere Zeichenfolge übereinstimmen.

In diesem Beispiel wird die Route Einschränkungen und Data-Token:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Diese Vorlage wird einen URL-Pfad, z. B. entsprechen `/Products/5` und extrahiert die Werte `{ controller = Products, action = Details, id = 5 }` und die Datentoken `{ locale = en-US }`.

![Windows-Token "lokal"](routing/_static/tokens.png)

<a name="id1"></a>

### <a name="url-generation"></a>URL-Generierung

Die `Route` Klasse kann auch die URL-Generierung durchführen, indem Sie einen Satz von Routenwerten mit seiner routenvorlage kombinieren. Dies ist logisch der umgekehrte Vorgang des URL-Pfad entsprechen.

Tipp: Angenommen Sie, um die URL-Generierung besser zu verstehen, welche URL zu generieren und dann überlegen, wie eine routenvorlage für die diese URL übereinstimmt. Welche Werte würde erstellt werden? Dies entspricht dem grobe Funktionsweise der URL-Generierung von der `Route` Klasse.

In diesem Beispiel wird eine grundlegende ASP.NET MVC-Stil Route verwendet:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Mit den Routenwerten `{ controller = Products, action = List }`, diese Route wird die URL generiert `/Products/List`. Die Routenwerte werden durch die entsprechenden Routenparameter zu einem URL-Pfad kombiniert ersetzt. Da `id` ist eine optionale Parameter weiterleiten, ist es kein Problem, dass sie nicht über einen Wert verfügt.

Mit den Routenwerten `{ controller = Home, action = Index }`, diese Route wird die URL generiert `/`. Die Routenwerte, die bereitgestellt wurden entsprechen die Standardwerte, damit die Segmente, die für diese Werte problemlos ausgelassen werden können. Beachten Sie, dass beide URLs generiert Round-Trip mit dieser Route-Definition würden und erzeugen die gleichen Routenwerte, die zum Generieren der URL verwendet wurden.

Tipp: Die app mithilfe von ASP.NET MVC sollten verwenden `UrlHelper` zum Generieren von URLs statt direkt routing aufzurufen.

Weitere Informationen zu der URL-Generierungsprozess, finden Sie unter [Url-Generierung-Reference](#url-generation-reference).

## <a name="using-routing-middleware"></a>Verwenden von Routing Middleware

Fügen Sie das NuGet-Paket "Microsoft.AspNetCore.Routing" hinzu.

Fügen Sie dem Dienstcontainer in routing *Startup.cs*:

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?highlight=3&start=11&end=14)]

Routen müssen konfiguriert werden, der `Configure` Methode in der `Startup` Klasse. Im Beispiel unten verwendet diese APIs:

* `RouteBuilder`
* `Build`
* `MapGet`Nur HTTP GET-Anforderungen erfüllt
* `UseRouter`

```csharp
public void Configure(IApplicationBuilder app, ILoggerFactory loggerFactory)
{
    var trackPackageRouteHandler = new RouteHandler(context =>
    {
        var routeValues = context.GetRouteData().Values;
        return context.Response.WriteAsync(
            $"Hello! Route values: {string.Join(", ", routeValues)}");
    });

    var routeBuilder = new RouteBuilder(app, trackPackageRouteHandler);

    routeBuilder.MapRoute(
        "Track Package Route",
        "package/{operation:regex(^(track|create|detonate)$)}/{id:int}");

    routeBuilder.MapGet("hello/{name}", context =>
    {
        var name = context.GetRouteValue("name");
        // This is the route handler when HTTP GET "hello/<anything>"  matches
        // To match HTTP GET "hello/<anything>/<anything>,
        // use routeBuilder.MapGet("hello/{*name}"
        return context.Response.WriteAsync($"Hi, {name}!");
    });

    var routes = routeBuilder.Build();
    app.UseRouter(routes);
}
```

Die folgende Tabelle zeigt die Antworten mit den angegebenen URIs.

| URI | Antwort  |
| ------- | -------- |
| /package/create/3  | Hallo! Routenwerte: [Vorgang erstellen], [ID: 3] |
| /package/track/-3  | Hallo! Routenwerte: [Vorgang, Nachverfolgen] [-Id,-3] |
| /package/track/-3/ | Hallo! Routenwerte: [Vorgang, Nachverfolgen] [-Id,-3]  |
| /package/track/ | \<Über diesen Bericht keine Übereinstimmung fallen > |
| /Hello/Joe abrufen | Hallo, Joe! |
| POST /hello/Joe | \<Fortfahren, nur HTTP GET entspricht > |
| /Hello/Joe/Smith abrufen | \<Über diesen Bericht keine Übereinstimmung fallen > |

Wenn Sie eine einzelne Route konfigurieren, rufen Sie `app.UseRouter` übergibt eine `IRouter` Instanz. Sie müssen keine Aufrufen `RouteBuilder`.

Das Framework bietet einen Satz von Erweiterungsmethoden für z. B. Erstellen von Routen:

* `MapRoute`
* `MapGet`
* `MapPost`
* `MapPut`
* `MapDelete`
* `MapVerb`

Einige dieser Methoden wie z. B. `MapGet` erfordern eine `RequestDelegate` bereitgestellt werden. Die `RequestDelegate` verwendet werden, als die *Routenhandler* Wenn die Route entspricht. Andere Methoden in dieser Familie können konfiguriert werden, eine middlewarepipeline, die als den Routenhandler verwendet werden soll. Wenn die *Zuordnung* Methode einen Handler, wie z. B. akzeptiert keine `MapRoute`, verwendet er die `DefaultHandler`.

Die `Map[Verb]` Methoden Einschränkungen verwenden, um die Route, die dem HTTP-Verb im Methodennamen zu beschränken. Beispielsweise finden Sie unter [MapGet](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L85-L88) und [MapVerb](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/RequestDelegateRouteBuilderExtensions.cs#L156-L180).

## <a name="route-template-reference"></a>Route-Vorlagenreferenz

Token in der geschweiften Klammern (`{ }`) definieren *Parameter weiterleiten* gebunden werden wird, wenn die Route verglichen wird. Sie können mehr als ein Routenparameter in einem Segment Route definieren, aber sie müssen durch einen Literalwert getrennt werden. Z. B. `{controller=Home}{action=Index}` wäre eine gültige Route nicht, da es kein Literalwert zwischen ist `{controller}` und `{action}`. Diese Routenparameter müssen einen Namen aufweisen, und haben möglicherweise zusätzliche Attribute angegeben.

Literaltext als Routenparameter (z. B. `{id}`) und die Pfadtrennzeichen `/` muss den Text in der URL übereinstimmen. Text Abgleich wird die Groß-/Kleinschreibung und basiert auf die decodierte Darstellung des Pfads URLs. Das literal Route Parameter Trennzeichen entsprechend `{` oder `}`, es durch die Wiederholung des Zeichens mit Escapezeichen versehen (`{{` oder `}}`).

URL-Muster, die versucht, einen Dateinamen mit der Erweiterung optional aufzuzeichnen haben weitere Aspekte zu berücksichtigen. Beispiel für die Verwendung der Vorlage `files/{filename}.{ext?}` – im Falle beide `filename` und `ext` vorhanden ist, werden beide Werte aufgefüllt werden. Wenn nur `filename` in der URL entspricht der Route vorhanden ist, weil der Punkt `.` ist optional. Die folgenden URLs würden diese Route übereinstimmen:

* `/files/myFile.txt`
* `/files/myFile.`
* `/files/myFile`

Können Sie die `*` -Zeichen als Präfix auf einen Routenparameter zum Binden an den Rest des URI - heißt dies eine *Catchall-* Parameter. Z. B. `blog/{*slug}` entsprechen einen beliebigen URI, die mit gestartet `/blog` ausnahmslos darauf folgende Waren (die zugewiesen werden die `slug` weiterleiten Wert). Catch-All-Parameter können auch mit der leeren Zeichenfolge entsprechen.

Routenparameter möglicherweise *Standardwerte*, festgelegten durch Angeben der standardmäßigen nach dem Parameternamen, getrennt durch ein `=`. Beispielsweise `{controller=Home}` würden definieren `Home` als Standardwert für `controller`. Der Standardwert wird verwendet, wenn kein Wert für den Parameter in der URL enthalten ist. Zusätzlich zu den Standardwerten, Routenparameter ausgelassen werden können (angegeben durch Anfügen einer `?` bis zum Ende des Parameternamens als in `id?`). Der Unterschied zwischen optional und "verfügt über default" ist, dass ein Routenparameter hat den Standardwert immer einen Wert erzeugt; Ein optionaler Parameter hat einen Wert nur, wenn eine bereitgestellt wird.

Routenparameter möglicherweise auch Einschränkungen, die den routenwert gebunden wird, von der URL übereinstimmen muss. Hinzufügen eines Doppelpunkts `:` und nach der Namen der Route-Parameter gibt an, auf den Namen der Einschränkung einer *Inlineeinschränkung* auf einen Routenparameter. Wenn die Einschränkung Argumente die bereitgestellt erfordert werden, in Klammern eingeschlossen `( )` nach den Namen der Einschränkung. Mehrere inlineeinschränkungen können angegeben werden, durch Anfügen einer anderen Doppelpunkt `:` und Name der Einschränkung. Der Einschränkungsname wird zum Übergeben der `IInlineConstraintResolver` Dienst zum Erstellen einer Instanz von `IRouteConstraint` in URL-Verarbeitung verwendet. Z. B. die routenvorlage `blog/{article:minlength(10)}` gibt an, die `minlength` Einschränkung mit dem Argument `10`. Weitere Beschreibung routeneinschränkungen, sowie eine Liste der Einschränkungen von Framework bereitgestellt werden, finden Sie unter [Route-Einschränkung-Reference](#route-constraint-reference).

Die folgende Tabelle enthält einige routenvorlagen und deren Verhalten.

| Routenvorlage | Übereinstimmende Beispiel-URL | Hinweise |
| -------- | -------- | ------- |
| hello  | /hello  | Nur entspricht der einzelnen Pfad`/hello` |
| {Seite = Home} | / | Entspricht, und legt `Page` an`Home` |
| {Seite = Home}  | /Contact  | Entspricht, und legt `Page` an`Contact` |
| {controller}/{action}/{id?} | /Products/List | Ordnet `Products` Controller und `List` Aktion |
| {controller}/{action}/{id?} | /Products/Details/123  |  Ordnet `Products` Controller und `Details` Aktion.  `id`Legen Sie auf 123 |
| {Controller = Home} / {Aktion = Index} / {Id}? | /  |  Ordnet `Home` Controller und `Index` -Methode. `id` wird ignoriert. |

Mithilfe einer Vorlage wird im Allgemeinen die einfachste Vorgehensweise zum routing. Einschränkungen und Standardwerte können auch außerhalb der routenvorlage angegeben werden.

Tip: Aktivierung der [Protokollierung](xref:fundamentals/logging/index) angezeigt wie die, wie z. B. in routing Implementierungen `Route`, Anforderungen entsprechen.

## <a name="route-constraint-reference"></a>Route-Einschränkung Verweis

Routeneinschränkungen ausgeführt, wenn eine `Route` verfügt über die Syntax der eingehenden URL abgeglichen und den URL-Pfad in Routenwerte in Token übersetzt. Routeneinschränkungen ist im Allgemeinen die routenwert verknüpft sind, über die routenvorlage untersuchen, und stellen eine einfache Ja/Nein Entscheidung zu, und zwar unabhängig davon, ob der Wert zulässig ist. Einige routeneinschränkungen verwendet Daten außerhalb der routenwert überlegen, ob die Anforderung weitergeleitet werden kann. Z. B. die `HttpMethodRouteConstraint` annehmen oder Ablehnen einer Anforderung basierend auf der HTTP-Verb können.

>[!WARNING]
> Vermeiden Sie die Verwendung von Einschränkungen für **input Validation**, da dies bedeutet, ungültige Eingabe 404 (Nichtgefunden) anstatt mit einer entsprechenden Fehlermeldung 400 Bad Request führt. Routeneinschränkungen muss zur **eindeutig** zwischen ähnlichen Routen, nicht um die Eingaben für eine bestimmte Route zu überprüfen.

Die folgende Tabelle enthält einige routeneinschränkungen und des erwarteten Verhaltens.

| Einschränkung | Beispiel | Beispiel für Übereinstimmungen | Hinweise |
| --------   | ------- | ------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789`  | Eine beliebige ganze Zahl entspricht |
| `bool`  | `{active:bool}` | `true`, `FALSE` | Übereinstimmungen `true` oder `false` (Groß-/Kleinschreibung) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm`  | Ein gültiger entspricht `DateTime` Wert (in der invarianten Kultur - Warnung angezeigt) |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Ein gültiger entspricht `decimal` Wert (in der invarianten Kultur - Warnung angezeigt) |
| `double`  | `{weight:double}` | `1.234`, `-1,001.01e8` | Ein gültiger entspricht `double` Wert (in der invarianten Kultur - Warnung angezeigt) |
| `float`  | `{weight:float}` | `1.234`, `-1,001.01e8` | Ein gültiger entspricht `float` Wert (in der invarianten Kultur - Warnung angezeigt) |
| `guid`  | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Ein gültiger entspricht `Guid` Wert |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Ein gültiger entspricht `long` Wert |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Zeichenfolge muss mindestens 4 Zeichen umfassen. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Zeichenfolge muss nicht mehr als 8 Zeichen lang sein. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Zeichenfolge muss genau 12 Zeichen lang sein. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Zeichenfolge muss mindestens 8 bis maximal 16 Zeichen lang sein. |
| `min(value)` | `{age:min(18)}` | `19` | Integer-Wert muss mindestens 18 sein. |
| `max(value)` | `{age:max(120)}` |  `91` | Integer-Wert muss nicht mehr als 120 sein. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Integer-Wert muss mindestens 18, aber nicht mehr als 120 sein. |
| `alpha` | `{name:alpha}` | `Rick` | Zeichenfolge muss mindestens eine alphabetische Zeichen bestehen (`a`-`z`, Groß-/Kleinschreibung) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Zeichenfolge muss den regulären Ausdruck entsprechen (Siehe Tipps zum Definieren eines regulären Ausdrucks) |
| `required`  | `{name:required}` | `Rick` |  Zum durchsetzen, dass ein Parameterwert während der URL-Generierung vorhanden ist. |

>[!WARNING]
> Routeneinschränkungen, die die URL überprüfen Sie auf einen CLR-Typ konvertiert werden können (z. B. `int` oder `DateTime`) immer die invariante Kultur verwenden – wird davon ausgegangen, die URL ist nicht lokalisiert. Die routeneinschränkungen Framework bereitgestellten ändern Sie nicht die Werte in Routenwerte gespeichert. Alle Routenwerte analysiert, die von der URL werden als Zeichenfolgen gespeichert werden. Z. B. die ["float" routeneinschränkung](https://github.com/aspnet/Routing/blob/1.0.0/src/Microsoft.AspNetCore.Routing/Constraints/FloatRouteConstraint.cs#L44-L60) wird versucht, den routenwert in einen float-Wert konvertiert, aber der konvertierte Wert dient nur zu überprüfen, ob sie die in einen float-Wert konvertiert werden kann.

## <a name="regular-expressions"></a>Reguläre Ausdrücke 

Fügt das Framework ASP.NET Core `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` an den Konstruktor für reguläre Ausdrücke. Finden Sie unter [RegexOptions-Enumeration](https://docs.microsoft.com/dotnet/api/system.text.regularexpressions.regexoptions) eine Beschreibung dieser Member.

Verwendung von regulären Ausdrücken, Trennzeichen und Token von Routing und die Programmiersprache c# ähnelt. Reguläre Ausdrücke Token müssen mit Escapezeichen versehen werden. Beispielsweise, um den regulären Ausdruck verwenden `^\d{3}-\d{2}-\d{4}$` im Routing, es erfordert das `\` Zeichen eingegeben als `\\` in der C#-Quelldatei als Escapesequenz für die `\` string Escape-Zeichen (außer bei [wörtliche Zeichenfolgenliterale](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/string). Die `{` , `}` , ' [' und ']' Zeichen mit Escapezeichen versehen werden, indem verdoppelt, um die Begrenzungszeichen der Routing-Parameter mit Escapezeichen versehen werden müssen.  Die folgende Tabelle zeigt einen regulären Ausdruck und die mit Escapezeichen versehene Version.

| Ausdruck               | Hinweis |
| ----------------- | ------------ | 
| `^\d{3}-\d{2}-\d{4}$` | Regulärer Ausdruck |
| `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` | Escapezeichen  |
| `^[a-z]{2}$` | Regulärer Ausdruck |
| `^[[a-z]]{{2}}$` | Escapezeichen  |

Reguläre Ausdrücke, die beim routing verwendet häufig beginnt mit der `^` Zeichen (Übereinstimmung, die Anfangsposition der Zeichenfolge) und enden mit der `$` Zeichen (Übereinstimmung Endposition der Zeichenfolge). Die `^` und `$` Zeichen sicher, dass die Übereinstimmung des regulären Ausdrucks den gesamten Übertragungsweg Parameterwert. Ohne die `^` und `$` Zeichen in der reguläre Ausdruck entspricht jeder Teilzeichenfolge innerhalb der Zeichenfolge, die häufig nicht gewünscht ist. In der folgenden Tabelle sind einige Beispiele für und erläutert, warum sie übereinstimmen oder nicht übereinstimmen.

| Ausdruck               | Zeichenfolge | Match | Kommentar |
| ----------------- | ------------ |  ------------ |  ------------ | 
| `[a-z]{2}` | hello | ja | Teilzeichenfolge Übereinstimmungen |
| `[a-z]{2}` | 123abc456 | ja | Teilzeichenfolge Übereinstimmungen |
| `[a-z]{2}` | mz | ja | entspricht dem Ausdruck |
| `[a-z]{2}` | MZ | ja | keine Groß-/Kleinschreibung unterschieden. |
| `^[a-z]{2}$` |  hello | Nein | finden Sie unter `^` und `$` oben |
| `^[a-z]{2}$` |  123abc456 | Nein | finden Sie unter `^` und `$` oben |

Verweisen auf [reguläre Ausdrücke von .NET Framework](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) für Weitere Informationen zu Syntax regulärer Ausdrücke.

Verwenden Sie einen regulären Ausdruck, um einen Parameter mit einem bekannten Satz möglicher Werte zu beschränken. Z. B. `{action:regex(^(list|get|create)$)}` nur entspricht der `action` weiterleiten Wert `list`, `get`, oder `create`. Wenn übergebene Einschränkungen Wörterbuch vorhanden ist, die Zeichenfolge "^ (Liste | Get | erstellen) $" entspräche. Einschränkungen, die im Wörterbuch Einschränkungen (nicht Inline in einer Vorlage) übergeben werden, die eine der bekannten Einschränkungen nicht übereinstimmen, werden ebenfalls als reguläre Ausdrücke behandelt.

## <a name="url-generation-reference"></a>URL-Generierung-Verweis

Im folgenden Beispiel wird gezeigt, wie auf einen Link zu einer Route, die ein Wörterbuch mit Routenwerten für die angegebene generieren und ein `RouteCollection`.

[!code-csharp[Main](../fundamentals/routing/sample/RoutingSample/Startup.cs?range=45-59)]

Die `VirtualPath` wird am Ende des oben genannten Beispiels generiert `/package/create/123`.

Der zweite Parameter für die `VirtualPathContext` Konstruktor ist eine Auflistung von *ambient Werte*. Ambient Werte bereitstellen halber Einschränken der Anzahl der Werte, die ein Entwickler in einem bestimmten Anforderungskontext angeben muss. Die aktuelle Routenwerte der aktuellen Anforderung gelten linkgenerierung ambient-Werte. Z. B. in einer ASP.NET MVC-app, wenn Sie sich auf die `About` Aktion der `HomeController`, müssen Sie nicht zum Angeben des Werts des Controller-Route mit Verknüpfen der `Index` Aktion (die ambient-Wert des `Home` verwendet werden).

Ambient Werte, die einen Parameter nicht übereinstimmen, werden ignoriert und die ambient-Werte werden auch ignoriert, wenn ein Wert explizit bereitgestellte, überschreibt den Wechsel von links nach rechts in der URL.

Die Abfragezeichenfolge werden Werte, die explizit bereitgestellt werden, aber die stimmen nicht überein. nichts, hinzugefügt. Die folgende Tabelle zeigt das Ergebnis aus, wenn die routenvorlage mit `{controller}/{action}/{id?}`.

| Ambient-Werte | Explizite Werte | Ergebnis |
| -------------   | -------------- | ------ |
| controller="Home" | Aktion = "Info" | `/Home/About` |
| controller="Home" | controller="Order",action="About" | `/Order/About` |
| Controller = "Home", Color = "Red" | Aktion = "Info" | `/Home/About` |
| controller="Home" | Aktion = "About" color = "Red" | `/Home/About?color=Red`

Wenn eine Route hat den Standardwert, der einen Parameter entsprechen nicht der Wert explizit angegeben, muss er den Standardwert übereinstimmen. Zum Beispiel:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
  defaults: new { controller = "Blog", action = "ReadPost" });
```

Linkgenerierung würde nur einen Link für diese Route generiert, wenn die entsprechenden Werte für Controller und Aktion bereitgestellt werden.
