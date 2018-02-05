---
title: Verankern Sie die Tag-Hilfsprogramm | Microsoft Docs
author: pkellner
description: Zeigt, wie mit Anker Tag Hilfsprogramm arbeiten
ms.author: riande
manager: wpickett
ms.date: 12/20/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 74609b515936ec7da8bfc133c27cb69f51311924
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
# <a name="anchor-tag-helper"></a>Anchor-Tag-Hilfsprogramm

Von [Peter Kellner](http://peterkellner.net) 

Anchor-Tag-Hilfsprogramm verbessert das HTML-Anchortag (`<a ... ></a>`) Tag, indem Sie neue Attribute hinzufügen. Der generierte Link (auf der `href` Tag) wird mit neuen Attributen erstellt. Diese URL kann ein optionale Protokoll wie Https enthalten.

Unten Lautsprecher Controller wird in Beispielen in diesem Dokument verwendet.

**SpeakerController.cs** 

[!code-csharp[SpeakerController](sample/TagHelpersBuiltInAspNetCore/src/TagHelpersBuiltInAspNetCore/Controllers/SpeakerController.cs)]


## <a name="anchor-tag-helper-attributes"></a>Anker Tagattribute-Hilfsprogramm

### <a name="asp-controller"></a>ASP-controller

`asp-controller`Dient zum Zuordnen der Controller zum Generieren der URL verwendet wird. Die angegebenen Controller müssen im aktuellen Projekt vorhanden sein. Der folgende Code Listet alle Lautsprecher: 

```cshtml
<a asp-controller="Speaker" asp-action="Index">All Speakers</a>
```

Das generierte Markup werden verwendet:

```html
<a href="/Speaker">All Speakers</a>
```

Wenn die `asp-controller` angegeben ist und `asp-action` ist nicht die Standardeinstellung `asp-action` wird die Standardmethode für die Controller der aktuell ausgeführten angezeigt werden. Ist im obigen Beispiel, wenn `asp-action` wird ausgelassen, und dieses Anchor-Tag-Hilfsprogramm wird generiert *HomeController*des `Index` Ansicht (**/Home**), werden die generierten Markup:

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>ASP-Aktion

`asp-action`Der Name der Aktionsmethode im Controller, die eingeschlossen werden in der generierten `href`. Z. B. der folgende Code generierten festlegen `href` um zur Detailseite sprechender Benutzer verweisen:

```html
<a asp-controller="Speaker" asp-action="Detail">Speaker Detail</a>
```

Das generierte Markup werden verwendet:

```html
<a href="/Speaker/Detail">Speaker Detail</a>
```

Wenn kein `asp-controller` -Attribut angegeben ist, wird der Standard-Controller Aufrufen der Sicht, die Ausführung der aktuellen Ansicht verwendet werden.  
 
Wenn das Attribut `asp-action` ist `Index`, wird keine Aktion an die URL, auf den Standardwert führende angefügt ist `Index` aufgerufenen Methode. Die Aktion angegebenen (oder standardmäßig), muss vorhanden sein, im Controller in referenziert `asp-controller`.

### <a name="asp-page"></a>ASP-Seite

Verwenden der `asp-page` -Attribut in ein Ankertag, legen Sie die URL zu einer bestimmten Seite verweisen. Der Name der Seite mit einem Schrägstrich voranstellen "/" erstellt die URL. Die URL im folgenden Beispiel verweist auf die Seite "Lautsprecher" im aktuellen Verzeichnis.

```cshtml
<a asp-page="/Speakers">All Speakers</a>
```

Die `asp-page` Attribut im vorherigen Codebeispiel rendert die HTML-Ausgabe in der Sicht, ähnlich wie der folgende Codeausschnitt ist:

```html
<a href="/items?page=%2FSpeakers">Speakers</a>
```

Die `asp-page` Attribut sich gegenseitig ausschließende mit ist der `asp-route`, `asp-controller`, und `asp-action` Attribute. Allerdings `asp-page` genutzt werden `asp-route-id` routing, steuern, wie im folgenden Codebeispiel veranschaulicht:

```cshtml
<a asp-page="/Speaker" asp-route-id="@speaker.Id">View Speaker</a>
```

Die `asp-route-id` erzeugt die folgende Ausgabe:

```html
https://localhost:44399/Speakers/Index/2?page=%2FSpeaker
```

> [!NOTE]
> Verwenden der `asp-page` Attribut in Razor-Pages, die URLs muss ein relativer Pfad sein, z. B. `"./Speaker"`. Relative Pfade in der `asp-page` Attribut sind nicht verfügbar in MVC-Ansichten. Verwenden Sie stattdessen die Syntax "/" für MVC-Ansichten.

### <a name="asp-route-value"></a>asp-route-{value}

`asp-route-`ist ein Platzhalter-Routenpräfix. Alle Werte, die Sie einfügen, nachdem nachfolgende Bindestrich als einen potenziellen Routenparameter interpretiert wird. Wenn eine Standardroute nicht gefunden wird, wird diese Routenpräfix zu der generierten Href als Anforderungsparameter und Wert angefügt. Andernfalls wird es in der routenvorlage ersetzt.

Vorausgesetzt, Sie haben eine Controllermethode wie folgt definiert:

```csharp
public IActionResult AnchorTagHelper(string id)
{
    var speaker = new SpeakerData()
    {
        SpeakerId = 12
    };
    return View(viewName, speaker);
}
```

Und haben die Standardvorlage für die Route definiert, die Ihrem *Startup.cs* wie folgt:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
});

