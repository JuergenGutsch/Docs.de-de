# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Gerüstbau mit Razor-Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial werden die Razor-Pages näher betrachtet, die durch Gerüstbau im vorherigen Tutorial erstellt wurden. 

Beispiel [Anzeigen oder Herunterladen](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie).

## <a name="the-create-delete-details-and-edit-pages"></a>Die Seiten „Create“, „Delete“, „Details“ und „Edit“.

Überprüfen Sie das Seitenmodell *Pages/Movies/Index.cshtml*: [!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor-Pages werden von `PageModel` abgeleitet. Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt. Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `MovieContext` zur Seite hinzuzufügen. Alle per Gerüstbau erstellten Seiten folgen diesem Muster. Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).

Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die Razor-Page zurück. `OnGetAsync` oder `OnGet` werden für eine Razor-Page aufgerufen, um den Zustand der Seite zu initialisieren. In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an. 

Wenn `OnGet` `void` und `OnGetAsync` `Task` zurückgibt, wird keine Rückgabemethode verwendet. Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden. Die kann z.B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:

<!-- TODO - replace with snippet
[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]
 -->

```csharp
public async Task<IActionResult> OnPostAsync()
{
    if (!ModelState.IsValid)
    {
        return Page();
    }

    _context.Movie.Add(Movie);
    await _context.SaveChangesAsync();

    return RedirectToPage("./Index");
}
```
Betrachten Sie die *Pages/Movies/Index.cshtml*-Razor-Page:

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor kann aus HTML in C# oder Razor-spezifisches Markup übergehen. Wenn auf ein `@`-Symbol ein für Razor reserviertes Schlüsselwort ([Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords)) folgt, wechselt es in Razor-spezifisches Markup. Andernfalls geht es in C# über.

Die Razor-Anweisung `@page` ändert die Datei in eine &mdash;-MVC-Aktion. Das bedeutet, dass sie Anforderungen verarbeiten kann. `@page` muss die erste Razor-Anweisung auf einer Seite sein. `@page` ist ein Beispiel für einen Übergang in Razor-spezifisches Markup. Unter [Razor-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.

Überprüfen Sie den Lambdaausdruck, der in der folgenden HTML-Hilfsfunktion verwendet wird:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

Das HTML-Hilfsprogramm `DisplayNameFor` überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen. Der Lambdaausdruck wird überprüft und nicht ausgewertet. Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]` `null` oder leer sind. Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.

<a name="md"></a>
### <a name="the-model-directive"></a>Die @model -Direktive

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Die `@model`-Anweisung gibt den Typ des Modells an, das an die Razor-Page weitergegeben wird. Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der Razor-Page verfügbar. Das Modell wird in den [HTML Helpers (HTML-Hilfsprogrammen)](https://docs.microsoft.com/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayName` auf der Seite verwendet.

<!-- why don't xref links work?
[HTML Helpers 2](xref:aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs)
-->

<a name="vd"></a>
### ViewData und Layout

Betrachten Sie folgenden Code:

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-)]

Der oben markierte Code ist ein Beispiel vom Übergang von Razor in C#. Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.

Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Hinzufügen von Daten verwendet werden kann, die an eine Ansicht übergeben werden sollen. Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden. Im vorherigen Beispiel wurde dem `ViewData`-Wörterbuch die Eigenschaft „Title“ hinzugefügt. Die Eigenschaft „Title“ wird in der *Pages/_Layout.cshtml*-Datei verwendet. Das folgende Markup zeigt die ersten Zeilen der *Pages/_Layout.cshtml*-Datei.

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout1.cshtml?highlight=6-)]

Die Zeile `@*Markup removed for brevity.*@` ist ein Razor-Kommentar. Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden Razor-Kommentare nicht an den Client gesendet.

Führen Sie die App aus, und testen Sie die Links im Projekt (**Home**, **Details**, **Contact** (Kontakt), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen)). Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet. *Pages/Index.cshtml* und *Pages/Movies/Index.cshtml* haben momentan denselben Titel, sie können aber verändert werden, um ihnen verschiedene Werte zu geben.

Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

Das vorhergehende Markup legt die Layoutdatei für alle Razor-Dateien unter dem Ordner *Seiten* auf *Pages/_Layout.cshtml* fest. Weitere Informationen finden Sie unter [Layout](xref:mvc/razor-pages/index#layout).

### <a name="update-the-layout"></a>Aktualisieren des Layouts

Ändern Sie das Element `<title>` in der Datei *Pages/_Layout.cshtml*, damit es eine kürzere Zeichenfolge verwendet.

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=1-6&highlight=6)]

Suchen Sie in der Datei *Pages/_Layout.cshtml* das folgende Ankerelement.

```cshtml
<a asp-page="/Index" class="navbar-brand">RazorPagesMovie</a>
```
Ersetzen Sie das vorhergehende Element durch das folgende Markup.

```cshtml
<a asp-page="/Movies/Index" class="navbar-brand">RpMovie</a>
```

Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro). In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellt einen Link zur Razor-Page `/Movies/Index`.

Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken. Weitere Informationen finden Sie in der Datei [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml) in GitHub.

### <a name="the-create-code-behind-page"></a>Die CodeBehind-Seite „Create“

Betrachten Sie die *Pages/Movies/Create.cshtml.cs*-CodeBehind-Datei:

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist. Die Seite „Create“ verfügt nicht über einen initialisierbaren Zustand. Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.

Die Eigenschaft `Movie` verwendet das `[BindProperty]`-Attribut, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren. Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.

Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt. Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird. Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann. Später in diesem Tutorial erfahren Sie mehr über die clientseitige Validierung und Modellvalidierung.

Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Indexseite umgeleitet.

### <a name="the-create-razor-page"></a>Die Razor-Page „Create“

Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der Razor-Page:

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

<!--
Visual Studio displays the `<form method="post">` tag in a distinctive font used for Tag Helpers. The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper). The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).


![VS17 view of Create.cshtml page](page/_static/th.png)
-->
