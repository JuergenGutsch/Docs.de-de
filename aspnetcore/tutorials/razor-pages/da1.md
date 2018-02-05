---
title: Aktualisieren der generierten Seiten
author: rick-anderson
description: Aktualisieren der generierten Seiten mit besserer Anzeige.
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/da1
ms.openlocfilehash: abf6839536150f29eaa2d07dafbe0d0c1a08e280
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
# <a name="updating-the-generated-pages"></a>Aktualisieren der generierten Seiten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Für den Anfang ist die Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen. Wir wollen die Uhrzeit nicht sehen (12:00:00 Uhr in der nachstehenden Abbildung), und **ReleaseDate** als soll **Release Date** (zwei Wörter) angezeigt werden.

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualisieren des generierten Codes

Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]

Klicken Sie mit der rechten Maustaste auf eine rote Wellenlinie > **Schnellaktionen und Refactorings**.

  ![Kontextmenü mit **> Schnellaktionen und Refactorings**.](da1/qa.png)

Wählen Sie `using System.ComponentModel.DataAnnotations;` aus.

  ![„using System.ComponentModel.DataAnnotations“ oben in der Liste](da1/da.png)

  Visual Studio fügt `using System.ComponentModel.DataAnnotations;` hinzu.

Wir behandeln [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) im nächsten Tutorial. Das [Display](https://docs.microsoft.com//aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata)-Attribut gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ anstatt „ReleaseDate“). Das [DataType](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (Datum) an, damit die im Feld gespeicherten Informationen zur Uhrzeit nicht angezeigt werden.

Navigieren Sie zu „Pages/Movies“, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der Link-URL „http://localhost:1234/Movies/Edit/5“](da1/edit7.png)

Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.

[!code-cshtml[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien. Im vorangehenden Code generiert das `AnchorTagHelper` dynamisch den Wert des HTML-Attributs `href` auf der Razor-Page (die Route ist relativ), das `asp-page`-Element und die Routen-ID (`asp-route-id`). Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:mvc/razor-pages/index#url-generation-for-pages).

Rufen Sie in Ihrem bevorzugten Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen. Ein Teil des generierten HTML-Codes wird unten gezeigt:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge (z B. `http://localhost:5000/Movies/Details?id=2`). 

Aktualisieren Sie die Razor-Pages „Edit“, „Details“ und „Delete“ so, dass die Routenvorlage „{id:int}“ verwendet wird. Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`. Führen Sie die App aus, und zeigen Sie dann den Quelltext an. Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Eine Anforderung an die Seite mit der Routenvorlage „{id:int}“, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück. `http://localhost:5000/Movies/Details` gibt beispielsweise den Fehler 404 zurück. Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:

 ```cshtml
@page "{id:int?}"
```

### <a name="update-concurrency-exception-handling"></a>Aktualisieren der Behandlung von Ausnahmen bei vollständiger Parallelität

Aktualisieren Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*. Im folgenden markierten Code sind alle Änderungen dargestellt:

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit.cshtml.cs?name=snippet1&highlight=16-23)]

Der vorherige Code erkennt nur Ausnahmen bei vollständiger Parallelität, wenn der erste gleichzeitige Client den Film löscht und der zweite gleichzeitige Client Änderungen am Film vornimmt.

So testen Sie den Block `catch`

* Legen Sie einen Haltepunkt auf `catch (DbUpdateConcurrencyException)` fest.
* Bearbeiten Sie einen Film.
* Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.
* Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.

Code in Produktionsumgebungen erkennt normalerweise Parallelitätskonflikte, wenn zwei oder mehrere Clients einen Datensatz gleichzeitig aktualisiert haben. Weitere Informationen finden Sie unter [Behandlung von Parallelitätskonflikten](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Überprüfen der Bereitstellung und Bindung

Untersuchen Sie die Datei *Pages/Movies/Edit.cshtml.cs*: [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit.cshtml.cs?name=snippet2)]

Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z.B. `http://localhost:5000/Movies/Edit/2`):

* Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück. 
* Die `Page`-Methode rendert die Razor-Page *Pages/Movies/Edit.cshtml*. Die Datei *Pages/Movies/Edit.cshtml* enthält die Modelldirektive (`@model RazorPagesMovie.Pages.Movies.EditModel`), die das Filmmodell auf der Seite verfügbar macht.
* Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.

Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:

* Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden. Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden), wird das Formular mit den übermittelten Werten erneut bereitgestellt.
* Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.

Die HTTP GET-Methoden auf den Razor-Pages „Index“, „Create“ und „Delete“ folgen einem ähnlichen Muster. Die HTTP-POST-Methode `OnPostAsync` auf der Razor-Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor-Page „Edit“.

Die Suche wird im nächsten Tutorial hinzugefügt.

>[!div class="step-by-step"]
[Zurück: Arbeiten mit SQL Server LocalDB](xref:tutorials/razor-pages/sql)
[Hinzufügen der Suche](xref:tutorials/razor-pages/search)
