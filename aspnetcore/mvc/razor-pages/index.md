---
title: "Einführung in Razor-Pages in ASP.NET Core"
author: Rick-Anderson
description: "ASP.NET Core-Lernprogramm für Razor-Pages. Enthält MVC Core, ASP.NET Core 2.x, Visual Studio 2017 sowie eine Einführung in die Webentwicklung. Diese Dokumentation stellt eine Übersicht über die Verwendung von Razor-Pages in ASP.NET Core bereit, um die Entwicklung von auf Seiten bezogene Szenarios zu vereinfachen."
manager: wpickett
ms.author: riande
ms.date: 09/12/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: mvc/razor-pages/index
ms.openlocfilehash: c16959189448337b12dc4902300516b25c200eee
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Einführung in Razor-Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)

Razor-Pages ist ein neues Feature von ASP.NET Core MVC, mit dem codierungsseitige Szenarios einfacher und produktiver werden.

Weitere Informationen zu einem Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Getting started with ASP.NET Core MVC (Erste Schritte mit ASP.NET Core MVC)](xref:tutorials/first-mvc-app/start-mvc).

Dieses Dokument bietet eine Einführung in Razor-Pages. Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial. Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Pages](xref:tutorials/razor-pages/razor-pages-start).

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a>Anforderungen von ASP.NET Core 2.0

