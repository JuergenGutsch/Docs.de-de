---
title: Tag-Hilfsprogramme in Formularen in ASP.NET Core
author: rick-anderson
description: Beschreibt die integrierte Tag Hilfsprogramme mit Forms verwendet.
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/working-with-forms
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: da36985206521798d3bfe71f6372dc5cc4fca09a
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="introduction-to-using-tag-helpers-in-forms-in-aspnet-core"></a>Einführung in die Verwendung von Tag-Hilfsprogramme in Formularen in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Dave Paquette](https://twitter.com/Dave_Paquette), und [Jerrie Pelser](https://github.com/jerriep)

Dieses Dokument veranschaulicht, arbeiten mit Formularen und die HTML-Elemente, die häufig in einem Formular verwendet wird. Der HTML-Code [Formular](https://www.w3.org/TR/html401/interact/forms.html) -Element stellt die primäre Mechanismus Web-apps verwenden zum Bereitstellen von Daten an den Server bereit. Die meisten dieses Dokuments beschreibt [Tag Hilfsprogramme](tag-helpers/intro.md) und wie sie produktiv Erstellung robustere HTML-Formularen helfen können. Sie sollten [Einführung in die Tag-Hilfsprogrammen](tag-helpers/intro.md) , bevor Sie dieses Dokument zu lesen.

In vielen Fällen HTML-Hilfsmethoden bieten eine alternative Methode in einer bestimmten Tag-Hilfsprogramm, aber es ist wichtig zu wissen, dass Tag Hilfsprogramme nicht HTML-Hilfsmethoden ersetzen, und es keines Tag-Hilfsprogramms für jedes HTML-Hilfsobjekt ist. Wenn eine HTML-Hilfsobjekt Alternative vorhanden ist, wird er erwähnt.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Das Form-Tag-Hilfsprogramm

Die [Formular](https://www.w3.org/TR/html401/interact/forms.html) Helper kennzeichnen:

* Den HTML-Code generiert [ \<Formular >](https://www.w3.org/TR/html401/interact/forms.html) `action` Attributwert für ein MVC-Controller-Aktion oder eine benannte Route

* Generiert ein ausgeblendetes [Anforderung Überprüfung Token](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) um websiteübergreifende anforderungsfälschung zu verhindern (bei Verwendung mit der `[ValidateAntiForgeryToken]` Attribut in der Aktionsmethode HTTP Post)

* Stellt die `asp-route-<Parameter Name>` -Attribut, auf dem `<Parameter Name>` die Routenwerte hinzugefügt wird. Die `routeValues` Parameter `Html.BeginForm` und `Html.BeginRouteForm` bieten eine ähnliche Funktionalität.

* Verfügt über Alternative HTML-Hilfsobjekt `Html.BeginForm` und`Html.BeginRouteForm`

Beispiel:

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Das Form-Tag-Hilfsobjekt oben wird der folgenden HTML-Code generiert:

```HTML
<form method="post" action="/Demo/Register">
     <!-- Input and Submit elements -->
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

Der MVC-Laufzeit generiert die `action` -Attributwert aus der Form-Tag-Helper-Attribute `asp-controller` und `asp-action`. Das Form-Tag-Hilfsprogramm generiert außerdem ein ausgeblendetes [Anforderung Überprüfung Token](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) um websiteübergreifende anforderungsfälschung zu verhindern (bei Verwendung mit der `[ValidateAntiForgeryToken]` Attribut in der Aktionsmethode HTTP Post). Schützen ein reines HTML-Formular aus websiteübergreifende anforderungsfälschung ist schwierig, die Form-Tag-Hilfsprogramm stellt diesen Dienst für Sie.

### <a name="using-a-named-route"></a>Verwenden eine benannte route

Die `asp-route` Tag Helper-Attribut kann auch Markup für den HTML-Code generieren `action` Attribut. Eine Anwendung mit einem [Route](../../fundamentals/routing.md) mit dem Namen `register` konnte das folgende Markup für die Seite "Registrierung" verwenden:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Viele der Ansichten in der *Ansichten/Konto* Ordner (generiert, wenn Sie eine neue Web-app erstellen *einzelne Benutzerkonten*) enthalten die [Asp-Route-Returnurl](https://docs.microsoft.com/aspnet/core/mvc/views/working-with-forms) Attribut:

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Mit den integrierten Vorlagen `returnUrl` wird nur automatisch aufgefüllt, wenn Sie versuchen, eine autorisierte Ressource zuzugreifen, aber nicht authentifiziert oder autorisiert. Beim Versuch, eines nicht autorisierten Zugriffs, leitet die Middleware Sicherheit Sie zur Anmeldeseite mit der `returnUrl` festgelegt.

## <a name="the-input-tag-helper"></a>Das Hilfsobjekt Eingabetag

Das Eingabe-Tag-Hilfsobjekt bindet ein HTML [ \<input >](https://www.w3.org/wiki/HTML/Elements/input) Element zu einem Modell Ausdruck in der Razor-Ansicht.

Syntax:

```HTML
<input asp-for="<Expression Name>" />
```

Das Hilfsobjekt Eingabetag:

* Generiert die `id` und `name` HTML-Attribute für den im angegebenen Ausdruck ein die `asp-for` Attribut. `asp-for="Property1.Property2"` entspricht `m => m.Property1.Property2`. Der Name des Ausdrucks ist Verwendungszwecks der `asp-for` Attributwert. Finden Sie unter der [Ausdrucksnamen](#expression-names) Abschnitt, um zusätzliche Informationen.

* Legt den HTML-Code `type` -Attributwert basierend auf den Typ des Modells und [-datenanmerkung](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) Attribute der Modelleigenschaft angewendet werden

* Den HTML-Code wird nicht überschreiben `type` -Attributwert aus, wenn ein solcher festgelegt wurde

* Generiert [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) Überprüfung Attributen von [-datenanmerkung](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) Attribute Modelleigenschaften angewendet werden

* Verfügt über eine HTML-Hilfsobjekt-Funktion mit überschneiden `Html.TextBoxFor` und `Html.EditorFor`. Finden Sie unter der **HTML-Hilfsobjekt Alternativen zur Eingabe Tag Helper** Abschnitt Weitere Informationen.

* Stellt die starke Typisierung. Wenn der Name der der Eigenschaft ändert und Sie die Tag-Hilfsprogramm nicht aktualisieren erhalten Sie eine Fehlermeldung ähnlich der folgenden:

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

Die `Input` Tag Helper legt den HTML-Code `type` Attribut basierend auf dem .NET. Die folgende Tabelle enthält einige allgemeine .NET-Typen und die generierten HTML-Typ (nicht in jedem Typ .NET wird aufgeführt).

|.NET-Typ|Eingabetyp|
|---|---|
|Bool|type=”checkbox”|
|Zeichenfolge|type=”text”|
|DateTime|Typ "Datetime" =|
|Byte|Typ = "Number"|
|Int|Typ = "Number"|
|Single, Double|Typ = "Number"|


Die folgende Tabelle zeigt einige häufige [datenanmerkungen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) Attribute, die das Hilfsprogramm Eingabetag bestimmte Eingabetypen zugeordnet werden kann (nicht jedes Validierungsattribut wird aufgeführt):


|Attribut|Eingabetyp|
|---|---|
|[EmailAddress]|Typ = "Email"|
|[Url]|type=”url”|
|[HiddenInput]|type=”hidden”|
|[Phone]|type=”tel”|
|[DataType(DataType.Password)]| type=”password”|
|[DataType(DataType.Date)]| Typ "Date" =|
|[DataType(DataType.Time)]| type=”time”|


Beispiel:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Der obige Code generiert den folgenden HTML-Code:

```HTML
  <form method="post" action="/Demo/RegisterInput">
       Email:
       <input type="email" data-val="true"
              data-val-email="The Email Address field is not a valid e-mail address."
              data-val-required="The Email Address field is required."
              id="Email" name="Email" value="" /> <br>
       Password:
       <input type="password" data-val="true"
              data-val-required="The Password field is required."
              id="Password" name="Password" /><br>
       <button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

Die datenanmerkungen angewendet, um die `Email` und `Password` Eigenschaften Metadaten für das Modell generieren. Das Eingabe-Tag-Hilfsobjekt nutzt die darin enthaltenen Modellmetadaten und erzeugt [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` Attribute (finden Sie unter [Modellvalidierung](../models/validation.md)). Diese Attribute beschreiben die Validierungssteuerelemente für die Verbindung zu den Eingabefeldern. Dies bietet unaufdringlichen HTML5 und [jQuery](https://jquery.com/) Überprüfung. Die unaufdringlichen Attribute haben das Format `data-val-rule="Error Message"`, wobei der Regel der Name einer Validierungsregel ist (z. B. `data-val-required`, `data-val-email`, `data-val-maxlength`usw..) Wenn eine Fehlermeldung im Attribut bereitgestellt wird, wird angezeigt als Wert für die `data-val-rule` Attribut. Es gibt auch Attribute des Formulars `data-val-ruleName-argumentName="argumentValue"` , die bieten weitere Details über die Regel ein, z. B. `data-val-maxlength-max="1024"` .

### <a name="html-helper-alternatives-to-input-tag-helper"></a>HTML-Hilfsobjekt Alternativen zur Eingabe Tag-Hilfsprogramm

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` und `Html.EditorFor` überlappenden Funktionen mit der Eingabe-Tag-Hilfsmethode. Das Eingabe-Tag-Hilfsobjekt werden automatisch festgelegt, die `type` -Attribut; `Html.TextBox` und `Html.TextBoxFor` wird nicht. `Html.Editor`und `Html.EditorFor` behandeln, Sammlungen, die komplexe Objekte und die Vorlagen nicht das Eingabe-Tag-Hilfsobjekt. Das Eingabe-Tag-Hilfsobjekt `Html.EditorFor` und `Html.TextBoxFor` sind stark typisiert (sie verwenden ein Lambda-Ausdrücke); `Html.TextBox` und `Html.Editor` nicht sind (sie verwenden die Namen von Gruppierungsausdrücken).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()`und `@Html.EditorFor()` mithilfe eines speziellen `ViewDataDictionary` Eintrag namens `htmlAttributes` beim Ausführen ihrer Standardvorlagen. Dieses Verhalten wird optional erweitert mit `additionalViewData` Parameter. Der Schlüssel "HtmlAttributes" ist die Groß-/Kleinschreibung. Der Schlüssel "HtmlAttributes" erfolgt ähnlich wie die `htmlAttributes` -Objekt übergeben, für die Eingabe von Hilfsprogrammen wie `@Html.TextBox()`.

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Ausdrucksnamen

Die `asp-for` Attributwert ist eine `ModelExpression` und der rechten Seite eines Lambda-Ausdrucks. Aus diesem Grund `asp-for="Property1"` wird `m => m.Property1` im generierten Code, daher wird, Sie müssen nicht mit dem Präfix `Model`. Können Sie das "@" Zeichen zu starten, einen Inlineausdruck vor dem Verschieben der `m.`:

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe" />
```

Wird Folgendes generiert:

```HTML
<input type="text" id="joe" name="joe" value="Joe" />
```

Mit Auflistungseigenschaften `asp-for="CollectionProperty[23].Member"` generiert den gleichen Namen wie `asp-for="CollectionProperty[i].Member"` Wenn `i` hat den Wert `23`.

### <a name="navigating-child-properties"></a>Navigieren in der untergeordneten Eigenschaften

Sie können auch auf die untergeordneten Eigenschaften, die mithilfe der Eigenschaftenpfad des Modells anzeigen navigieren. Betrachten Sie eine komplexere Modellklasse, die ein untergeordnetes Element enthält `Address` Eigenschaft.

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

In der Ansicht der Bindung an `Address.AddressLine1`:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Der folgende HTML-Code wird generiert, für `Address.AddressLine1`:

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="" />
```

### <a name="expression-names-and-collections"></a>Ausdrucksnamen und Sammlungen

Beispiel für ein Modell, das ein Array von `Colors`:

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Der Action-Methode:

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

Die folgenden Razor wird gezeigt, wie Sie einen bestimmten zugreifen `Color` Element:

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

Die *Views/Shared/EditorTemplates/String.cshtml* Vorlage:

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

-Beispiels mit `List<T>`:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Die folgenden Razor zeigt, wie eine Auflistung durchlaufen:

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

Die *Views/Shared/EditorTemplates/ToDoItem.cshtml* Vorlage:

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]


>[!NOTE]
>Verwenden Sie immer `for` (und *nicht* `foreach`) eine Liste durchlaufen. Beim Auswerten eines Indexers in einer LINQ Ausdruck kann teuer sein und sollte minimiert werden.

&nbsp;

>[!NOTE]
>Kommentierte Beispielcode oben beschriebene wird gezeigt, wie Sie den Lambdaausdruck mit ersetzen würde die `@` Operator auf jede `ToDoItem` in der Liste.

## <a name="the-textarea-tag-helper"></a>Das Textarea-Tag-Hilfsprogramm

Die `Textarea Tag Helper` Tag Helper ist vergleichbar mit der Eingabe-Tag-Hilfsmethode.

* Generiert die `id` und `name` Attribute und die überprüfungsattribute Daten aus dem Modell für eine [ \<Textarea >](https://www.w3.org/wiki/HTML/Elements/textarea) Element.

* Stellt die starke Typisierung.

* HTML-Hilfsobjekt Alternative:`Html.TextAreaFor`

Beispiel:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Der folgende HTML-Code wird generiert:

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-label-tag-helper"></a>Das Label-Tag-Hilfsprogramm

* Die Beschriftung der Label-Komponente generiert und `for` -Attribut auf einen [ <label> ](https://www.w3.org/wiki/HTML/Elements/label) -Element für den Namen eines Ausdrucks

* Alternative HTML-Hilfsobjekt: `Html.LabelFor`.

Die `Label Tag Helper` bietet die folgenden Vorteile über ein reines HTML Label-Element:

* Sie erhalten automatisch den aussagekräftige Bezeichnung-Wert aus der `Display` Attribut. Verlauf der Zeit und die Kombination von kann sich der gewünschten Anzeigenamen ändern `Display` Attribut und Bezeichnung Tag Helper gelten die `Display` wird überall verwendet.

* Weniger Markup im Quellcode

* Starke Typisierung mit dem Modelleigenschaft.

Beispiel:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Der folgende HTML-Code wird generiert, für die `<label>` Element:

```HTML
<label for="Email">Email Address</label>
```

Das Label-Tag-Hilfsobjekt generiert die `for` zugeordnete Attributwert von "Email", die ID ist der `<input>` Element. Die Tag-Hilfsprogramme generieren konsistent `id` und `for` Elemente, damit sie ordnungsgemäß zugeordnet werden können. Die Beschriftung in diesem Beispiel ergibt sich aus der `Display` Attribut. Wenn das Modell hat nicht eine `Display` -Attribut, die Beschriftung wäre der Eigenschaftsname des Ausdrucks.

## <a name="the-validation-tag-helpers"></a>Die Überprüfung Tag-Hilfsprogramme

Es gibt zwei Überprüfung Tag Hilfsmethoden. Die `Validation Message Tag Helper` (dem zeigt einer validierungsmeldung an, für eine einzelne Eigenschaft für das Modell), und die `Validation Summary Tag Helper` (so wird eine Zusammenfassung der Überprüfungsfehler angezeigt wird). Die `Input Tag Helper` HTML5 Client Side Validierungsattribute Elemente basierend auf Daten auf Ihren Modellklassen anmerkungsattribute Eingabespalten hinzugefügt. Überprüfung wird auch auf dem Server ausgeführt. Der Überprüfungshelfer Tag zeigt diese Fehlermeldungen an, wenn ein Validierungsfehler auftritt.

### <a name="the-validation-message-tag-helper"></a>Tag der Überprüfungshelfer-Nachricht

* Fügt der [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` -Attribut auf die [umfassen](https://developer.mozilla.org/docs/Web/HTML/Element/span) -Element, das die Überprüfungsfehlermeldungen auf das Eingabefeld der angegebenen Modelleigenschaft fügt. Wenn ein Client-Side-Überprüfungsfehler auftritt, [jQuery](https://jquery.com/) zeigt die Fehlermeldung in der `<span>` Element.

* Validierung findet auch auf dem Server. Clients können JavaScript deaktiviert und eine Validierung kann nur auf der Serverseite ausgeführt werden.

* HTML-Hilfsobjekt Alternative:`Html.ValidationMessageFor`

Die `Validation Message Tag Helper` wird verwendet, mit der `asp-validation-for` Attribut in einem HTML [umfassen](https://developer.mozilla.org/docs/Web/HTML/Element/span) Element.

```HTML
<span asp-validation-for="Email"></span>
```

Tag der Überprüfungshelfer-Nachricht wird der folgenden HTML-Code generiert:

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Im Allgemeinen verwendet, die `Validation Message Tag Helper` nach einem `Input` Tag-Hilfsprogramm für die gleiche Eigenschaft. Auf diese Weise zeigt Validierungsfehlermeldungen in der Nähe der Eingabe, die den Fehler verursacht hat.

> [!NOTE]
> Benötigen Sie eine Sicht mit der richtigen JavaScript-Code und [jQuery](https://jquery.com/) Skriptverweise für clientseitige Validierung eingerichtet. Finden Sie unter [Modellvalidierung](../models/validation.md) für Weitere Informationen.

Bei der Überprüfung ein serverseitiger Fehler auftritt (z. B. Wenn Sie benutzerdefinierte Validierung oder die clientseitige Validierung deaktiviert ist), setzt MVC diese Fehlermeldung als Text der `<span>` Element.

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Der Überprüfungshelfer Summary (Tag)

* Ziele `<div>` Elemente mit dem `asp-validation-summary` Attribut

* HTML-Hilfsobjekt Alternative:`@Html.ValidationSummary`

Die `Validation Summary Tag Helper` wird verwendet, um eine Zusammenfassung von validierungsmeldungen anzuzeigen. Die `asp-validation-summary` Attributwert darf keines der folgenden sein:

|Zusammenfassung der ASP-Validierung|Validierungsmeldungen angezeigt|
|--- |--- |
|ValidationSummary.All|Eigenschaft "und" Model-Ebene|
|ValidationSummary.ModelOnly|Modell|
|ValidationSummary.None|Keiner|

### <a name="sample"></a>Beispiel

Im folgenden Beispiel wird das Datenmodell mit versehenen `DataAnnotation` Attribute, die Validierungsfehlermeldungen generiert, auf die `<input>` Element.  Tritt ein Validierungsfehler auf, wird das Tag Überprüfungshelfer die Fehlermeldung angezeigt:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Die generierten HTML-Codes (wenn das Modell gültig ist):

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid e-mail address."
   data-val="true"> <br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-select-tag-helper"></a>Die Select-Tag-Hilfsprogramm

* Generiert [wählen](https://www.w3.org/wiki/HTML/Elements/select) und zugehörigen [Option](https://www.w3.org/wiki/HTML/Elements/option) Elemente für die Eigenschaften des Modells.

* Verfügt über Alternative HTML-Hilfsobjekt `Html.DropDownListFor` und`Html.ListBoxFor`

Die `Select Tag Helper` `asp-for` gibt den Namen des Modell-Eigenschaft für die [wählen](https://www.w3.org/wiki/HTML/Elements/select) Element und `asp-items` gibt an, die [Option](https://www.w3.org/wiki/HTML/Elements/option) Elemente.  Zum Beispiel:

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Beispiel:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

Die `Index` Methode initialisiert die `CountryViewModel`des ausgewählten Landes festlegt und übergibt es an die `Index` anzeigen.

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

Die HTTP-POST `Index` Methode zeigt die Auswahl an:

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

Die `Index` anzeigen:

[!code-cshtml[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Die folgenden HTML-Code (mit "CA" ausgewählt) generiert:

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

> [!NOTE]
> Es empfiehlt sich nicht mit `ViewBag` oder `ViewData` mit dem Tag-Helfer auswählen. Einem Ansichtsmodell ist unter Umständen stabiler MVC-Metadaten bereitstellen und in der Regel weniger problematisch.

Die `asp-for` Attributwert ist ein Sonderfall und erfordert keine `Model` als Präfix des Hilfsprogramm-Tag Attribute stimmen (z. B. `asp-items`)

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Enum-Bindung

Es ist häufig sinnvoll, verwenden Sie `<select>` mit einer `enum` Eigenschaft und generieren die `SelectListItem` Elemente aus der `enum` Werte.

Beispiel:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

Die `GetEnumSelectList` Methode generiert eine `SelectList` Objekt für eine Enumeration.

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Sie können die Enumeratorliste mit ergänzen die `Display` Attribut, um eine umfangreichere Benutzeroberfläche zu erhalten:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Der folgende HTML-Code wird generiert:

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

### <a name="option-group"></a>Optionsgruppe

Der HTML-Code [ \<Optgroup >](https://www.w3.org/wiki/HTML/Elements/optgroup) Element wird generiert, wenn das Ansichtsmodell, eine oder mehrere enthält `SelectListGroup` Objekte.

Die `CountryViewModelGroup` Gruppen der `SelectListItem` Elemente in den Gruppen "North America" und "Europe":

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Die beiden Gruppen sind unten dargestellt:

![Beispiel für eine Option](working-with-forms/_static/grp.png)

Die generierten HTML-Code:

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
```

### <a name="multiple-select"></a>Wählen Sie mehrere

Das Tag-Hilfsobjekt wählen generiert automatisch die [mehrere = "mehrere"](http://w3c.github.io/html-reference/select.html) Attribut, wenn die Eigenschaft in angegeben die `asp-for` -Attribut ist ein `IEnumerable`. Betrachten Sie z. B. die folgenden Modell:

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Mit der folgenden Ansicht:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Generiert den folgenden HTML-Code:

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

### <a name="no-selection"></a>Keine Auswahl getroffen wurde

Wenn Sie selbst unter Verwendung der Option "nicht angegeben" auf mehreren Seiten gefunden haben, können Sie eine Vorlage, um zu vermeiden, wiederholen den HTML-Code erstellen:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

Die *Views/Shared/EditorTemplates/CountryViewModel.cshtml* Vorlage:

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

Hinzufügen von HTML [ \<Option >](https://www.w3.org/wiki/HTML/Elements/option) Elemente ist nicht beschränkt auf die *keine Auswahl* Fall. Die folgende Sicht und Aktion-Methode wird z. B. HTML ähnelt der obige Code generiert:

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

Die richtige `<option>` Element ausgewählt werden (enthalten die `selected="selected"` Attribut) abhängig von der aktuellen `Country` Wert.

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
 ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Taghilfsprogramme](tag-helpers/intro.md)

* [HTML-Formular-element](https://www.w3.org/TR/html401/interact/forms.html)

* [Überprüfung von Anforderungstoken](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)

* [Modellbindung](../models/model-binding.md)

* [Modellvalidierung](../models/validation.md)

* [datenanmerkungen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)

* [Codeausschnitte für dieses Dokument](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/forms/sample).
