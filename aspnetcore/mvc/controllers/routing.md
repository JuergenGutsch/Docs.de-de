---
title: Routing zum Controlleraktionen
author: rick-anderson
description: 
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/routing
ms.openlocfilehash: cc3277400aee956f47c53e5a4f3d4e84d3a3d1a3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="routing-to-controller-actions"></a>Routing zum Controlleraktionen

Durch [Ryan Nowak](https://github.com/rynowak) und [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC verwendet Routing [Middleware](../../fundamentals/middleware.md) die URLs der eingehende Anforderungen entsprechen und Aktionen zuordnen. Routen werden in der Startcode oder Attribute definiert. Routen wird beschrieben, wie URL-Pfade auf Aktionen abgeglichen werden sollen. Routen werden auch verwendet, zum Generieren von URLs (für Links), die in Antworten gesendet werden. 

Aktionen werden konventionell entweder weitergeleitet oder Attribut weitergeleitet. Platzieren eine Route auf dem Controller oder die Aktion erleichtert Attribut weitergeleitet. Finden Sie unter [gemischten routing](#routing-mixed-ref-label) für Weitere Informationen.

Dieses Dokument wird erläutert, die Interaktionen zwischen MVC und routing und wie normale MVC-apps stellen routing-Funktionen. Finden Sie unter [Routing](xref:fundamentals/routing) Informationen zu erweiterten routing.

## <a name="setting-up-routing-middleware"></a>Einrichten von Middleware Routing

In Ihrem *konfigurieren* Methode möglicherweise Code ähnelt:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Beim Aufruf von `UseMvc`, `MapRoute` wird verwendet, um eine einzelne Route erstellen, die wir auf als verweisen müssen die `default` Route. Die meisten MVC-apps verwendet eine Route mit einer Vorlage ähnelt der `default` Route.

Die routenvorlage `"{controller=Home}/{action=Index}/{id?}"` kann einen URL-Pfad, z. B. entsprechen `/Products/Details/5` und extrahiert die Routenwerte `{ controller = Products, action = Details, id = 5 }` durch den Pfad zu versehen. MVC versucht, einen Controller mit dem Namen suchen `ProductsController` , und führen Sie die Aktion `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Beachten Sie, dass in diesem Beispiel wurden die modellbindung den Wert der verwenden würden `id = 5` festzulegende der `id` Parameter `5` diese Aktion aufrufen. Finden Sie unter der [Modell Bindung](../models/model-binding.md) Weitere Details.

Mithilfe der `default` Route:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Die routenvorlage:

* `{controller=Home}`definiert `Home` als Standard`controller`

* `{action=Index}`definiert `Index` als Standard`action`

* `{id?}`definiert `id` als optional

Standard- und optionale Routenparameter müssen nicht in der URL-Pfad für eine Übereinstimmung vorhanden sein. Finden Sie unter [Route Vorlagenverweis](../../fundamentals/routing.md#route-template-reference) für eine ausführliche Beschreibung der Syntax der Route-Vorlage.

`"{controller=Home}/{action=Index}/{id?}"`kann den URL-Pfad entsprechen `/` und die Routenwerte entsteht `{ controller = Home, action = Index }`. Die Werte für `controller` und `action` stellen die Standardwerte verwenden `id` keine Wert erzeugt, da keine entsprechende Segment in der URL-Pfad vorhanden ist. MVC würden diese Routenwerte verwenden, um Wählen Sie die `HomeController` und `Index` Aktion:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Mit diesem Controller Definition und die routenvorlage, die `HomeController.Index` Aktion wird für keines der folgenden URL-Pfade ausgeführt werden:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Die Hilfsmethode `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Kann verwendet werden, um zu ersetzen:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`und `UseMvcWithDefaultRoute` fügen eine Instanz des `RouterMiddleware` die Middleware-Pipeline. MVC interagiert nicht direkt mit Middleware und routing verwendet, um Anforderungen zu verarbeiten. MVC verbunden ist, auf die Routen über eine Instanz des `MvcRouteHandler`. Der Code innerhalb eines `UseMvc` ähnelt der folgenden:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`Alle Routen nicht direkt definieren die routenauflistung für einen Platzhalter hinzugefügt der `attribute` Route. Die Überladung `UseMvc(Action<IRouteBuilder>)` können Sie Ihre eigenen Routen hinzufügen und unterstützt auch das routing-Attribut.  `UseMvc`und alle seine Varianten Fügt einen Platzhalter für die attributenroute - routing-Attribut ist immer verfügbar, unabhängig von der Konfiguration `UseMvc`. `UseMvcWithDefaultRoute`definiert eine Standardroute und routing-Attribut unterstützt. Die [Routing-Attribut](#attribute-routing-ref-label) Abschnitt enthält weitere Informationen zu routing-Attribut.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Herkömmliche routing

Die `default` Route:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

ist ein Beispiel für eine *konventionellen routing*. Wir rufen Sie dieses Format *konventionellen routing* da-Objektvariablen eine *Konvention* für URL-Pfade:

* das erste Pfadsegment ordnet den Namen des Controllers

* die zweite ordnet den Aktionsnamen.

* Das dritte Segment wird verwendet, einen optionalen `id` verwendet, um eine modellentität zuordnen

Mit diesem `default` Route, die URL-Pfad `/Products/List` ordnet die `ProductsController.List` Aktion und `/Blog/Article/17` ordnet `BlogController.Article`. Diese Zuordnung basiert auf den Controller- und Namen **nur** und wird nicht anhand des Namespaces, Speicherorte für Quelldateien oder Methodenparameter.

> [!TIP]
> Mithilfe der herkömmlichen routing mit die Standardroute können Sie die Anwendung schnell erstellen, ohne Sie zu einem neuen URL-Muster für jede Aktion, die Sie definieren. Für eine Anwendung mit CRUD-Stil-Aktionen können Konsistenz für URLs zu, in Ihren Domänencontrollern Ihren Code vereinfachen die Benutzeroberfläche besser vorhersagbar zu machen.

> [!WARNING]
> Die `id` wird als optional definiert, durch die routenvorlage, was bedeutet, dass Ihre Aktionen werden, ohne die ID, die als Teil der URL bereitgestellt ausgeführt können. In der Regel was passiert, wenn `id` fehlt in der URL ist, dass er festgelegt wird `0` von modellbindung und demzufolge keine Entität befinden sich in der Datenbank entsprechen `id == 0`. Routing-Attribut können Sie die ID ist für einige Aktionen aus, und nicht für andere stellen eine differenzierte Steuerung gewähren. Gemäß der Konvention, die die Dokumentation enthält optionale Parameter wie `id` Wenn sie wahrscheinlich in die richtige Verwendung angezeigt werden.

## <a name="multiple-routes"></a>Mehrere Routen

Sie können mehrere Routen innerhalb von hinzufügen `UseMvc` durch Hinzufügen von mehr Aufrufe zu `MapRoute`. Auf diese Weise können Sie mehrere Konventionen definieren, oder konventionellen Routen hinzu, die einer bestimmten Aktion zugeordnet sind, z. B.:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Die `blog` Route hier ist ein *dedizierten konventionellen Route*, was bedeutet, dass das herkömmliche Routingsystem verwendet, aber für eine bestimmte Aktion dediziert ist. Da `controller` und `action` nicht als Parameter in der routenvorlage angezeigt, können nur die Standardwerte aufweisen und diese Route wird daher immer zugeordnet, auf die Aktion `BlogController.Article`.

Routen in der Auflistung von Routen werden sortiert und verarbeitet werden, in der Reihenfolge, die sie hinzugefügt haben. In diesem Beispiel daher die `blog` Route versucht, bevor die `default` Route.

> [!NOTE]
> *Dedizierte konventionelle Routen* verwenden häufig Catchall-Routenparameter wie `{*article}` zum Erfassen des verbleibenden Teils der URL-Pfad. Damit kann eine Route "zu gierigen", was bedeutet, dass er URLs findet, die Sie von anderen Routen abgeglichen werden sollen. Fügen Sie die "gierigen" Routen weiter unten in der Routingtabelle, um dieses Problem zu beheben.

### <a name="fallback"></a>Fallback

Im Rahmen der anforderungsverarbeitung MVC überprüft, ob die Routenwerte verwendet werden können, auf einem Controller und Aktion in der Anwendung feststellen. Falls die Routenwerte eine Aktion übereinstimmen klicken Sie dann die Route ist kein Übereinstimmung angesehen, und die nächste Route wird versucht. Hierbei spricht *fallback*, und es wurde vorgesehen, um Fälle zu vereinfachen, konventionelle Routen, die sich überlappen.

### <a name="disambiguating-actions"></a>Aktionen eindeutig gemacht

Wenn die beiden Aktionen werden über Weiterleitung durch übereinstimmen, muss MVC eindeutig machen, um wählen "beste" geeignet, da sonst eine Ausnahme auslösen. Zum Beispiel:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Dieser Controller definiert zwei Aktionen, die mit den URL-Pfad übereinstimmen würden `/Products/Edit/17` und Weiterleiten von Daten `{ controller = Products, action = Edit, id = 17 }`. Dies ist ein typisches Muster für MVC-Controller, in denen `Edit(int)` zeigt ein Formular zum Bearbeiten eines Produkts und `Edit(int, Product)` bereitgestellte Formular verarbeitet. Um dies zu ermöglichen MVC auswählen müssen `Edit(int, Product)` bei die Anforderung ist ein HTTP `POST` und `Edit(int)` Wenn das HTTP-Verb ist etwas anderes.

Die `HttpPostAttribute` ( `[HttpPost]` ) ist eine Implementierung von `IActionConstraint` nur die Aktion, die ausgewählt werden, wenn das HTTP-Verb ermöglichen `POST`. Das Vorhandensein einer `IActionConstraint` macht die `Edit(int, Product)` "bessere" übereinstimmen, als `Edit(int)`, sodass `Edit(int, Product)` wird zuerst versucht werden.

Sie müssen nur zum Schreiben von benutzerdefinierten `IActionConstraint` Implementierungen in speziellen Szenarien, aber es wichtig zu verstehen, die Rolle des Attribute wie den `HttpPostAttribute` -ähnliche Attributen für andere HTTP-Verben definiert sind. In herkömmlichen routing wird für Aktionen, die den gleichen Aktionsnamen verwenden, wenn Inhaltspakete Teil von sind häufige eine `show form -> submit form` Workflow. Die Vorteile dieses Muster wird offensichtlich, mehr nach dem Überprüfen der [Verständnis IActionConstraint](#understanding-iactionconstraint) Abschnitt.

Wenn mehrere Routen entsprechen und MVC eine "bewährte" Route kann nicht gefunden werden kann, löst sie eine `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Routennamen

Die Zeichenfolgen `"blog"` und `"default"` in den folgenden Beispielen werden Routennamen:


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Die Routennamen benennen Sie der Route logische, damit für URL-Generierung die benannte Route verwendet werden kann. Dadurch wird URL-Erstellung erheblich vereinfacht, um die Reihenfolge der Routen URL-Generierung kompliziert vornehmen kann. Routennamen müssen anwendungsweite eindeutig sein.

Routennamen haben keine Auswirkung auf die URL entsprechen oder die Verarbeitung von Anforderungen; Sie sind nur für die Erzeugung der URL verwendet. [Routing](xref:fundamentals/routing) enthält weitere ausführliche Informationen zum URL-Generierung, einschließlich der URL-Generierung in MVC-spezifische Hilfsprogramme.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Routing-Attribut

Routing-Attribut verwendet einen Satz von Attributen, um Aktionen routenvorlagen direkt zugeordnet ist. Im folgenden Beispiel `app.UseMvc();` werden in der `Configure` -Methode und keine Route übergeben wird. Die `HomeController` entspricht einen Satz von URLs, welche die Standardroute ähnelt `{controller=Home}/{action=Index}/{id?}` entsprechen:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Die `HomeController.Index()` Aktion wird ausgeführt werden, für den URL-Pfaden `/`, `/Home`, oder `/Home/Index`.

> [!NOTE]
> In diesem Beispiel werden eine programming Hauptunterschied zwischen routing-Attribut und herkömmliche routing hervorgehoben. Routing-Attribut erfordert weitere Eingabe, um eine Route anzugeben. die herkömmliche Standardroute verarbeitet Routen prägnanter. Allerdings routing-Attribut zulässt (und erfordert) präzise Kontrolle der routenvorlagen für jede Aktion gelten.

Mit dem Controllernamen und Aktionsnamen routing-Attribut wiedergeben **keine** Rolle in der Aktion aktiviert ist. In diesem Beispiel werden die gleichen wie im vorherigen Beispiel-URLs abgeglichen.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Die oben genannten routenvorlagen nicht für die Routenparameter definieren `action`, `area`, und `controller`. Diese Routenparameter sind in der Tat in attributenrouten nicht zulässig. Da die routenvorlage bereits mit der Aktion zugeordnet ist, wäre nicht sinnvoll, den Namen der Aktion aus der URL analysieren erleichtern.

## <a name="attribute-routing-with-httpverb-attributes"></a>Routing mit Attributen Http [Verb]-Attribut

Routing-Attribut kann auch vornehmen, verwenden die `Http[Verb]` Attribute wie `HttpPostAttribute`. Alle diese Attribute können keine routenvorlage akzeptieren. Dieses Beispiel zeigt zwei Aktionen, die die gleichen routenvorlage entsprechen:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Für einen URL-Pfad, z. B. `/products` der `ProductsApi.ListProducts` Aktion wird ausgeführt werden, wenn das HTTP-Verb `GET` und `ProductsApi.CreateProduct` wird ausgeführt werden, wenn das HTTP-Verb `POST`. Zuerst routing-Attribut entspricht den URL für den Satz von routenvorlagen durch routenattributen definiert. Nachdem Sie eine routenvorlage für die übereinstimmt, `IActionConstraint` Einschränkungen werden angewendet, um zu bestimmen, welche Aktionen ausgeführt werden können.

> [!TIP]
> Wenn Sie eine REST-API zu erstellen, ist selten, dass Sie verwenden möchten `[Route(...)]` einer Aktionsmethode aufwiesen. Es ist besser, verwenden Sie die spezifischere `Http*Verb*Attributes` präzise dazu, was Ihre API unterstützt werden. Zu wissen, welche bestimmten logischen Operationen Pfade und HTTP-Verben zugeordnet, werden Clients von REST-APIs erwartet.

Da eine attributenroute für eine bestimmte Aktion gültig ist, ist es ganz einfach als Teil der Vorlage Routendefinition erforderlichen Parameter. In diesem Beispiel `id` als Teil des URL-Pfads erforderlich ist.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Die `ProductsApi.GetProduct(int)` Aktion wird ausgeführt für einen URL-Pfad, z. B. `/products/3` jedoch nicht für einen URL-Pfad, z. B. `/products`. Finden Sie unter [Routing](../../fundamentals/routing.md) für eine vollständige Beschreibung der routenvorlagen und verwandten Optionen.

## <a name="route-name"></a>Routenname

Der folgende Code definiert eine *Routennamen* von `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Routennamen können verwendet werden, um eine URL basierend auf einer bestimmten Route zu generieren. Routennamen haben keine Auswirkung auf die URL Vergleichsverhalten von routing und dienen nur zum URL-Generierung. Routennamen müssen anwendungsweite eindeutig sein.

> [!NOTE]
> Vergleichen Sie dies mit der konventionellen *Standardroute*, definiert die `id` Parameter als optional (`{id?}`). Diese Fähigkeit, genau APIs geben bietet Vorteile, z. B. schreibbarkeit `/products` und `/products/5` an unterschiedliche Aktionen gesendet werden soll.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Kombinieren von Routen

Um machen routing-Attribut weniger wiederkehrende werden routenattributen auf dem Controller mit routenattribute in die einzelnen Aktionen kombiniert. Routenvorlagen auf dem Controller definiert sind routenvorlagen zu den Aktionen vorangestellt. Platzierung von Route-Attribut auf dem Controller macht **alle** Aktionen im Controller verwenden routing-Attribut.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

In diesem Beispiel wird die URL-Pfad `/products` erfüllen können `ProductsApi.ListProducts`, und die URL-Pfad `/products/5` erfüllen können `ProductsApi.GetProduct(int)`. Beide der genannten Aktionen nur HTTP abgeglichen `GET` , da sie mit ausgestattet sind die `HttpGetAttribute`.

Weiterleiten von Vorlagen, die auf eine Aktion, die mit einem `/` nicht mit routenvorlagen gilt für den Controller kombiniert abrufen. In diesem Beispiel vergleicht eine Sammlung von URL-Pfade ähnelt der *Standardroute*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Sortierung attributenrouten

Im Gegensatz zu herkömmlichen Routen, die in einer definierten Reihenfolge ausgeführt, eine Struktur erstellt, und alle Routen gleichzeitig entspricht routing-Attribut. Dies verhält sich wie – wenn die routeneinträge liefern platziert wurden, zu einer idealen Sortierung; die spezifischsten Routen haben die Möglichkeit, vor der allgemeineren Routen ausführen.

Z. B. eine Route wie `blog/search/{topic}` ist genauer als eine Route wie `blog/{*article}`. Logisch gesehen der `blog/search/{topic}` Route "" zuerst, wird standardmäßig ausgeführt, da dies nur sinnvolle Sortierung ist. Herkömmliche routing verwenden, ist der Entwickler verantwortlich für die Platzierung von Routen in der gewünschten Reihenfolge.

Attributenrouten können konfigurieren, dass eine Bestellung mit der `Order` -Eigenschaft aller Attribute Route Framework bereitgestellt. Routen werden verarbeitet, entsprechend eine aufsteigende Sortierung der `Order` Eigenschaft. Die Standardreihenfolge ist `0`. Festlegen einer Route mit `Order = -1` ausgeführt wird, bevor die Routen, die eine Bestellung nicht festlegen. Festlegen einer Route mit `Order = 1` nach Route Standardreihenfolge ausgeführt wird.

> [!TIP]
> Vermeiden Sie je nach `Order`. Wenn die URL-Space explizite Werte ordnungsgemäß weiterleiten erforderlich ist, ist es wahrscheinlich für Clients sowie verwirrend. Routing-Attribut wird im Allgemeinen die richtige Route mit übereinstimmenden URL ausgewählt. Wenn die Standardreihenfolge zum URL-Generierung nicht funktioniert, verwenden Routennamen als eine Außerkraftsetzung in der Regel einfacher als das Anwenden der `Order` Eigenschaft.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Ersetzung in routenvorlagen Token ([Controller], [Aktion] [Bereich])

Der Einfachheit halber attributenrouten unterstützen *token Ersatz* indem ein Token in eckige Klammern einschließen (`[`, `]`). Die Token `[action]`, `[area]`, und `[controller]` ersetzt werden mit den Werten der Aktionsname, der Bereichsname und des Controllernamens von der Aktion, in dem die Route definiert ist. In diesem Beispiel können die Aktionen URL-Pfade entsprechen, wie in den Kommentaren beschrieben:

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Tokenersetzung tritt auf, als der letzte Schritt der Erstellung der attributenrouten. Im obigen Beispiel verhält sich der folgende Code entspricht:

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Attributenrouten können auch mit Vererbung kombiniert werden. Dies ist besonders effektiv mit tokenersetzung kombiniert.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPost("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Tokenersetzung gilt auch für Routennamen durch attributenrouten definiert. `[Route("[controller]/[action]", Name="[controller]_[action]")]`generiert einen eindeutigen Routennamen für jede Aktion.

Das literal tokenersetzung Trennzeichen entsprechend `[` oder `]`, es durch die Wiederholung des Zeichens mit Escapezeichen versehen (`[[` oder `]]`).

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Mehrere Routen

Routing unterstützt mehrere Routen, die die gleiche Aktion erreichen definieren des Attributs. Die häufigste Verwendung dieses imitieren, die das Verhalten entspricht der *konventionellen Standardroute* wie im folgenden Beispiel gezeigt:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Einfügen von mehreren routenattributen auf dem Controller, bedeutet, dass es sich bei jeweils mit jedem der routenattribute in den Aktionsmethoden kombiniert werden.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Bei mehreren routenattributen (implementiert, `IActionConstraint`) werden auf eine Aktion platziert, und klicken Sie dann jede Aktion-Einschränkung mit der routenvorlage aus dem Attribut kombiniert, die es definiert.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Während mehrere Routen zu Aktionen mit leistungsstarke erscheinen kann, ist es besser, URL-Bereich für die Anwendung einfach und klar definierten beibehalten. Verwenden Sie mehrere Routen zu Aktionen nur, z. B. zur Unterstützung von vorhandenen Clients erforderlich sind.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Angeben Optionaler Routenparameter Attribut, Standardwerte und Einschränkungen

Attributenrouten unterstützen die gleichen Inline-Syntax als herkömmliche Routen optionale Parameter, Standardwerte und Einschränkungen angeben.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Finden Sie unter [Route Vorlagenverweis](../../fundamentals/routing.md#route-template-reference) für eine ausführliche Beschreibung der Syntax der Route-Vorlage.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Benutzerdefinierte routenattributen verwenden`IRouteTemplateProvider`

Alle routenattribute im Framework bereitgestellt ( `[Route(...)]`, `[HttpGet(...)]` usw.) implementieren die `IRouteTemplateProvider` Schnittstelle. MVC sucht nach Attribute auf Controllerklassen und Aktionsmethoden auf, wenn die Anwendung gestartet und verwendet diejenigen, die implementieren `IRouteTemplateProvider` den anfänglichen Satz von Routen zu erstellen.

Sie können implementieren `IRouteTemplateProvider` Definieren eigener routenattribute. Jede `IRouteTemplateProvider` sortieren können Sie eine einzelne Route mit einer benutzerdefinierten routenvorlage definieren, und nennen Sie:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Automatisch das Attribut aus dem obigen Beispiel legt die `Template` auf `"api/[controller]"` Wenn `[MyApiController]` angewendet wird.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Anpassen von attributenrouten mithilfe Anwendungsmodell

Die *Anwendungsmodell* ist ein Objektmodell erstellt beim Starten aller Metadaten von MVC, weiterleiten und Aktionen ausführen. Die *Anwendungsmodell* enthält alle Daten vom routenattributen erfasst (über `IRouteTemplateProvider`). Sie können schreiben *Konventionen* so ändern Sie das Anwendungsmodell zum Startzeitpunkt anpassen, wie routing verhält. Dieser Abschnitt zeigt ein einfaches Beispiel für das routing mit Anwendungsmodell anpassen.

[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Gemischte routing:-Attribut routing Vs konventionellen routing

MVC-Anwendungen können die Verwendung von herkömmlichen routing und routing-Attribut kombinieren. Es ist in der Regel verwenden herkömmliche Routen für Domänencontroller, die für den HTML-Seiten für Browser, und das Attribut routing für Domänencontroller, die für den REST-APIs.

Aktionen werden konventionell entweder weitergeleitet oder Attribut weitergeleitet. Platzieren eine Route auf dem Controller oder die Aktion erleichtert Attribut weitergeleitet. Aktionen, die attributenrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden. **Alle** routenattribut auf dem Controller macht alle Aktionen im Controller Attribut weitergeleitet.

> [!NOTE]
> Welche zwei Arten von routing Systeme unterscheidet, ist der Prozess, der angewendet, nachdem eine routenvorlage für die mit einer URL übereinstimmt. In herkömmlichen routing dienen die Routenwerte aus der Übereinstimmung mit einer Nachschlagetabelle für alle Aktionen für herkömmliche gerouteten die Aktion und den Controller aus. Klicken Sie in der routing-Attribut, jede Vorlage ist bereits eine Aktion zugeordnet und keine weiteren Suche erforderlich ist.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL-Generierung

MVC-Anwendungen können routing URL Generation Funktionen verwenden, um URL-Links, um Aktionen zu generieren. Generieren von URLs eliminiert hartcodierten URLs, ausführenden Code robuster und verwaltbar. Dieser Abschnitt konzentriert sich auf die URL Generation Features von MVC und befasst sich nur auf die Grundlagen der Funktionsweise der URL-Generierung. Finden Sie unter [Routing](../../fundamentals/routing.md) für eine ausführliche Beschreibung der URL-Generierung.

Die `IUrlHelper` Schnittstelle ist die zugrunde liegenden Teil einer Infrastruktur zwischen MVC und routing für URL-Generierung. Finden Sie eine Instanz von `IUrlHelper` über die `Url` Eigenschaft im Controller, Ansichten und Anzeigen von Komponenten.

In diesem Beispiel wird die `IUrlHelper` Schnittstelle wird verwendet, über die `Controller.Url` Eigenschaft zum Generieren einer URL an eine andere Aktion.

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Wenn die Anwendung Parallelität mit konventionellen standardmäßig weiterzuleiten, den Wert der `url` Variable wird die Zeichenfolge für den URL-Pfad sein `/UrlGeneration/Destination`. Diese URL-Pfad wird mit den Werten, die zum Übergeben von durch die Kombination aus der aktuellen Anforderung (ambient-Werte), die Routenwerte routing erstellt `Url.Action` und ersetzen diese Werte in der routenvorlage:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Jede Routenparameter in der routenvorlage hat sein Wert durch die passenden Namen mit den Werten und die ambient-Werte ersetzt. Ein Routenparameter, der einen Wert aufweist können einen Standardwert, wenn er verfügt über einen oder übersprungen werden, wenn er optional ist (wie im Fall von `id` in diesem Beispiel). URL-Generierung schlägt fehl, wenn alle erforderlichen Routenparameter einen entsprechenden Wert besitzt. Sollte die URL-Generierung für eine Route ein Fehler auftritt, wird die nächste Route versucht, bis alle Routen versucht wurden wurden oder eine Übereinstimmung gefunden wird.

Das Beispiel `Url.Action` oben geht davon aus konventionellen routing, aber URL Generation funktioniert auf ähnliche Weise mit routing-Attribut, obwohl die Konzepte unterscheiden. Mit herkömmlichen routing dienen zum Erweitern einer Vorlage und die Routenwerte für die Routenwerte `controller` und `action` in der Regel angezeigt werden in dieser Vorlage – dies funktioniert, da die URLs von routing gefunden wird, entsprechen einer *Konvention*. In der routing-Attribut, das Routenwerte für `controller` und `action` dürfen nicht angezeigt werden in der Vorlage – sie werden stattdessen verwendet, um nachschlagen, welche Vorlage verwendet.

In diesem Beispiel wird das routing-Attribut verwendet:

[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC erstellt eine Nachschlagetabelle Verwaltungsaktionen Attribut weitergeleitet und entspricht der `controller` und `action` Werte auf die routenvorlage für URL-Generierung verwendet. Im obigen Beispiel `custom/url/to/destination` generiert wird.

### <a name="generating-urls-by-action-name"></a>Generieren von URLs durch den Namen der Aktion

`Url.Action` (`IUrlHelper` . `Action`) und alle zugehörigen Überladungen alle auf diesem Konzept basieren, dass Sie angeben, was Sie durch Angabe eines Controllernamen und Aktionsnamen die Verknüpfung erstellen möchten.

> [!NOTE]
> Bei Verwendung `Url.Action`, aktuelle Route Werte für `controller` und `action` sind für Sie – der Wert der angegebenen `controller` und `action` sind Teil der beiden *ambient Werte* **und** *Werte*. Die Methode `Url.Action`, verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, die an die aktuelle Aktion weiterleitet.

Versuche, verwenden Sie die Werte in der ambient-Werte Informationen ausfüllen, die Sie bereitgestellt haben, während der Generierung eine URL-Routing. Verwenden eine Route wie `{a}/{b}/{c}/{d}` und ambient Werte `{ a = Alice, b = Bob, c = Carol, d = David }`, routing verfügt über genügend Informationen zum Generieren einer URL ohne zusätzliche Werte – da alle weiterleiten Parameter einen Wert aufweisen. Wenn Sie den Wert hinzugefügt `{ d = Donovan }`, den Wert `{ d = David }` würde ignoriert werden, und die generierte URL-Pfad wäre `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> URL-Pfade sind hierarchisch. Im obigen Beispiel wird den Wert hinzufügen `{ c = Cheryl }`, beide Werte `{ c = Carol, d = David }` würde ignoriert werden. In diesem Fall haben wir mehr einen Wert für `d` und URL-Generierung schlägt fehl. Sie müssen den gewünschten Wert angeben `c` und `d`.  Sie erwarten wahrscheinlich, dieses Problem mit der Standardroute erreicht werden soll (`{controller}/{action}/{id?}`)- jedoch selten tritt dieses Verhalten in der Praxis als `Url.Action` immer explizit anzugeben, wird eine `controller` und `action` Wert.

Mehr Überladungen der `Url.Action` akzeptieren auch ein zusätzliches *Routenwerte* Werte außer für Routenparameter zu verwendendes Objekt `controller` und `action`. Sie werden am häufigsten finden Sie in diesem mit verwendet `id` wie `Url.Action("Buy", "Products", new { id = 17 })`. Gemäß der Konvention der *Routenwerte* Objekt stellt normalerweise ein Objekt des anonymen Typs, aber es kann auch ein `IDictionary<>` oder eine *einfaches altes .NET Objekt*. Alle zusätzlichen Routenwerte, die Routenparameter nicht übereinstimmen, werden in der Abfragezeichenfolge platziert.

[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> Um eine absolute URL zu erstellen, verwenden Sie eine Überladung, akzeptiert eine `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generieren von URLs von route

Der obige Code veranschaulicht das Generieren einer URL durch die Übergabe der Controller- und Name. `IUrlHelper`bietet außerdem die `Url.RouteUrl` -Methodenfamilie. Diese Methoden ähneln `Url.Action`, aber nicht können, kopieren Sie die aktuellen Werte der `action` und `controller` , die Routenwerte. Die häufigste Verwendung ist die Angabe von einem Routennamen an eine Route zu verwenden, um die URL in der Regel generieren *ohne* Angabe eines Namens Controller bzw. die Aktionsmethode.

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generieren von URLs in HTML

`IHtmlHelper`Stellt die `HtmlHelper` Methoden `Html.BeginForm` und `Html.ActionLink` generieren `<form>` und `<a>` Elemente bzw. Verwenden Sie diese Methoden die `Url.Action` Methode, um eine URL zu generieren und ähnliche Argumente akzeptieren. Die `Url.RouteUrl` Begleitgeräte für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink` die ähnliche Funktionen aufweisen.

TagHelpers Generieren von URLs mit dem `form` taghelpers und die `<a>` taghelpers. Verwenden Sie diese beiden `IUrlHelper` für ihre Implementierung. Finden Sie unter [arbeiten mit Formularen](../views/working-with-forms.md) für Weitere Informationen.

In Ansichten die `IUrlHelper` kann über die `Url` -Eigenschaft für alle Ad-hoc-URL-Generierung, die nicht von den oben genannten abgedeckt.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generieren von URLS in Aktionsergebnisse

Die obigen Beispiele haben gezeigt, mithilfe von `IUrlHelper` in einem Controller, während die häufigste Verwendung in einem Controller zum Generieren einer URL im Rahmen des ein Aktionsergebnis wird.

Die `ControllerBase` und `Controller` Basisklassen bieten Hilfsmethoden für Aktionsergebnisse, die auf eine andere Aktion verweisen. Eine typische Verwendung besteht darin, leiten nach der Benutzereingaben akzeptieren.

```csharp
public Task<IActionResult> Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
}
```

Die Aktion Ergebnisse Factorymethoden folgen einem ähnlichen Muster auf die Methoden auf `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Sonderfall für dedizierte konventionellen Routen

Herkömmliche routing können Sie eine spezielle Art von Route-Definition bezeichnet eine *dedizierten konventionellen Route*. Im folgenden Beispiel wird die Route mit dem Namen `blog` ist eine dedizierte konventionelle Route.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Verwenden diese Route-Definitionen `Url.Action("Index", "Home")` generiert den URL-Pfad `/` mit der `default` Route, aber nicht, warum? Können Sie erraten, die Routenwerte `{ controller = Home, action = Index }` wäre ausreichend, um eine URL generiert die Verwendung `blog`, und das Ergebnis wäre `/blog?action=Index&controller=Home`.

Dedizierte konventionelle Routen abhängig ist, ein spezielles Verhalten von Standardwerten, die einen entsprechenden Routenparameter besitzen, die verhindert, dass die Route wird "zu gieriger" mit URL-Generierung. In diesem Fall die Standardwerte sind `{ controller = Blog, action = Article }`, und weder `controller` noch `action` wird als ein Routenparameter. Wenn routing URL-Generierung ausführt, müssen die angegebenen Werte die Standardwerte übereinstimmen. Mithilfe von URL-Generierung `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` stimmen nicht überein. `{ controller = Blog, action = Article }`. Routing dann ausgewichen, um zu versuchen `default`, die erfolgreich ausgeführt wird.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Bereiche

[Bereiche](areas.md) eine MVC-Funktion, die zum Organisieren von verwandten Funktionen in einer Gruppe als eine separate routing-Namespace (für Controlleraktionen) und Ordnerstruktur (Views) verwendet werden. Ermöglicht es einer Anwendung mehrere Domänencontroller mit dem gleichen Namen – solange sie verschiedene sind mithilfe von Bereichen *Bereiche*. Mithilfe von Bereichen erstellt eine Hierarchie für die Weiterleitung und Hinzufügen von einem anderen Routenparameter, `area` auf `controller` und `action`. In diesem Abschnitt wird erläutert, wie routing Bereiche - interagiert, finden Sie unter [Bereiche](areas.md) Weitere Informationen zur Verwendung von Bereichen mit Ansichten.

Das folgende Beispiel konfiguriert die MVC, um die Standardroute konventionellen verwenden und ein *Bereich Route* für einen Bereich mit dem Namen `Blog`:

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

Beim Abgleich von URL-Pfad wie `/Manage/Users/AddUser`, erzeugt die erste Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`. Die `area` routenwert wird erzeugt, indem ein Standardwert für `area`, in der Tat die Route erstellt, indem `MapAreaRoute` entspricht der folgenden:

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`erstellt eine Route mit einem Standardwert und die Einschränkung für `area` verwenden den angegebenen Bereichsnamen in diesem Fall `Blog`. Der Standardwert wird sichergestellt, dass die Route immer erzeugt `{ area = Blog, ... }`, die Einschränkung muss den Wert `{ area = Blog, ... }` für URL-Generierung.

> [!TIP]
> Herkömmliche routing ist die Reihenfolge wichtig. Im Allgemeinen sollten Routen mit Bereichen weiter oben in der Routingtabelle platziert werden, wie sie spezifischere als Routen, ohne einen Bereich sind.

Verwenden das oben genannte Beispiel an, würde die Routenwerte die folgende Aktion übereinstimmen:

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Die `AreaAttribute` ist, was einen Controller kennzeichnet im Rahmen eines Bereichs, sagen wir, dass in diesem Controller ist der `Blog` Bereich. Domänencontroller ohne eine `[Area]` Attribut sind keine Mitglieder der jeder Bereich und werden **nicht** übereinstimmen, wenn die `area` Route wird von routing bereitgestellt. Im folgenden Beispiel kann nur der erste Controller aufgeführten entsprechen, die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Der Namespace der jedem Controller ist aus Gründen der Vollständigkeit im folgenden dargestellt: Andernfalls würde die Controller eine Benennung von in Konflikt stehen und ein Compilerfehler generiert haben. Klasse Namespaces haben keine Auswirkungen auf MVCs-routing.

Die ersten beiden Controller sind Elemente der Bereiche, und nur überein, wenn von ihren jeweiligen Bereichsname bereitgestellt wird der `area` Wert weiterzuleiten. Der dritte Controller ist nicht Mitglied eines beliebigen Bereich und die einzige Übereinstimmung kann, wenn kein Wert für `area` von routing bereitgestellt.

> [!NOTE]
> Im Hinblick auf Übereinstimmung *kein Wert*, Abwesenheit der `area` Wert ist gleich als wäre der Wert für `area` waren Null oder eine leere Zeichenfolge.

Bei der Ausführung einer Aktion innerhalb eines Bereichs-Wert die Route für `area` als verfügbar ein *ambient Wert* für das routing für die Verwendung für URL-Generierung. Dies bedeutet, dass standardmäßig Bereiche agieren *persistente* für URL-Generierung, wie im folgenden Beispiel gezeigt.

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Grundlegendes zu IActionConstraint

> [!NOTE]
> Dieser Abschnitt ist eine tief greifende Framework Mechanismen und wie eine Aktion zum Ausführen von MVC auswählt. Eine typische Anwendung erforderlich keinen benutzerdefinierten.`IActionConstraint`

Sie haben wahrscheinlich bereits verwendet `IActionConstraint` , auch wenn Sie nicht mit der Oberfläche vertraut sind. Die `[HttpGet]` Attribut und ähnliche `[Http-VERB]` Attribute implementieren `IActionConstraint` um die Ausführung einer Aktionsmethode einzuschränken.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Vorausgesetzt, die herkömmliche Standardroute des URL-Pfads `/Products/Edit` wären die Werte `{ controller = Products, action = Edit }`, entsprechen die **beide** der hier gezeigten Aktionen. In `IActionConstraint` Terminologie, die wir sagen würden, dass beide der genannten Aktionen Kandidaten - berücksichtigt werden, da beide die Routendaten übereinstimmt.

Wenn die `HttpGetAttribute` ausgeführt wird, es wird angegeben, die *Edit()* wird eine Übereinstimmung für *abrufen* und keine Übereinstimmung für alle anderen HTTP-Verb. Die `Edit(...)` Aktion verfügt nicht über alle definierten Einschränkungen und daher HTTP-Verb entspricht. Vorausgesetzt, sodass eine `POST` – nur `Edit(...)` übereinstimmt. Aber für eine `GET` beide Aktionen können dennoch übereinstimmen - jedoch eine Aktion mit einer `IActionConstraint` gilt immer *besser* als eine Aktion ohne. Daher da `Edit()` hat `[HttpGet]` sie spezifischere gilt und wird ausgewählt werden, wenn beide Aktionen verglichen werden können.

Im Prinzip `IActionConstraint` ist eine Form der *überladen*, aber statt Überladen von Methoden mit demselben Namen, ist es überladen zwischen Aktionen, die die gleiche URL entsprechen. Routing-Attribut verwendet auch `IActionConstraint` und kann dazu führen, Aktionen, die von verschiedenen Controllern beide vermerkt Kandidaten.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementieren von IActionConstraint

Die einfachste Methode zum Implementieren einer `IActionConstraint` zum Erstellen einer Klasse abgeleitet ist `System.Attribute` und platzieren Sie es auf Ihre Aktionen und Controllern aus. MVC erkennt automatisch `IActionConstraint` , die als Attribute angewendet werden. Sie verwenden das Anwendungsmodell, um Einschränkungen anzuwenden, und dies ist wahrscheinlich die flexibelste Herangehensweise, da Sie zu Metaprogram hinauszögern wie sie angewendet werden.

Im folgenden Beispiel wird eine Einschränkung wählt eine Aktion basierend auf einer *Landeskennzahl* von den Routendaten. Die [vollständige Sample auf GitHub](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Sie sind verantwortlich für die Implementierung der `Accept` -Methode und eine "Order" für die Einschränkung auszuführende auswählen. In diesem Fall die `Accept` -Methode zurückkehrt `true` zu kennzeichnen, die Aktion ist eine Übereinstimmung bei der `country` route entspricht. Dies unterscheidet sich von einem `RouteValueAttribute` dahingehend, dass fallback auf eine Aktion nicht mit Attributen versehen können. Das Beispiel zeigt, dass, wenn Sie definieren eine `en-US` Aktion und dann einen Ländercode wie `fr-FR` ausweichen auf einen generischeren Controller, der keine `[CountrySpecific(...)]` angewendet.

Die `Order` Eigenschaft entscheidet, in welchen *Phase* die Einschränkung gehört. Aktionseinschränkungen führen Sie in Gruppen auf Grundlage der `Order`. Z. B. alle das Framework bereitgestellt, verwenden Sie HTTP-Method-Attribute den gleichen `Order` Wert, sodass sie in der gleichen Stufe ausgeführt werden. Sie können beliebig viele Stufen Sie die gewünschten Richtlinien implementieren müssen.

> [!TIP]
> Um zu entscheiden, auf einen Wert für `Order` überlegen, ob die Einschränkung vor HTTP-Methoden angewendet werden soll. Niedrigere Zahlen werden zuerst ausgeführt.