```

Die **Cshtml** Datei mit der Anker-Tag-Hilfsmethode zum Verwenden der **Lautsprecher** Modellparameter auf dem Controller an die Ansicht übergeben lautet wie folgt:

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-id=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

Die generierte HTML-Code kann dann wie folgt werden, da **Id** in die Standardroute gefunden wurde.

```html
<a href='/Speaker/Detail/12'>SpeakerId: 12</a>
```

Wenn das Routenpräfix nicht Teil der routing-Vorlage gefunden wird, wird dies ist der Fall mit den folgenden **Cshtml** Datei:

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-speakerid=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

Die generierte HTML-Code kann dann wie folgt werden, da **Speakerid** wurde nicht gefunden, in der Route verglichen:

```html
<a href='/Speaker/Detail?speakerid=12'>SpeakerId: 12</a>
```

Wenn entweder `asp-controller` oder `asp-action` nicht angegeben werden, und klicken Sie dann die gleichen standardverarbeitung eingehalten wird, wie in der `asp-route` Attribut.

### <a name="asp-route"></a>ASP-route

`asp-route`bietet eine Möglichkeit, eine URL direkt für eine benannte Route erstellen. Verwenden von routing Attributen, eine Route kann benannt werden entsprechend der `SpeakerController` und verwendet die `Evaluations` Methode.

`Name = "speakerevals"`teilt dem Anker Tag-Hilfsprogramm zum Generieren einer Route direkt auf diesem Controllermethode, die mit der URL `/Speaker/Evaluations`. Wenn `asp-controller` oder `asp-action` angegeben ist, zusätzlich zu `asp-route`, die Route generiert möglicherweise nicht Ihren Erwartungen. `asp-route`sollte nicht verwendet werden, mithilfe eines der Attribute `asp-controller` oder `asp-action` zur Vermeidung eines Konflikts Route.

### <a name="asp-all-route-data"></a>asp-all-route-data

`asp-all-route-data`ermöglicht das Erstellen eines Wörterbuchs von Schlüssel-Wert-Paaren, in dem der Schlüssel ist der Name des Parameters und der Wert ist der Wert mit dem Schlüssel zugeordnet ist.

Wie im Beispiel unten zeigt wird ein Inline-Wörterbuch erstellt, und die Daten in der Razor-Ansicht übergeben werden. Als Alternative können die Daten auch mit Ihrem Modell übergeben werden.

```cshtml
@{
    var dict =
        new Dictionary<string, string>
        {
            {"speakerId", "11"},
            {"currentYear", "true"}
        };
}
<a asp-route="speakerevalscurrent"
asp-all-route-data="dict">SpeakerEvals</a>
```

Der obige Code generiert die folgende URL: http://localhost/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true

Wenn der Link geklickt wird, wird die Controllermethode `EvaluationsCurrent` aufgerufen wird. Wird aufgerufen, da dieser Controller zwei Zeichenfolgenparameter verfügt, die mit übereinstimmen, was erstellt wurde die `asp-all-route-data` Wörterbuch.

Wenn sämtliche Schlüssel im Wörterbuch übereinstimmen Parameter weiterleiten, diese Werte in der Route nach Bedarf ersetzt, und die andere nicht übereinstimmende Werte als Anforderungsparameter generiert werden.

### <a name="asp-fragment"></a>ASP-fragment

`asp-fragment`definiert ein URL-Fragment, an die URL angefügt werden soll. Anchor-Tag-Hilfsprogramm fügen das Hashzeichen (#). Wenn Sie ein Tag zu erstellen:

```cshtml
<a asp-action="Evaluations" asp-controller="Speaker"  
   asp-fragment="SpeakerEvaluations">About Speaker Evals</a>
