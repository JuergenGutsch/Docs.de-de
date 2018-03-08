---
title: "Razor-syntaxverweis für ASP.NET Core"
author: rick-anderson
description: "Erfahren Sie Markup Razor-Syntax für das Einbetten von serverbasiertem Code in Webseiten aus."
keywords: ASP.NET Core, Razor, Razor-Direktiven
ms.author: riande
manager: wpickett
ms.date: 10/18/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: 743c42b26c62d0e24b5d5b487b3154bc249fcff4
ms.sourcegitcommit: a873f862c8e68b2cf2998aaed3dddd93eeba9e0f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2017
---
# <a name="razor-syntax-for-aspnet-core"></a>Razor-Syntax für ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), und [Dan Vicarel](https://github.com/Rabadash8820)

Razor ist eine Markupsyntax für das Einbetten von serverbasiertem Code in Webseiten. Die Razor-Syntax besteht aus Razor-Markup, C#- und HTML. Dateien, die in der Regel enthält Razor haben eine *cshtml* Dateierweiterung.

## <a name="rendering-html"></a>Rendern von HTML

Die Standardsprache für den Razor ist HTML. Rendern von HTML in Razor-Markuperweiterung unterscheidet sich nicht als HTML-Datei aus einer HTML-Datei gerendert.  HTML-Markup *cshtml* Razor-Dateien vom Server unverändert gerendert wird.

## <a name="razor-syntax"></a>Razor-syntax

Razor unterstützt C#- und verwendet die `@` Symbol für den Übergang von HTML in c#. Razor C#-Ausdrücke ausgewertet und rendert sie in der HTML-Ausgabe.