Installieren Sie [.NET Core](https://www.microsoft.com/net/core) 2.0.0 oder höher.

Wenn Sie Visual Studio verwenden, installieren Sie [Visual Studio](https://www.visualstudio.com/vs/) 2017 Version 15.3 oder höher mit folgenden Workloads:

* **ASP.NET und Webentwicklung**
* **Plattformübergreifende .NET Core-Entwicklung**

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a>Erstellen eines Projekts mit Razor-Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Weitere Informationen zum Erstellen eines Projekts mit Razor-Pages mithilfe von Visual Studio finden Sie unter [Getting started with Razor Pages (Erste Schritte mit Razor-Pages)](xref:tutorials/razor-pages/razor-pages-start).

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Führen Sie `dotnet new razor` über die Befehlszeile aus.

Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

Führen Sie `dotnet new razor` über die Befehlszeile aus.

#   <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli) 

Führen Sie `dotnet new razor` über die Befehlszeile aus.

---

## <a name="razor-pages"></a>Razor-Pages

Razor-Pages ist in *Startup.cs* aktiviert:

[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Der vorherige Code ähnelt stark einer Razor-Umgebungsdatei. Der Unterschied besteht in der `@page`-Anweisung. `@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden. `@page` muss die erste Razor-Anweisung auf einer Seite sein. `@page` wirkt sich auf das Verhalten aller anderen Razor-Konstrukte aus.

Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt. Die Datei *Pages/Index2.cshtml*:

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Das Seitenmodell *Pages/Index2.cshtml.cs*:

[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor-Pages, nur dass außerdem *cs* angefügt wird. Die vorherige Datei mit Razor-Pages lautet beispielsweise *Pages/Index2.cshtml*. Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.

Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt. Die folgende Tabelle zeigt einen Pfad zu Razor-Pages und die entsprechende URL:

| Dateiname und Pfad               | Entsprechende URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` oder `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` oder `/Store/Index` |

Notizen:

* Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor-Pages.
* Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.

## <a name="writing-a-basic-form"></a>Schreiben eines einfachen Formulars

Features von Razor-Pages erstellen allgemeine Muster, die einfach mit Webbrowsern verwendet werden können. Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor-Pages definiert wurden. Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:

Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) initialisiert.

[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Das Datenmodell:

[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]

Der db-Kontext:

[!code-cs[main](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Die Umgebungsdatei *Pages/Create.cshtml*:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Das Seitenmodell *Pages/Create.cshtml.cs*:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Die `PageModel`-Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.

Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden. Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten. Durch diese Trennung können Sie Seitenabhängigkeiten durch [Abhängigkeiteneinschleusung](xref:fundamentals/dependency-injection) verwalten und [Komponententests](xref:testing/razor-pages-testing) für die Seiten durchführen.

Die Seite hat eine `OnPostAsync`-*Handlermethode*, die auf `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet). Sie können Handlermethoden für alle HTTP-Verben hinzufügen. Die am häufigsten verwendeten Handler sind:

* `OnGet`, um den für eine Seite erforderlichen Status zu initialisieren. [OnGet](#OnGet)-Beispiel
* `OnPost`, um Formularübermittlungen zu behandeln

Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet. Der `OnPostAsync`-Code im vorhergehenden Beispiel ähnelt dem, was Sie normalerweise in einem Controller schreiben würden. Der vorhergehende Code ist typisch für Razor-Pages. Die meisten primitiven MVC-Typen wie die [Modellbindung](xref:mvc/models/model-binding), die [Validierung](xref:mvc/models/validation) und Aktionsergebnisse werden freigegeben.  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

Die vorherige `OnPostAsync`-Methode:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Der grundlegende Ablauf von `OnPostAsync`:

Prüfen auf Validierungsfehler

*  Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.
*  Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an. Die clientseitige Validierung ist identisch mit herkömmlichen ASP.NET Core MVC-Anwendungen, denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.

Wenn die Daten erfolgreich eingegeben wurden, ruft die `OnPostAsync`-Handlermethode die `RedirectToPage`-Hilfsmethode auf, um eine Instanz von `RedirectToPageResult` zurückzugeben. `RedirectToPage` ist ein neues Aktionsergebnis und ähnelt `RedirectToAction` oder `RedirectToRoute`, ist aber für Seiten angepasst. Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter. Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).

Wenn das übermittelte Formular Validierungsfehler enthält (die an den Server übergeben wurden), ruft die `OnPostAsync`-Handlermethode die `Page`-Hilfsmethode auf. `Page` gibt eine Instanz von `PageResult` zurück. Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben. `PageResult` ist der standardmäßige <!-- Review  -->-Rückgabetyp für eine Handlermethode. Eine Handlermethode, die `void` zurückgibt, rendert die Seite.

Die Eigenschaft `Customer` verwendet das `[BindProperty]`-Attribut, um die Modellbindung zu aktivieren.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor-Pages binden Eigenschaften standardmäßig nur an Nicht-GET-Verben. Durch die Bindung an Eigenschaften können Sie den Umfang von Codes reduzieren, den Sie schreiben müssen. Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name" />`) zu rendern und die Eingabe zu akzeptieren.

Die Startseite (*Index.cshtml*):

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Die CodeBehind-Datei *Index.cshtml.cs*:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Die Datei *Index.cshtml* enthält das folgende Markup, um einen Bearbeitungslink für jeden Kontakt zu erstellen:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) verwendet das Attribut `asp-route-{value}`, um einen Link zur Bearbeitungsseite zu generieren. Der Link enthält die Routendaten mit der Kontakt-ID. Beispielsweise `http://localhost:5000/Edit/1`.

Die Datei *Pages/Edit.cshtml*:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

Die erste Zeile enthält die `@page "{id:int}"`-Anweisung. Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten. Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.

Die Datei *Pages/Edit.cshtml.cs*:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Die Datei *index.cshtml* enthält auch Markup zum Erstellen der Schaltfläche „Löschen“ für jeden benutzerdefinierten Kontakt:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Wenn die „Löschen“-Schaltfläche in HTML gerendert wird, enthält ihr `formaction`-Element Parameter für Folgendes:

* Die benutzerdefinierte Kontakt-ID, die vom `asp-route-id`-Attribut angegeben wird
* Der `handler`, der vom `asp-page-handler`-Attribut angegeben wird

Hier sehen Sie ein Beispiel für eine gerenderte „Löschen“-Schaltfläche mit einer benutzerdefinierten Kontakt-ID von `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet. Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters entsprechend des Schemas `OnPost[handler]Async` ausgewählt.

Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten. Wenn der `asp-page-handler` auf einen anderen Wert festgelegt wird, wie z.B. `remove`, wird eine Seitenhandlermethode mit dem Namen `OnPostRemoveAsync` ausgewählt.

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Die `OnPostDeleteAsync`-Methode:

* Akzeptiert die `id` der Abfragezeichenfolge.
* Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.
* Wenn der Kundenkontakt gefunden wird, wird er aus der Liste der Kundenkontakte entfernt. Die Datenbank wurde aktualisiert.
* Ruft `RedirectToPage` auf, um die Stammindexseite (`/Index`) umzuleiten.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF und Razor-Pages

Sie müssen keinen Code für die [Antifälschungsvalidierung](xref:security/anti-request-forgery) schreiben. Die Generierung und Validierung von Antifälschungstoken ist automatisch in Razor-Pages enthalten.

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor-Pages

Razor-Pages funktionieren mit allen Funktionen des Razor-Anzeigemoduls. Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml*, *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.

Lassen Sie uns diese Seite mithilfe einiger dieser Funktionen entwirren.

Fügen Sie einer *Pages/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

Das [Layout](xref:mvc/views/layout):

* Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.
* Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.

Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).

Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

**Hinweis:** Das Layout befindet sich im Ordner *Pages* (Seiten). Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite. Ein Layout im Ordner *Seiten* kann aus jeder Razor-Page unter dem Ordner *Pages* verwendet werden.

Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren. *Views/Shared* ist ein MVC-Ansichtsmuster. Razor-Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.

Die Ansichtensuche in einer Razor-Page enthält den Ordner *Pages*. Die Layouts, Vorlagen und Teilansichten, die Sie mit MVC-Controllern und herkömmlichen Razor-Ansichten verwenden, *funktionieren einfach so*.

Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` wird weiter unten im Tutorial erläutert. Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.

<a name="namespace"></a>

Wenn die `@namespace`-Anweisung explizit auf eine Seite angewendet wird:

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Die Anweisung legt den Namespace für die Seite fest. Die `@model`-Anweisung muss den Namespace nicht enthalten.

Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert. Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.

Die CodeBehind-Datei *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Der generierte Namespace für die Razor-Page *Pages/Customers/Edit.cshtml* ist identisch mit der CodeBehind-Datei. Die `@namespace`-Anweisung wurde entworfen, damit die einem Projekt hinzugefügten C#-Klassen und mit Seiten generierter Code *einfach so funktionieren*, ohne dass eine `@using`-Anweisung für die CodeBehind-Datei hinzugefügt werden muss.

**Hinweis:** `@namespace` funktioniert auch mit konventionellen Razor-Ansichten.

Die ursprüngliche Umgebungsdatei *Pages/Create.cshtml*:

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Die aktualisierte Umgebungsdatei *Pages/Create.cshtml*:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

Die [Razor-Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>URL-Generierung für Seiten

Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Die App hat die folgende Datei/Ordner-Struktur:

* */Pages*

  * *Index.cshtml*
  * */Customer*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* leiten bei Erfolg an *Pages/Index.cshtml* weiter. Die Zeichenfolge `/Index` ist Teil des URI, der auf die vorhergehende Seite zugreifen soll. Die Zeichenfolge `/Index` kann für das Generieren von URIs für die Seite *Pages/Index.cshtml* verwendet werden. Zum Beispiel:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`). Die vorherigen Beispiele für die URL-Generierung sind viel umfangreicher an Features als eine einfache hartcodierte URL. Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.

Die URL-Generierung für Seiten unterstützt relative Namen. In der folgenden Tabelle wird dargestellt, welche Indexseite für verschiedene `RedirectToPage`-Parameter aus *Pages/Customers/Create.cshtml* ausgewählt wird:

| RedirectToPage(x)| Seite |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*. Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur. Wenn Sie relative Namen verwenden, um Seiten in einem Ordner zu verknüpfen, können Sie diesen Ordner umbenennen. Alle Links funktionieren weiterhin, da sie nicht den Namen des Ordners enthalten.

## <a name="tempdata"></a>TempData

ASP.NET Core macht die Eigenschaft [TempData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) auf einem [Controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller) verfügbar. Diese Eigenschaft speichert Daten, bis sie gelesen wurden. Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen. `TempData` eignet sich für die Weiterleitung, wenn Daten für mehr als eine Anforderung benötigt werden.

Das `[TempData]`-Attribut ist neu in ASP.NET Core 2.0 und wird auf Domänencontrollern und Seiten unterstützt.

Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.

```cs
[TempData]
public string Message { get; set; }
```

Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#temp).

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a>Mehrere Handler pro Seite

Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln. Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`. `asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden. `asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.

Das Seitenmodell:

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Der vorherige Code verwendet *benannte Handlermethoden*. Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt. Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden. Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `http://localhost:5000/Customers/CreateFATH?handler=JoinList`. Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.

## <a name="customizing-routing"></a>Anpassen des Routings

Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, können Sie die Route ändern, damit der Handlername im Pfadteil der URL eingefügt wird. Sie können die Route anpassen, indem Sie eine Routenvorlage in doppelten Anführungszeichen nach der `@page`-Anweisung hinzufügen.

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Die vorherige Route platziert den Handlernamen im URL-Pfad statt in die Abfragezeichenfolge. Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.

Sie können einer Seitenroute mit `@page` weitere Segmente und Parameter hinzufügen. Alles, was hier angegeben wird, wird der Standardroute der Seite **angefügt**. Die Verwendung eines absoluten oder virtuellen Pfads, um die Seitenroute (z.B. `"~/Some/Other/Path"`) zu ändern, wird nicht unterstützt.

## <a name="configuration-and-settings"></a>Konfiguration und Einstellungen

Um die erweiterten Optionen zu konfigurieren, verwenden Sie die Erweiterungsmethode `AddRazorPagesOptions` auf dem MVC-Generator:

[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Derzeit können Sie `RazorPagesOptions` verwenden, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen. Auf diese Weise wird in Zukunft eine höhere Erweiterbarkeit erreicht.

Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor view compilation and precompilation in ASP.NET Core (Razor-Ansichtenkompilierung und Vorkompilierung in ASP.NET)](xref:mvc/views/view-compilation).

[Laden Sie Beispielcode herunter, oder zeigen Sie ihn an](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).

Lesen Sie auch den Artikel [Getting started with Razor Pages in ASP.NET Core (Erste Schritte mit Razor-Pages in ASP.NET Core)](xref:tutorials/razor-pages/razor-pages-start), der auf diese Einführung aufbaut.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Festlegen des Inhaltsstammverzeichnisses für Razor-Pages

Standardmäßig lautet das Stammverzeichnis für Razor-Pages */Pages*. Fügen Sie [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass sich Ihre Razor-Dateien im Inhaltsstammverzeichnis ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) der App befinden:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor-Pages

Fügen Sie [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass Ihre Razor-Pages sich in einem benutzerdefinierten Stammverzeichnis in der App befinden (geben Sie einen relativen Pfad an):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="see-also"></a>Siehe auch

* [Getting started with Razor Pages (Erste Schritte mit Razor-Pages)](xref:tutorials/razor-pages/razor-pages-start)
* [Autorisierungskonventionen für Razor-Pages](xref:security/authorization/razor-pages-authorization)
* [Benutzerdefinierte Routen- und Seitenmodellanbieter für Razor-Pages](xref:mvc/razor-pages/razor-pages-convention-features)
* [Unit- und Integrationstests für Razor-Pages](xref:testing/razor-pages-testing)