```

Die generierte URL: http://localhost/Speaker/Evaluations#SpeakerEvaluations

Hash-Tags sind nützlich, wenn die clientseitige Anwendungen zu erstellen. Sie können zum einfach markieren und suchen beispielsweise in JavaScript verwendet werden.

### <a name="asp-area"></a>asp-area

`asp-area`Legt den Bereichsnamen, den ASP.NET Core verwendet wird, um die entsprechenden Route festzulegen. Im folgenden sind Beispiele für wie das Area-Attribut bewirkt, dass eine neuzuordnung von Routen. Festlegen von `asp-area` Blogs Präfixe das Verzeichnis `Areas/Blogs` auf die Routen den zugeordneten Controller und Ansichten für diese Anchortag.

* Projektname
  * wwwroot
  * Bereiche
    * Blogs
      * Domänencontroller
        * HomeController.cs
      * Ansichten
        * Startseite
          * Index.cshtml
          * AboutBlog.cshtml
  * Domänencontroller

Angeben einer Bereich Tag gültig ist, z. B. ```area="Blogs"``` beim Verweisen auf die ```AboutBlog.cshtml``` Datei sieht wie folgt unter Verwendung des Premium-Tag-Hilfsprogramms.

```cshtml
<a asp-action="AboutBlog" asp-controller="Home" asp-area="Blogs">Blogs About</a>
```

Die generierte HTML-Code wird das Segment Bereiche enthalten und werden wie folgt:

```html
<a href="/Blogs/Home/AboutBlog">Blogs About</a>
```

> [!TIP]
> Für MVC-Bereiche in einer Webanwendung arbeiten muss die routenvorlage einen Verweis auf den Bereich enthalten, falls vorhanden. Diese Vorlage, die der zweite Parameter ist von der `routes.MapRoute` -Methodenaufruf, als angezeigt:`template: '"{area:exists}/{controller=Home}/{action=Index}"'`

### <a name="asp-protocol"></a>asp-protocol

Die `asp-protocol` wird ein Protokoll angegeben (z. B. `https`) in der URL. Ein Beispiel für Anchor-Tag-Hilfsprogramm, das das Protokoll enthält wird wie folgt aussehen:

```<a asp-protocol="https" asp-action="About" asp-controller="Home">About</a>```

und generieren HTML wie folgt:

```<a href="https://localhost/Home/About">About</a>```

Die Domäne im Beispiel ist "localhost", aber die Anker-Tag-Hilfsprogramm verwendet die Website für die öffentliche Domäne beim Generieren der URL.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bereiche](xref:mvc/controllers/areas)