Wenn ein `@` Symbol wird gefolgt von einer [Razor reserviertes Schlüsselwort](#razor-reserved-keywords), geht in Razor-spezifischer Markups. Andernfalls geht es in einfachen c#.

Mit Escapezeichen eine `@` symbol in Razor-Markup, verwenden Sie eine zweite `@` Symbol:

```cshtml
<p>@@Username</p>
```

Der Code wird in HTML gerendert, mit einem einzelnen `@` Symbol:

```html
<p>@Username</p>
```

HTML-Attribute und Inhalt mit e-Mail-Adressen nicht behandeln die `@` Symbol als ein Zeichen Übergang. Die e-Mail-Adressen im folgenden Beispiel werden durch Analysieren von Razor bleiben unverändert:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Implizite Razor-Ausdrücke

Implizite Razor-Ausdrücke beginnen mit `@` gefolgt von C#-Code:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Mit Ausnahme von C#- `await` Schlüsselwort implicit-Ausdrücken keine Leerzeichen enthalten. Wenn die C#-Anweisung eine klare beendet wurde, können Leerzeichen vermischt werden:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implicit-Ausdrücken **kann nicht** C#-Generika, als eines der Zeichen innerhalb der Klammern enthalten (`<>`) als HTML-Tags interpretiert werden. Der folgende Code ist **nicht** gültig:

```cshtml
<p>@GenericMethod<int>()</p>
```

Der vorhergehende Code generiert einen Compilerfehler, der einen der folgenden ähnelt:

 * Das Element "Int" wurde nicht geschlossen.  Alle Elemente muss entweder selbst schließen oder ein entsprechendes Endtag vorhanden.
 *  Die Methodengruppe "GenericMethod" nicht mit Typ "Object" Delegaten kann nicht konvertiert werden. Wollten Sie die Methode aufrufen? " 
 
Generische Methodenaufrufen, müssen umschlossen werden ein [expliziter Razor-Ausdruck](#explicit-razor-expressions) oder ein [Razor-Codeblock](#razor-code-blocks). Diese Einschränkung gilt nicht für *vbhtml* Razor-Dateien, da Visual Basic-Syntax Klammern um generische Typparameter anstelle von Klammern platziert.

## <a name="explicit-razor-expressions"></a>Explizite Razor-Ausdrücke

Explizite Razor-Ausdrücke bestehen aus einer `@` Symbolbreite ausgeglichene Klammer. Zum Zeitpunkt der letzten Woche zu rendern, wird das folgende Razor-Markup verwendet:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Alle Inhalte innerhalb der `@()` Klammern ausgewertet und in die Ausgabe des gerendert.

Implizite Ausdrücke, die im vorherigen Abschnitt beschriebenen darf keine Leerzeichen in der Regel enthalten. Im folgenden Code wird nicht eine Woche nach der aktuellen Uhrzeit subtrahiert:

[!code-cshtml[Main](razor/sample/Views/Home/Contact.cshtml?range=17)]

Der Code wird der folgenden HTML-Code gerendert:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

EXPLICIT-Ausdrücken können zum Verketten von Text mit einem Ergebnis des Ausdrucks verwendet werden:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Ohne die explizite Ausdruck `<p>Age@joe.Age</p>` so behandelt, als eine e-Mail-Adresse und `<p>Age@joe.Age</p>` gerendert wird. Wenn ein expliziter Ausdruck geschrieben `<p>Age33</p>` gerendert wird.


EXPLICIT-Ausdrücken dienen zum Rendern der Ausgabe von generischen Methoden in *cshtml* Dateien. In einem impliziten Ausdruck, der Zeichen innerhalb der Klammern (`<>`) als HTML-Tags interpretiert werden. Das folgende Markup wird **nicht** Razor gültig:

```cshtml
<p>@GenericMethod<int>()</p>
```

Der vorhergehende Code generiert einen Compilerfehler, der einen der folgenden ähnelt:

 * Das Element "Int" wurde nicht geschlossen.  Alle Elemente muss entweder selbst schließen oder ein entsprechendes Endtag vorhanden.
 *  Die Methodengruppe "GenericMethod" nicht mit Typ "Object" Delegaten kann nicht konvertiert werden. Wollten Sie die Methode aufrufen? " 
 
 Das folgende Markup zeigt die richtige Methode schreiben, diesen Code.  Der Code wird als ein expliziter Ausdruck geschrieben:

```cshtml
<p>@(GenericMethod<int>())</p>
```

Hinweis: Diese Einschränkung gilt nicht für *vbhtml* Razor-Dateien.  Mit *vbhtml* Razor-Dateien, Visual Basic-Syntax platziert Klammern um generische Typparameter anstelle von eckigen Klammern.

## <a name="expression-encoding"></a>Expression-Codierung

C#-Ausdrücke, die zu einer Zeichenfolge ausgewertet werden HTML-codiert. C#-Ausdrücke, die ergeben `IHtmlContent` sind direkt über gerendert `IHtmlContent.WriteTo`. C#-Ausdrücke, ergeben nicht `IHtmlContent` werden in eine Zeichenfolge konvertiert `ToString` und codiert werden, bevor sie gerendert werden.

```cshtml
@("<span>Hello World</span>")
```

Der Code wird der folgenden HTML-Code gerendert:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Der HTML-Code wird im Browser als gezeigt:

```
<span>Hello World</span>
```

`HtmlHelper.Raw`Ausgabe wird nicht codierte aber als HTML-Markup gerendert.

> [!WARNING]
> Mit `HtmlHelper.Raw` unsanitized Benutzer Eingabe ist ein Sicherheitsrisiko dar. Benutzereingabe möglicherweise böswilligen JavaScript- oder andere Exploits durchführen enthalten. Es ist schwierig, Bereinigen von Benutzereingaben. Vermeiden Sie die Verwendung `HtmlHelper.Raw` Benutzereingaben.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Der Code wird der folgenden HTML-Code gerendert:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor-Codeblöcke

Razor-Codeblöcke beginnen Sie mit `@` und eingeschlossen werden `{}`. Im Gegensatz zu Ausdrücken wird nicht C#-Code in Codeblöcken gerendert. Codeblöcke Ausdrücke in einer Sicht gemeinsam zu nutzen die gleichen Bereich und werden in Reihenfolge definiert:

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

Der Code wird der folgenden HTML-Code gerendert:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a>Implizite Übergänge

In einem Codeblock die Standardsprache ist c#, aber die Razor-Seite können Übergang zurück zum HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Explizite durch Trennzeichen getrennten Übergang

Um einen Unterabschnitt eines Codeblocks definieren, die HTML gerendert werden soll, umschließen Sie die Zeichen für das Rendering mit den Razor  **\<Text >** Tag:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Verwenden Sie diesen Ansatz zum Rendern von HTML, die von einem HTML-Tags eingeschlossen ist nicht an. Tritt auf, ein Razor-Laufzeitfehler, ohne ein HTML- oder Razor-Tag.

Die  **\<Text >** Tag ist nützlich, um die Leerzeichen beim Rendern von Inhalt zu steuern:

* Nur der Inhalt zwischen die  **\<Text >** -Tags wird gerendert. 
* Keine Leerzeichen vor oder nach der  **\<Text >** Tags in der HTML-Ausgabe angezeigt.

### <a name="explicit-line-transition-with-"></a>Explizite Zeile Übergang mit @:

Um den Rest der eine ganze Zeile in einem Codeblock als HTML zu rendern, verwenden die `@:` Syntax:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Ohne die `@:` im Code wird ein Razor-Laufzeitfehler generiert.

Warnung: Zusätzliche `@` Zeichen in einer Razor-Datei können dazu führen, dass Ursache Compiler-Fehler bei den Anweisungen weiter unten in den Block. Dieser Compilerfehler können schwierig sein, zu verstehen, da der tatsächliche Fehler vor den gemeldeten Fehler auftritt.  Dieser Fehler tritt häufig nach mehrere implizite/explizite Ausdrücke in einen einzelnen Codeblock zu kombinieren.

## <a name="control-structures"></a>Steuerungsstrukturen

Steuerungsstrukturen sind eine Erweiterung von Codeblöcken. Alle Aspekte von Codeblöcken (Übergang zu Markup Inline-c#) auch gelten die folgenden Strukturen:

### <a name="conditionals-if-else-if-else-and-switch"></a>Konditionelle Abschnitte @if, ElseIf, #else- und@switch

`@if`Steuerelemente, wenn der Code ausgeführt wird:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`und `else if` erfordern die `@` Symbol:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

Das folgende Markup zeigt, wie eine Switch-Anweisung:

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Schleifen @for, @foreach, @while, und @do während

Auf Vorlagen basierende HTML kann mit Schleifen Steueranweisungen gerendert werden.  Um eine Liste der Personen zu rendern:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Die folgenden Schleifen Anweisungen werden unterstützt:

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>Zusammengesetzte@using

In c# ist ein `using` Anweisung wird verwendet, um sicherzustellen, dass ein Objekt wurde verworfen. In Razor wird derselbe Mechanismus verwendet, um HTML-Hilfsmethoden zu erstellen, zusätzliche Inhalte enthalten. Im folgenden Code Rendern HTML-Hilfsmethoden ein Formulartag mit der `@using` Anweisung:


```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

Bereichsebene Aktionen können ausgeführt werden, mit [Tag Hilfsprogramme](xref:mvc/views/tag-helpers/intro).

### <a name="try-catch-finally"></a>@try, catch, finally

Behandlung von Ausnahmen ist vergleichbar mit c#:

[!code-cshtml[Main](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

Razor hat die Möglichkeit, kritische Abschnitte mit Lock-Anweisungen zu schützen:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Kommentare

Razor unterstützt C#- und HTML-Kommentare:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Der Code wird der folgenden HTML-Code gerendert:

```html
<!-- HTML comment -->
```

Razor-Kommentare werden vom Server entfernt, bevor auf der Webseite gerendert wird. Razor verwendet `@*  *@` zur Begrenzung von Kommentaren. Der folgende Code ist auskommentiert, damit der Server kein Markup Rendern:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Anweisungen

Razor-Direktiven werden durch implicit-Ausdrücken mit reservierten Schlüsselwörtern folgenden dargestellt die `@` Symbol. Eine Richtlinie ändert sich normalerweise die Möglichkeit, die eine Sicht wird analysiert oder andere Funktionalität ermöglicht.

Verstehen, wie der Razor-Code für eine Ansicht generiert erleichtert das Verständnis der Funktionsweise von Direktiven.

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

Der Code generiert eine Klasse, die etwa wie folgt:

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Weiter unten in diesem Artikel im Abschnitt [Anzeigen der Razor c#-Klasse, die für eine Sicht generiert](#viewing-the-razor-c-class-generated-for-a-view) wird erläutert, wie diese generierte Klasse angezeigt.

### <a name="using"></a>@using

Die `@using` Direktive fügt c# `using` Richtlinie in die generierte Ansicht:

[!code-cshtml[Main](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

Die `@model` Richtlinie gibt den Typ des Modells an eine Ansicht übergeben:

```cshtml
@model TypeNameOfModel
```

In einer ASP.NET Core MVC-app mit einzelne Benutzerkonten erstellt die *Views/Account/Login.cshtml* -Ansicht enthält die folgende Deklaration einer Modell:

```cshtml
@model LoginViewModel
```

Die generierte Klasse erbt von `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor macht eine `Model` Eigenschaft für den Zugriff auf das Modell an die Ansicht zu übergeben:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Die `@model` Richtlinie gibt den Typ dieser Eigenschaft. Die Richtlinie gibt an, die `T` in `RazorPage<T>` , dass die generierte, die Klasse die Sicht abgeleitet ist. Wenn die `@model` Richtlinie iisn't angegeben, die `Model` -Eigenschaft ist vom Typ `dynamic`. Der Wert des Modells wird auf dem Controller an die Ansicht übergeben. Weitere Informationen finden Sie unter [stark typisierte Modelle und die @model Schlüsselwort.

### <a name="inherits"></a>@inherits

Die `@inherits` Richtlinie bietet vollen Zugriff auf die Klasse erbt von die Sicht:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Der folgende Code ist ein benutzerdefinierter Typ des Razor-Seite:

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

Die `CustomText` in einer Ansicht angezeigt wird:

[!code-cshtml[Main](razor/sample/Views/Home/Contact10.cshtml)]

Der Code wird der folgenden HTML-Code gerendert:

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 `@model`und `@inherits` in derselben Ansicht verwendet werden kann.  `@inherits`kann einem *_ViewImports.cshtml* -Datei, die die Sicht importiert:

[!code-cshtml[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

Der folgende Code ist ein Beispiel für eine stark typisierte Ansicht:

[!code-cshtml[Main](razor/sample/Views/Home/Login1.cshtml)]

Wenn "rick@contoso.com" übergeben wird im Modell, Ansicht die folgenden HTML-Markup generiert:

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject


Die `@inject` Richtlinie ermöglicht die Razor-Seite zum Einfügen von eines Diensts von der [Dienstcontainer](xref:fundamentals/dependency-injection) in eine Sicht. Weitere Informationen finden Sie unter [Abhängigkeitsinjektion in Sichten](xref:mvc/views/dependency-injection).

### <a name="functions"></a>@functions

Die `@functions` Richtlinie ermöglicht eine Razor-Seite, um eine Sicht Funktionsebene Inhalt hinzuzufügen:

```cshtml
@functions { // C# Code }
```

Zum Beispiel:

[!code-cshtml[Main](razor/sample/Views/Home/Contact6.cshtml)]

Der Code generiert den folgenden HTML-Markup:

```html
<div>From method: Hello</div>
```

Der folgende Code ist die generierte Razor C#-Klasse:

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

Die `@section` Richtlinie dient in Verbindung mit der [Layout](xref:mvc/views/layout) Ansichten zum Rendern von Inhalt in verschiedenen Teilen der HTML-Seite zu aktivieren. Weitere Informationen finden Sie unter [Abschnitte](xref:mvc/views/layout#layout-sections-label).

## <a name="tag-helpers"></a>Tag-Hilfsprogramme

Es gibt drei Anweisungen, die betreffen [Tag Hilfsprogramme](xref:mvc/views/tag-helpers/intro).

| Direktive | Funktion |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | Stellt Tag Hilfen zu einer Ansicht zur Verfügung. |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Entfernt die Tag-Hilfsprogramme, die zuvor aus einer Sicht hinzugefügt. |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Gibt ein Tagpräfix Tag Helper-Unterstützung zu aktivieren und Tag Helper Verwendung als explizite Anforderung festgelegt. |

## <a name="razor-reserved-keywords"></a>Razor reservierte Schlüsselwörter

### <a name="razor-keywords"></a>Razor-Schlüsselwörter

* Seite "(erfordert ASP.NET Core 2.0 und höher)
* -Funktionen
* inherits
* Modell
* section
* Hilfsprogramm (von ASP.NET Core derzeit nicht unterstützt)

Razor-Schlüsselwörter werden mit Escapezeichen versehen `@(Razor Keyword)` (z. B. `@(functions)`).

### <a name="c-razor-keywords"></a>C#-Razor-Schlüsselwörter

* Groß- und Kleinschreibung
* do
* default
* for
* foreach
* if
* else
* lock
* switch
* try
* catch
* finally
* Verwenden
* while

C#-Razor-Schlüsselwörter muss mit doppelten Escapezeichen `@(@C# Razor Keyword)` (z. B. `@(@case)`). Die erste `@` versieht den Razor-Parser. Die zweite `@` versieht den C#-Parser.

### <a name="reserved-keywords-not-used-by-razor"></a>Reservierte Schlüsselwörter, die nicht vom Razor verwendet

* namespace
* Klasse

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>Anzeigen von für eine Sicht generiert Razor C#-Klasse.

Fügen Sie dem ASP.NET Core MVC-Projekt die folgende Klasse hinzu:

[!code-csharp[Main](razor/sample/Utilities/CustomTemplateEngine.cs)]

Überschreiben Sie die `RazorTemplateEngine` hinzugefügt, indem MVC mit der `CustomTemplateEngine` Klasse:

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=10-14)]

Legen Sie einen Haltepunkt für die `return csharpDocument` SoH `CustomTemplateEngine`. Bei der Ausführung des Programms am Haltepunkt beendet wird, zeigen Sie den Wert des `generatedCode`.

![Text-Schnellansicht Überblick generatedCode](razor/_static/tvr.png)

## <a name="view-lookups-and-case-sensitivity"></a>Ansicht Suchvorgänge und Groß-/Kleinschreibung

Das Razor-Ansichtsmodul führt die Groß-/Kleinschreibung Suchvorgänge für Ansichten. Allerdings wird die tatsächliche Suche durch das zugrunde liegende Dateisystem bestimmt:

* Basis-Dateiquelle: 
  * Unter Betriebssystemen mit Groß-/Kleinschreibung beachten Dateisystemen (z. B. Windows) sind die physische Datei Anbieter Suchvorgänge Groß-/Kleinschreibung beachtet. Beispielsweise `return View("Test")` ergibt Übereinstimmungen für */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, und eine andere Variante der Schreibweise.
  * Auf Dateisystemen, Groß-/Kleinschreibung (z. B. Linux, OS x, und mit `EmbeddedFileProvider`), Suchvorgänge Groß-/Kleinschreibung beachtet. Beispielsweise `return View("Test")` speziell Übereinstimmungen */Views/Home/Test.cshtml*.
* Vorkompilierte Sichten: Nachschlagen vorkompilierte Sichten mit ASP.Net Core 2.0 und höher, Groß-/Kleinschreibung beachtet, unter allen Betriebssystemen ist. Das Verhalten ist identisch mit dem Verhalten der physischen Datei des Anbieters unter Windows. Wenn zwei vorkompilierte Sichten nur im Fall unterschiedlich sind, ist das Ergebnis der Suche nicht deterministisch.

Entwicklern wird empfohlen, die Groß-/Kleinschreibung von Datei- und Verzeichnisnamen, die Groß-/Kleinschreibung der entsprechen:

    * Namen von Bereich, Controller und Aktion. 
    * Razor-Seiten.
    
Groß-wird sichergestellt, dass die Bereitstellungen ihrer Ansichten unabhängig von der zugrunde liegenden Dateisystem suchen.
