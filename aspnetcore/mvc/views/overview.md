---
title: Ansichten im Kern der ASP.NET MVC
author: ardalis
description: Erfahren Sie, wie Ansichten der app-datendarstellung und die Benutzerinteraktion in ASP.NET Core MVC behandeln.
keywords: ASP.NET Core, MVC, Razor, Viewmodel, Viewdata, Viewbag anzeigen
ms.author: riande
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: d3fbdecaed87b3432f0532748a0833c833c65129
ms.sourcegitcommit: a60a99104fe7a29e271667cead6a06b6d8258d03
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2017
---
# <a name="views-in-aspnet-core-mvc"></a>Ansichten im Kern der ASP.NET MVC

Durch [Steve Smith](https://ardalis.com/) und [Luke Latham](https://github.com/guardrex)

Dieses Dokument erläutert, Ansichten, die in ASP.NET Core MVC-Anwendungen verwendet wird. Informationen für Razor-Pages finden Sie unter [Einführung in Razor-Pages](xref:mvc/razor-pages/index).

In der **M**Odel -**V**vorhandenes -**C**Ontroller (MVC)-Muster, die *Ansicht* verarbeitet die app Daten Präsentation und Benutzerinteraktion. Eine Sicht ist eine HTML-Vorlage mit eingebetteten [Razor Markup](xref:mvc/views/razor). Razor-Markup ist Code, der Interaktion mit HTML-Markup, um eine Webseite zu erzeugen, die an den Client gesendet wird.

In ASP.NET-MVC Core Ansichten sind *cshtml* Dateien, die [C#-Programmiersprache](/dotnet/csharp/) in Razor-Markup. In der Regel anzeigen von Dateien in Ordnern für jede von der app gruppiert [Controller](xref:mvc/controllers/actions). Die Ordner befinden sich einem *Ansichten* Ordner im Stammverzeichnis der app:

![Ordner Views in Projektmappen-Explorer von Visual Studio ist mit dem Basisordner anzuzeigende About.cshtml Contact.cshtml und Index.cshtml Dateien öffnen öffnen](overview/_static/views_solution_explorer.png)

Die *Home* Controller wird dargestellt, indem Sie eine *Home* Ordner innerhalb der *Ansichten* Ordner. Die *Home* Ordner enthält Ansichten für die *zu*, *Kontakt*, und *Index* (Startseite) Webseiten. Wenn ein Benutzer fordert eine der drei Webseiten, Controlleraktionen in der *Home* Controller zu ermitteln, welche der drei Ansichten zum Erstellen und Zurückgeben einer Webseite für den Benutzer verwendet wird.

Verwendung [Layouts](xref:mvc/views/layout) konsistent Webseite Abschnitte und codewiederholungen reduzieren. Layouts enthalten oft den Header, Navigation und im Menü-Elemente und die Fußzeile. Kopf- und Fußzeilen enthalten normalerweise Textbaustein Markup für viele Metadatenelementen sowie Links zu Skripts und des Stils Bestand. Layouts können Sie die diesem Textbaustein Markup in Ihren Ansichten zu vermeiden.

[Teilansichten](xref:mvc/views/partial) Codeduplikaten durch die Verwaltung von wieder verwendbare Teile der Ansichten reduzieren. Eine Teilansicht eignet sich z. B. für ein Autor Profildaten für eine Blogwebsite, die in mehreren Ansichten angezeigt wird. Ein Autor Profildaten ist Normalansicht Inhalt und erfordert Code zum Ausführen, um den Inhalt für die Webseite zu erzeugen. Autor Profildaten Inhalt ist an die Ansicht von allein, wurden die modellbindung verfügbar, deshalb ideal eine Teilansicht für diesen Typ von Inhalt zu verwenden ist.

[Anzeigen von Komponenten](xref:mvc/views/view-components) sind partielle ähnlich wie Ansichten, sie codewiederholungen reduzieren können, aber das erscheint für Inhalt anzeigen, die auf dem Server ausgeführt wird, um die Webseite rendern Code erfordert geeignet. Anzeigen von Komponenten sind nützlich, wenn es sich bei der gerenderte Inhalt Datenbankinteraktion haben, z. B. für eine Website mit dem Einkaufswagen ist erforderlich. Ansichtskomponenten sind nicht begrenzt auf die um Bindung zu modellieren, um die Ausgabe der Webseite.

## <a name="benefits-of-using-views"></a>Vorteile der Verwendung von Sichten

Ansichten zur Verfügung, zum Herstellen einer [ **S**Eparation **o**f **C**Oncerns (SoC) Entwurf](http://deviq.com/separation-of-concerns/) innerhalb einer MVC-app durch die Trennung von Benutzer-Schnittstelle Markup aus andere Teile der app. SoC Entwurf nach macht Ihre app modular aufgebaut, die bietet mehrere Vorteile:

* Die app ist einfacher zu verwalten, da es besser organisiert ist. Ansichten werden im Allgemeinen von app-Funktion gruppiert. Dies erleichtert die zugehörige Sichten zu suchen, bei der Arbeit auf eine Funktion.
* Die Teile der app sind lose verbunden. Erstellen und aktualisieren die app-Ansichten getrennt von der Logik und die Daten Zugriff Geschäftskomponenten. Sie können die Ansichten der app ändern, ohne unbedingt andere Teile der app zu aktualisieren.
* Es ist einfacher, das Benutzeroberflächenkomponenten der app zu testen, da die Ansichten separaten Einheiten sind.
* Aufgrund der besseren Organisation ist es weniger wahrscheinlich, dass Sie versehentlich wiederholen Abschnitte der Benutzeroberfläche werden.

## <a name="creating-a-view"></a>Erstellen einer Ansicht

Sichten, die für einen Controller spezifisch sind werden erstellt, der *Ansichten / [ControllerName]* Ordner. Sichten, die für Domänencontroller, freigegeben werden befinden sich der *Ansichten/freigegeben* Ordner. Um eine Ansicht zu erstellen, fügen Sie eine neue Datei hinzu, und geben Sie ihm den gleichen Namen wie der Aktion zugeordneten Controller mit dem *cshtml* Dateierweiterung. Eine Sicht erstellt, der mit übereinstimmt der *zu* Aktion in der *Home* Controller, erstellen eine *About.cshtml* in der Datei die *Ansichten/Start*Ordner:

[!code-cshtml[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* Markup beginnt mit der `@` Symbol. Zur C#-Anweisungen durch Platzieren von C#-code in [Razor Codeblöcke](xref:mvc/views/razor#razor-code-blocks) abgegrenzt von geschweiften Klammern (`{ ... }`). Finden Sie beispielsweise die Zuweisung von "About" um `ViewData["Title"]` oben. Sie können Werte in HTML anzeigen, indem Sie einfach verweisen auf den Wert mit dem `@` Symbol. Den Inhalt der `<h2>` und `<h3>` oben aufgeführten Elemente.

Den Inhalt der oben gezeigten ist nur ein Teil der gesamten Webseite, die für dem Benutzer gerendert wird. Der Rest der Seite Layout und andere allgemeine Aspekte der Sicht werden angegeben, in anderen Dateien anzeigen. Weitere Informationen finden Sie unter der [Layout Thema](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Wie Controller Ansichten angeben

Ansichten werden in der Regel zurückgegeben, von Aktionen als ein [ViewResult](/aspnet/core/api/microsoft.aspnetcore.mvc.viewresult), d. h. einen Typ von [ActionResult](/aspnet/core/api/microsoft.aspnetcore.mvc.actionresult). Die Aktionsmethode erstellen und zurückgeben kann eine `ViewResult` direkt, jedoch, ist nicht im Allgemeinen vorgenommen. Da die meisten Domänencontroller erben [Controller](/aspnet/core/api/microsoft.aspnetcore.mvc.controller), verwenden Sie einfach die `View` Hilfsmethode zurückzugebenden der `ViewResult`:

*HomeController.cs*

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Wenn diese Aktion zurückgegeben wird, die *About.cshtml* anzeigen, die im vorherigen Abschnitt gezeigt als der folgenden Webseite gerendert wird:

![Zu den Seiten im Edge-Browser gerendert](overview/_static/about-page.png)

Die `View` -Hilfsmethode verfügt über mehrere Überladungen. Optional können Sie Folgendes angeben:

* Eine explizite Ansicht zurückgeben:

  ```csharp
  return View("Orders");
  ```
* Ein [Modell](xref:mvc/models/model-binding) Übergabe an die die Sicht:

  ```csharp
  return View(Orders);
  ```
* Eine Sicht und ein Modell:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>View-Ermittlung

Ein Prozess wird aufgerufen, wenn eine Aktion eine Sicht zurückgegeben wird, *Ansicht Ermittlung* erfolgt. Dieser Prozess wird bestimmt, welche Datei verwendet wird anhand des Ansichtsnamens. 

Das Standardverhalten der `View` Methode (`return View();`) ist eine Sicht mit dem gleichen Namen wie die Aktionsmethode zurückgeben, von dem sie aufgerufen wird. Z. B. die *zu* `ActionResult` Methodennamen des Controllers wird zur Suche nach einer Datei anzeigen, die mit dem Namen *About.cshtml*. Die Common Language Runtime sucht zuerst die *Ansichten / [ControllerName]* Ordner für die Ansicht. Wenn es keine entsprechende Ansicht gefunden werden, sucht der *Shared* Ordner für die Ansicht.

Es spielt keine Rolle, wenn Sie, implizit zurückkehren die `ViewResult` mit `return View();` oder übergeben Sie den Namen der Ansicht, um explizit die `View` Methode mit `return View("<ViewName>");`. Zeigen Sie in beiden Fällen Ermittlung sucht nach einer übereinstimmenden Ansichtsdatei, in der angegebenen Reihenfolge aus:

   1. *Views/\[ControllerName]\[ViewName].cshtml*
   1. *Views/Shared/\[ViewName].cshtml*

Anstatt ein Ansichtsname kann ein Dateipfad für die Sicht angegeben werden. Wenn einen absoluten Pfad, angefangen beim Stamm app verwenden (optional beginnend mit "/" oder "~ /"), wird die *cshtml* Erweiterung muss angegeben werden:

```csharp
return View("Views/Home/About.cshtml");
```

Sie können auch einen relativen Pfad zum Angeben von Ansichten in verschiedenen Verzeichnissen ohne die *cshtml* Erweiterung. Innerhalb der `HomeController`, können Sie zurückkehren, die *Index* -Ansicht Ihrer *verwalten* Ansichten mit einem relativen Pfad:

```csharp
return View("../Manage/Index");
```

Auf ähnliche Weise können Sie angeben, das aktuelle Controller-spezifische Verzeichnis mit dem ". /" Präfix:

```csharp
return View("./About");
```

[Teilansichten](xref:mvc/views/partial) und [anzeigen Komponenten](xref:mvc/views/view-components) ähnliche (aber nicht identisch) Ermittlungsmechanismus verwenden.

Sie können anpassen, dass die Standardkonvention dafür, wie Ansichten innerhalb der app gespeichert werden, mithilfe ein benutzerdefinierten [IViewLocationExpander](/aspnet/core/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).

Ermittlung der Ansicht basiert auf Dateinamen Suchen von Dateien anzeigen. Wenn das zugrunde liegende Dateisystem Groß-/Kleinschreibung beachtet wird, werden Sichtnamen wahrscheinlich Groß-/Kleinschreibung beachtet. Aus Kompatibilitätsgründen betriebssystemübergreifende übereinstimmen Sie Schreibweise und die zwischen Controller und Aktion und zugeordnete Ansicht-Ordner und Dateinamen. Wenn ein Fehler aufgetreten ist, den eine Datei nicht gefunden werden kann, bei der Arbeit mit einem Dateisystem Groß-/Kleinschreibung beachtet wird, vergewissern Sie sich, dass die Groß-/Kleinschreibung zwischen die angeforderte Ansicht-Datei und den Dateinamen der tatsächlichen Ansicht übereinstimmt.

Führen Sie die bewährte Methode zum Organisieren von der Dateistruktur für Ihre Ansichten, um die Beziehungen zwischen Domänencontrollern, Aktionen und Ansichten für die Verwaltbarkeit und Klarheit widerzuspiegeln.

## <a name="passing-data-to-views"></a>Übergeben von Daten mit Ansichten

Sie können Daten mit Ansichten mithilfe von mehreren Ansätzen übergeben. Die zuverlässigste Methode ist die Angabe einer [Modell](xref:mvc/models/model-binding) Typ in der Ansicht. Dieses Modell wird häufig als bezeichnet eine *Viewmodel*. Sie können eine Instanz des Typs Viewmodel zur Ansicht übergeben, von der Aktion.

Ein Viewmodel Daten an eine Ansicht zu übergeben, können die Ansicht zu nutzen *starken* Überprüfung des Typs. *Starke Typisierung* (oder *stark typisierte*) bedeutet, dass jede Variable und jede Konstante einen explizit definierten Typ aufweist (z. B. `string`, `int`, oder `DateTime`). Die Gültigkeit der Typen, die in einer Ansicht verwendet, die zum Zeitpunkt der Kompilierung aktiviert ist.

[Visual Studio](https://www.visualstudio.com/vs/) und [Visual Studio Code](https://code.visualstudio.com/) Liste stark typisierte Klasse, Elemente mit einem Feature [IntelliSense](/visualstudio/ide/using-intellisense). Wenn Sie die Eigenschaften von einem Viewmodel anzeigen möchten, geben Sie den Variablennamen ein für das Viewmodel, gefolgt von einem Punkt (`.`). Dadurch können Sie Code schneller mit weniger Fehlern zu schreiben.

Geben Sie ein Modell mithilfe der `@model` Richtlinie. Verwenden Sie das Modell mit `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Um das Modell zur Ansicht bereitzustellen, übergibt er der Controller als Parameter:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Es gelten keine Einschränkungen für die Typen, die Sie eine Ansicht bereitstellen können. Es wird empfohlen, **P**unformatierten **O**%ld **C**LR **O**Viewmodels-Objekt (POCO), mit wenigen oder gar keinen Verhalten (Methoden) definiert. Viewmodel-Klassen werden in der Regel entweder gespeichert, der *Modelle* Ordner oder ein separates *ViewModels* Ordner im Stammverzeichnis der app. Die *Adresse* Viewmodel verwendet, die im obigen Beispiel ist eine POCO-Viewmodel gespeichert in einer Datei namens *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

> [!NOTE]
> Nichts daran hindert Sie die gleichen Klassen für die Viewmodel-Typen und Ihre Business-Modelltypen verwenden. Verwenden von separaten Modellen kann jedoch Ihre Ansichten unabhängig von der Geschäftslogik und die Daten Zugriff Teile Ihrer app variieren. Trennung von Modellen und Viewmodels bietet zudem Sicherheitsvorteile, wenn Modelle verwenden [modellbindung](xref:mvc/models/model-binding) und [Überprüfung](xref:mvc/models/validation) für Daten, die an die app vom Benutzer gesendet.


<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-and-viewbag"></a>Schwach typisierte Daten (ViewData und ViewBag)

Hinweis: `ViewBag` ist in der Razor-Pages nicht verfügbar.

Zusätzlich zu den stark typisierten Ansichten Ansichten haben Zugriff auf eine *schwach typisierte* (so genannte *lose typisierten*) Sammeln von Daten. Im Gegensatz zu starke Typen *unsichere Typen* (oder *lose Typen*) bedeutet, dass Sie explizit den Typ der Daten deklarieren nicht, Sie verwenden. Die Auflistung der schwach typisierten Daten können für kleine Mengen von Daten aus dem Controller und Ansichten übergeben.

| Übergeben von Daten zwischen einem...                        | Beispiel                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Controller und einer Ansicht                             | Auffüllen einer Dropdownliste mit Daten.                                          |
| Anzeigen und eine [Layoutansicht](xref:mvc/views/layout)   | Festlegen der  **\<Title >** Elementinhalt in der Layoutansicht aus einer Datei anzeigen.  |
| [Teilansicht](xref:mvc/views/partial) und einer Ansicht | Ein Widget, das Daten basierend auf der Webseite, die vom Benutzer angeforderte anzeigt.      |

Diese Auflistung kann verwiesen werden, entweder über die `ViewData` oder `ViewBag` Eigenschaften für Controller und Ansichten. Die `ViewData` Eigenschaft ist ein Wörterbuch von schwach typisierte Objekte. Die `ViewBag` Eigenschaft ist ein Wrapper um `ViewData` , dynamische Eigenschaften bereitstellt, für das zugrunde liegende `ViewData` Auflistung.

`ViewData`und `ViewBag` dynamisch zur Laufzeit aufgelöst werden. Seit der Kompilierung typüberprüfung bieten nicht, sind beide im Allgemeinen mehr als fehleranfällig als die Verwendung einer Viewmodel. Aus diesem Grund einige Entwickler bevorzugen Minimal oder nie `ViewData` und `ViewBag`.


<a name="VD"></a>

**ViewData**

`ViewData`ist eine [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) Objekt erfolgt über `string` Schlüssel. Zeichenfolgendaten können gespeichert und ohne die Notwendigkeit einer Typumwandlung direkt verwendet werden, jedoch müssen Sie eine Umwandlung andere `ViewData` Objektwerten auf bestimmte Typen, wenn Sie zu extrahieren. Sie können `ViewData` übergeben von Daten von Controller mit Ansichten und in Ansichten, einschließlich [Teilansichten](xref:mvc/views/partial) und [Layouts](xref:mvc/views/layout).

Im folgenden ist ein Beispiel, das Werte für einen Gruß und eine Adresse mit `ViewData` in einer Aktion:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Arbeiten Sie mit den Daten in einer Ansicht:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

**ViewBag**

Hinweis: `ViewBag` ist in der Razor-Pages nicht verfügbar.

`ViewBag`ist eine [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) -Objekt, das dynamischen Zugriff auf die Objekte, die in gespeicherten `ViewData`. `ViewBag`ist besonders angenehm beim besser zu handhaben, da es keine Typumwandlung erforderlich. Das folgende Beispiel zeigt, wie Sie `ViewBag` dasselbe Ergebnis erzielt als mit `ViewData` oben:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**ViewData und ViewBag gleichzeitig zu verwenden**

Hinweis: `ViewBag` ist in der Razor-Pages nicht verfügbar.

Da `ViewData` und `ViewBag` finden Sie in den gleichen zugrunde liegende `ViewData` -Auflistung, können Sie beide `ViewData` und `ViewBag` mischen und zwischen ihnen beim Lesen und Schreiben von Werten entsprechen.

Legen Sie den Titel mit `ViewBag` und die Beschreibung mit `ViewData` am oberen Rand einer *About.cshtml* anzeigen:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Lesen der Eigenschaften jedoch die Verwendung von reverse `ViewData` und `ViewBag`. In der *_Layout.cshtml* Datei, rufen Sie den Titel mit `ViewData` und rufen Sie die Beschreibung mit `ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Beachten Sie, dass Zeichenfolgen eine Umwandlung für erfordern `ViewData`. Sie können `@ViewData["Title"]` ohne Umwandlung.

Mit beiden `ViewData` und `ViewBag` auf die gleiche Uhrzeit funktioniert wie mischen und anpassen, lesen und schreiben die Eigenschaften. Das folgende Markup gerendert wird:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Zusammenfassung der Unterschiede zwischen ViewData und ViewBag**

 `ViewBag`ist nicht verfügbar in der Razor-Pages.

* `ViewData`
  * Leitet sich von [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), sodass er Wörterbucheigenschaften verfügt, die hilfreich sein, z. B. `ContainsKey`, `Add`, `Remove`, und `Clear`.
  * Schlüssel im Wörterbuch sind Zeichenfolgen, daher Leerzeichen zulässig ist. Ein Beispiel: `ViewData["Some Key With Whitespace"]`
  * Beliebiger Typ außer einem `string` umgewandelt werden muss, in der Ansicht mit `ViewData`.
* `ViewBag`
  * Leitet sich von [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), daher lässt die Erstellung von dynamischen Eigenschaften, die Verwendung der Punktnotation (`@ViewBag.SomeKey = <value or object>`), und es ist keine Typumwandlung erforderlich. Die Syntax des `ViewBag` ist es schneller, Controller und Ansichten hinzufügen.
  * Einfacher, die null-Werte zu überprüfen. Ein Beispiel: `@ViewBag.Person?.Name`

**Wann ViewData oder ViewBag verwenden**

Beide `ViewData` und `ViewBag` sind gleichermaßen gültig Ansätze für kleine Mengen von Daten zwischen Controllern und Ansichten übergeben. Die Auswahl der, die eine zu verwenden (oder beides) nach unten zu überlassen oder die Einstellung Ihrer Organisation stammen. Obwohl Sie mischen und zuordnen können `ViewData` und `ViewBag` -Objekte ist der Code einfacher zu lesen und zu verwalten, wenn Sie wählen Sie nur einen und verwenden Sie diese durchgängig. Da beide dynamisch zur Laufzeit aufgelöst und anfällig für Laufzeitfehler verursacht werden, verwenden Sie sie sorgfältig. Einige Entwickler vermeiden komplett.

### <a name="dynamic-views"></a>Dynamische Ansichten

Geben Sie Sichten, die ein Modell zu deklarieren, nicht mit `@model` jedoch eine Modellinstanz übergebenen aufweisen (z. B. `return View(Address);`) können die Eigenschaften der Instanz dynamisch verweisen:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Diese Funktion bietet Flexibilität, aber es bietet keine Kompilierung Schutz noch IntelliSense zur Verfügung. Wenn die Eigenschaft nicht vorhanden ist, schlägt die Webseite Generation zur Laufzeit fehl.

## <a name="more-view-features"></a>Weitere Funktionen

[Kennzeichnen von Hilfsprogrammen](xref:mvc/views/tag-helpers/intro) erleichtern das serverseitige Verhalten vorhandenen HTML-Tags hinzuzufügen. Verwenden von Tag-Hilfsprogrammen vermeidet die Notwendigkeit zum Schreiben von benutzerdefiniertem Code oder Hilfsprogramme in Ihren Ansichten. Tag-Hilfsprogrammen, die als Attribute auf HTML-Elemente angewendet werden und von Editoren, die sie nicht verarbeiten können ignoriert werden. Dadurch können Sie zum Bearbeiten und Rendern von Markup der Sicht in einer Vielzahl von Tools.

Generieren von benutzerdefinierten HTML-Markup kann mit vielen integrierten HTML-Hilfsmethoden erreicht werden. Komplexere Benutzeroberflächenlogik kann behandelt werden, indem [Ansichtskomponenten](xref:mvc/views/view-components). Ansichtskomponenten finden Sie die gleichen SoC, Controller und Ansichten bieten. Sie können angeben, entfällt der Bedarf für Aktionen und Ansichten, die mit Daten, die durch allgemeine Elemente der Benutzeroberfläche verwendet.

Wie viele andere Aspekte von ASP.NET Core Ansichten unterstützen [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), sodass Dienste sind [in Sichten eingefügt](xref:mvc/views/dependency-injection).
