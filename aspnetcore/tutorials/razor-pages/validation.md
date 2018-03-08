---
title: "Hinzufügen der Validierung"
author: rick-anderson
description: "Gewusst wie: Hinzufügen der Validierung zu einer Racor-Page"
keywords: ASP.NET Core, Validierung, DataAnnotations, Razor, Racor-Pages
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 302e3077e8cf1cc3b145fcb4ba2ff677023d1524
ms.sourcegitcommit: c9658c0db446f7cb2e443f62b00cf773bed545fa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2017
---
# <a name="adding-validation-to-a-razor-page"></a>Hinzufügen der Validierung zu einer Razor-Page

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Abschnitt wird dem Modell `Movie` Validierungslogik hinzugefügt. Die Validierungsregeln werden erzwungen, wenn ein Benutzer einen Film erstellt oder bearbeitet.

## <a name="validation"></a>Validierung

Ein wesentlicher Grundsatz der Softwareentwicklung heißt [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (**D**on't **R**epeat **Y**ourself, dt. Wiederholen Sie sich nicht). Razor-Pages sind für Entwicklungsaufgaben gedacht, bei denen Funktionalität einmal angegeben und in der gesamten App übernommen wird. Der DRY-Ansatz kann die Codemenge in einer App reduzieren. Durch diesen Ansatz wird Code weniger fehleranfällig und lässt sich leichter testen und verwalten.

Die von Razor-Pages und Entity Framework gebotene Unterstützung der Validierung ist ein gutes Beispiel des DRY-Prinzips. Validierungsregeln werden an zentraler Stelle (in der Modellklasse) deklarativ angegeben und überall in der App erzwungen.

### <a name="adding-validation-rules-to-the-movie-model"></a>Hinzufügen von Validierungsregeln zum Modell „Movie“

Öffnen Sie Datei *Movie.cs*. [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) bietet eine integrierte Gruppe von Validierungsattributen, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden. „DataAnnotations“ enthält auch Formatierungsattribute wie `DataType`, die bei der Formatierung helfen und keine Validierung bieten.

Aktualisieren Sie die `Movie`-Klasse, um die Validierungsattribute `Required`, `StringLength`, `RegularExpression` und `Range` zu nutzen.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

Validierungsattribute geben das Verhalten an, das für Modelleigenschaften erzwungen wird:

* Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft über einen Wert verfügen muss. Nichts hindert Benutzer allerdings daran, Leerzeichen einzugeben, um die Validierungseinschränkung für einen Nullable-Typ zu umgehen. [Werttypen](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/value-types), bei denen es sich nicht um Nullable-Typen handelt (wie z.B. `decimal`, `int`, `float` und `DateTime`), sind grundsätzlich erforderlich und benötigen das Attribut `Required` nicht.
* Das Attribut `RegularExpression` schränkt die Zeichen ein, die ein Benutzer eingeben kann. Im oben stehenden Code sind für `Genre` und `Rating` nur Buchstaben erlaubt (Leerzeichen, Ziffern und Sonderzeichen sind nicht zulässig).
* Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.
* Das Attribut `StringLength` legt die Höchstlänge einer Zeichenfolge und optional die Mindestlänge fest. 

Dadurch, dass Validierungsregeln von ASP.NET Core automatisch erzwungen werden, wird eine App stabiler. Die automatische Validierung für Modelle hilft beim Schutz der App, da Sie nicht daran denken müssen, diesen anzuwenden, sobald neuer Code hinzugefügt wird.

### <a name="validation-error-ui-in-razor-pages"></a>Benutzeroberflächenoption für Validierungsfehler in Razor-Pages

Führen Sie die App aus, und navigieren Sie zu „Pages/Movies“.

Klicken Sie auf den Link **Neu erstellen**. Füllen Sie das Formular mit einigen ungültigen Werten aus. Wenn die clientseitige jQuery-Validierung den Fehler erkennt, wird eine Fehlermeldung angezeigt.

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](validation/_static/val.png)

> [!NOTE]
> Sie können ggf. in das Feld `Price` keine Dezimaltrennzeichen oder Kommas eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) in nicht englischen Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englischen Datums- und Uhrzeitformaten müssen Sie Schritte zur Globalisierung Ihrer App ausführen. Weitere Informationen finden Sie unter [Zusätzliche Ressourcen](#additional-resources). Geben Sie einstweilen ganze Zahlen wie 10 ein.

Wie Sie sehen, hat das Formular in allen Feldern mit einem ungültigen Wert automatisch eine Validierungsfehlermeldung angezeigt. Die Fehlermeldungen werden sowohl auf Clientseite (mithilfe von JavaScript und jQuery) als auch auf Serverseite erzwungen (wenn ein Benutzer JavaScript deaktiviert hat).

Ein entscheidender Vorteil ist, dass **keine** Codeänderungen auf den Seiten „Erstellen“ oder „Bearbeiten“ erforderlich waren. Nach Anwenden von „DataAnnotations“ auf das Modell wurde die Benutzeroberflächenvalidierung aktiviert. Die in diesem Tutorial erstellten Razor-Pages haben die Validierungsregeln automatisch übernommen (mithilfe der Validierungsattribute für die Eigenschaften der Modellklasse `Movie`). Testen Sie die Validierung mithilfe der Seite „Bearbeiten“. Es erfolgt dieselbe Validierung.

Die Formulardaten werden erst an den Server zurückgesendet, wenn auf Clientseite keine Validierungsfehler auftreten. Überprüfen Sie mithilfe von mindestens einem der folgenden Ansätze, ob keine Formulardaten bereitgestellt werden:

* Setzen Sie einen Haltepunkt in der `OnPostAsync`-Methode. Senden Sie das Formular (wählen Sie **Erstellen** oder **Speichern**). Der Haltepunkt wird niemals erreicht.
* Verwenden Sie das [Tool Fiddler](http://www.telerik.com/fiddler).
* Verwenden Sie die Browserentwicklungstools zum Überwachen des Netzwerkdatenverkehrs.

### <a name="server-side-validation"></a>Serverseitige Validierung

Wenn JavaScript im Browser deaktiviert ist, erfolgt beim Senden des Formulars mit Fehlern eine Bereitstellung auf dem Server.

Testen Sie optional die serverseitige Validierung:

* Deaktivieren Sie JavaScript im Browser. Wenn Sie JavaScript im Browser nicht deaktivieren können, probieren Sie einen anderen Browser.
* Setzen Sie auf der Seite „Erstellen“ oder „Bearbeiten“ in der `OnPostAsync`-Methode einen Haltepunkt.
* Senden Sie ein Formular mit Validierungsfehlern.
* Überprüfen Sie, ob der Modellstatus ungültig ist:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

Der folgende Code zeigt einen Teil der Seite *Create.cshtml*, deren Gerüst Sie zuvor im Tutorial erstellt haben. Sie wird von den Seiten „Erstellen“ und „Bearbeiten“ zum Anzeigen des anfänglichen Formulars und zum erneuten Anzeigen des Formulars bei einem Fehler verwendet.

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) verwendet die Attribute von [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind. Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zeigt Validierungsfehler. Weitere Informationen finden Sie unter [Validierung](xref:mvc/models/validation).

Die Seiten „Erstellen“ und „Bearbeiten“ weisen keine Validierungsregeln auf. Die Validierungsregeln und Fehlerzeichenfolgen werden nur in der `Movie`-Klasse angegeben. Diese Validierungsregeln gelten automatisch für Razor-Pages, die das Modell `Movie` bearbeiten.

Wenn Validierungslogik geändert werden muss, erfolgt dies nur im Modell. Die Validierung erfolgt in der gesamten Anwendung einheitlich (Validierungslogik ist zentral definiert). Die zentrale Validierung unterstützt sauberen Code und erleichtert dessen Verwaltung und Aktualisierung.

## <a name="using-datatype-attributes"></a>Verwenden von „DataType“-Attributen

Untersuchen Sie die Klasse `Movie`. Der Namespace `System.ComponentModel.DataAnnotations` stellt zusätzlich zu der integrierten Gruppe von Validierungsattributen Formatierungsattribute bereit. Das `DataType`-Attribut wird nur auf die Eigenschaften `ReleaseDate` und `Price` angewendet.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Die `DataType`-Attribute geben dem Anzeigemodul nur Hinweise zum Formatieren der Daten (und liefern Attribute wie `<a>` für URLs und `<a href="mailto:EmailAddress.com">` für E-Mail). Verwenden Sie das `RegularExpression`-Attribut, um das Format der Daten zu validieren. Das `DataType`-Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist. `DataType`-Attribute sind keine Validierungsattribute. In der Beispielanwendung wird nur das Datum ohne Uhrzeit angezeigt.

Die `DataType`-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“ usw. Das `DataType`-Attribut kann der Anwendung auch ermöglichen, typspezifische Features bereitzustellen. Ein Link des Typs `mailto:` kann beispielsweise für `DataType.EmailAddress` erstellt werden. In Browsern mit HTML5-Unterstützung kann für `DataType.Date` eine Datumsauswahl bereitgestellt werden. Die `DataType`-Attribute geben `data-`-Attribute (ausgesprochen „Datadash“) von HTML5 aus, die HTML5-Browser nutzen. Die `DataType`-Attribute bieten **keine** Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf `CultureInfo` des Servers angezeigt.

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Die Einstellung `ApplyFormatInEditMode` gibt an, dass die Formatierung angewendet werden soll, wenn der Wert zur Bearbeitung angezeigt wird. Dieses Verhalten ist für einige Felder ggf. nicht wünschenswert. Beispielsweise sollte bei Währungswerten das Währungssymbol auf der Benutzeroberfläche für die Bearbeitung nicht angezeigt werden.

Das `DisplayFormat`-Attribut kann eigenständig verwendet werden, doch meist empfiehlt es sich, das `DataType`-Attribut zu verwenden. Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm. Es bietet die folgenden Vorteile, die „DisplayFormat“ nicht bietet:

* Der Browser kann HTML5-Features aktivieren (z.B. zum Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links usw.).
* Standardmäßig rendert der Browser Daten mit dem ordnungsgemäßen auf Ihrem Gebietsschema basierenden Format.
* Das `DataType`-Attribut kann dem ASP.NET Core-Framework ermöglichen, die richtige Feldvorlage zum Rendern der Daten auszuwählen. `DisplayFormat`, falls eigenständig genutzt, verwendet die Zeichenfolgenvorlage.

Hinweis: Die jQuery-Validierung funktioniert nicht mit den Attributen `Range` und `DateTime`. Bei folgendem Code wird z.B. stets ein clientseitiger Validierungsfehler angezeigt, auch wenn sich das Datum im angegebenen Bereich befindet:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Es wird allgemein nicht empfohlen, feste Datumsangaben in Ihren Modellen zu kompilieren, weshalb vom Einsatz des `Range`-Attributs und von `DateTime` abgeraten wird.

Der folgende Code zeigt die Kombination von Attributen in einer Zeile:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

### <a name="publish-to-azure"></a>Veröffentlichen in Azure

Anweisungen zum Veröffentlichen dieser App in Azure finden Sie unter [Veröffentlichen einer ASP.NET Core-Web-App in Azure App Service mit Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Arbeiten mit Formularen](xref:mvc/views/working-with-forms)
* [Globalisierung und Lokalisierung](xref:fundamentals/localization)
* [Einführung in Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)
* [Erstellen von Taghilfsprogrammen](xref:mvc/views/tag-helpers/authoring)

>[!div class="step-by-step"]
[Zurück: Hinzufügen eines neuen Feldes](xref:tutorials/razor-pages/new-field)
[Weiter: Hochladen von Dateien](xref:tutorials/razor-pages/uploading-files)
