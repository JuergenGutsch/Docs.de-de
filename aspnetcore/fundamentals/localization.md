---
title: Globalisierung und Lokalisierung in ASP.NET Core
author: rick-anderson
description: "Erfahren Sie, wie ASP.NET Core-Dienste und Middleware bereitstellt, für die Lokalisierung von Inhalten in andere Sprachen und Kulturen."
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/localization
ms.openlocfilehash: 1922037245a33f49c17f1c361003260462d96264
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalisierung und Lokalisierung in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Michael Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://twitter.com/NadeemAfana), und [Hisham "bin" Ateya](https://twitter.com/hishambinateya)

Erstellen einer mehrsprachigen Websites mit ASP.NET Core können Ihre Website in einem breiteren Publikum zukommen lassen zu erreichen. ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.

Internationalisierung umfasst [Globalisierung](https://docs.microsoft.com/dotnet/api/system.globalization) und [Lokalisierung](https://docs.microsoft.com/dotnet/standard/globalization-localization/localization). Globalisierung versteht man das Entwerfen von apps, die verschiedene Kulturen unterstützen. Globalisierung fügt Unterstützung für die Eingabe, die Anzeige und die Ausgabe des einen definierten Satz von Sprachskripts, die sich auf bestimmte geografische Bereiche beziehen.

Lokalisierung ist der Prozess der Anpassung einer globalisierten Apps vorgestellt, die Sie bereits für Lokalisierbarkeit, um eine bestimmte Kultur bzw. das angegebene Gebietsschema verarbeitet haben. Weitere Informationen finden Sie unter **Globalisierung und Lokalisierung Begriffe** kurz vor dem Ende dieses Dokuments.

App Lokalisierung umfasst die folgenden Schritte:

1. Stellen Sie die app-Inhalte lokalisierbare

2. Geben Sie die lokalisierte Ressourcen für die Sprachen und Kulturen, die Sie unterstützen

3. Implementieren Sie eine Strategie zum Auswählen der Sprache/Kultur für jede Anforderung

## <a name="make-the-apps-content-localizable"></a>Stellen Sie die app-Inhalte lokalisierbare

Eingeführt in ASP.NET Core, `IStringLocalizer` und `IStringLocalizer<T>` wurden entworfen, um die Produktivität beim Entwickeln von apps lokalisiert. `IStringLocalizer`verwendet die [ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager) und [ResourceReader](https://docs.microsoft.com/dotnet/api/system.resources.resourcereader) auf kulturspezifische Ressourcen zur Laufzeit bereitzustellen. Die einfache Schnittstelle verfügt über einen Indexer und ein `IEnumerable` für lokalisierte Zeichenfolgen zurückgeben. `IStringLocalizer`nicht müssen standardmäßige sprachenzeichenfolgen in einer Ressourcendatei speichern. Sie können nicht früh in der Entwicklung Ressourcendateien erstellen müssen und entwickeln eine app, die für die Lokalisierung vorgesehen. Der folgende Code zeigt, wie die Zeichenfolge "Zu Title" für die Lokalisierung zu umschließen.

[!code-csharp[Main](localization/sample/Localization/Controllers/AboutController.cs)]

Im obigen Code die `IStringLocalizer<T>` Implementierung ergibt sich aus der [Abhängigkeitsinjektion](dependency-injection.md). Wenn der lokalisierte Wert von "Zu Title" nicht gefunden wird, der Indexerschlüssel zurückgegeben wird, d. h. die Zeichenfolge "Zu Title". Sie können die Standardeinstellung Sprache Literalzeichenfolgen in der app und umschließen sie den Lokalisierungsexperten, damit Sie sich bei der Entwicklung der app konzentrieren können. Sie entwickeln von Apps mit der Standardsprache und Schritt der Lokalisierung vorbereiten, ohne zunächst eine Standarddatei für die Ressource erstellt. Alternativ können Sie den herkömmlichen Ansatz verwenden, und geben Sie einen Schlüssel zum Abrufen der Zeichenfolge der Standardsprache. Für viele Entwickler den neuen Workflow, der ohne einer Standardsprache *resx* Datei sowie das Umbrechen von einfach die Zeichenfolgenliterale können die gering Lokalisierungsprozess einer app. Andere Entwickler bevorzugt den herkömmlichen Ablauf wie vereinfachen können zum Arbeiten mit länger Zeichenfolgenliterale und erleichtern die lokalisierte Zeichenfolgen zu aktualisieren.

Verwenden der `IHtmlLocalizer<T>` Implementierung für Ressourcen, die HTML enthalten. `IHtmlLocalizer`HTML-codiert Argumente, die in der Ressourcenzeichenfolge formatiert werden, jedoch wird keine HTML-codiert die Ressourcenzeichenfolge selbst darstellt. Im Beispiel für den hervorgehobenen, nur des Wert des `name` Parameter ist HTML-codiert.

[!code-csharp[Main](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Hinweis:** im Allgemeinen nur Text und nicht HTML lokalisiert werden soll.

Sie können auf der untersten Ebene abrufen `IStringLocalizerFactory` von [Abhängigkeitsinjektion](dependency-injection.md):

[!code-csharp[Main](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Der obige Code veranschaulicht die zwei Factory Methoden erstellen.

Sie können partitionieren Ihrer lokalisierten Zeichenfolgen vom Netzwerkcontroller, Bereich, oder besitzen nur ein Container. In der Beispiel-app eine dummy-Klasse mit dem Namen `SharedResource` für freigegebene Ressourcen verwendet wird.

[!code-csharp[Main](localization/sample/Localization/Resources/SharedResource.cs)]

Einige Entwickler verwenden die `Startup` Klasse, um die globale oder freigegebene Zeichenfolgen enthalten. Im folgenden Beispiel wird die `InfoController` und `SharedResource` Lokalisierungsexperten verwendet werden:

[!code-csharp[Main](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Ansicht Lokalisierung

Die `IViewLocalizer` Service bietet lokalisierte Zeichenfolgen für eine [Ansicht](https://docs.microsoft.com/aspnet/core). Die `ViewLocalizer` -Klasse implementiert diese Schnittstelle und den Speicherort der Ressource an, aus dem Dateipfad Sicht sucht. Der folgende Code zeigt, wie Sie die standardmäßige Implementierung des `IViewLocalizer`:

[!code-cshtml[Main](localization/sample/Localization/Views/Home/About.cshtml)]

Die standardmäßige Implementierung des `IViewLocalizer` die Ressourcendatei, die basierend auf den Dateinamen für die Sicht sucht. Es gibt keine Option, um eine global freigegebener Ressourcendatei verwendet. `ViewLocalizer`implementiert den Lokalisierungsexperten verwenden `IHtmlLocalizer`, sodass Razor HTML nicht die lokalisierte Zeichenfolge zu codieren. Sie können Ressourcenzeichenfolgen parametrisieren und `IViewLocalizer` wird HTML-Codierung, die Parameter, aber nicht die Ressourcenzeichenfolge. Betrachten Sie das folgende Markup für den Razor aus:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Eine französische Ressourcendatei kann Folgendes enthalten:

| Key | Wert |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b> ` |

Der gerenderte Ansicht würde das HTML-Markup aus der Ressourcendatei enthalten.

**Hinweis:** im Allgemeinen nur Text und nicht HTML lokalisiert werden soll.

Für die Verwendung eine freigegebenen Ressource-Datei in einer Sicht einfügen `IHtmlLocalizer<T>`:

[!code-cshtml[Main](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>DataAnnotations-Lokalisierung

DataAnnotations-Fehlermeldungen mit lokalisiert werden `IStringLocalizer<T>`. Mithilfe der Option `ResourcesPath = "Resources"`, die Fehlermeldungen `RegisterViewModel` können in einem der folgenden Pfade gespeichert werden:

* Resources/ViewModels.Account.RegisterViewModel.fr.resx
* Resources/ViewModels/Account/RegisterViewModel.fr.resx

[!code-csharp[Main](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

In ASP.NET-MVC 1.1.0 Core und höher, nicht-Validation-Attribute sind lokalisiert. Wird von ASP.NET Core MVC 1,0 **nicht** suchen Sie die lokalisierten Zeichenfolgen für nicht-Validierungsattribute.

<a name="one-resource-string-multiple-classes"></a>
### <a name="using-one-resource-string-for-multiple-classes"></a>Verwenden eine Ressourcenzeichenfolge für mehrere Klassen

Der folgende Code zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

In der vorangehende Code `SharedResource` ist die Klasse, die Resx entspricht, in dem die validierungsmeldungen gespeichert sind. Bei diesem Ansatz verwenden DataAnnotations nur `SharedResource`, anstatt die Ressource für jede Klasse. 

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Geben Sie die lokalisierte Ressourcen für die Sprachen und Kulturen, die Sie unterstützen  

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures und SupportedUICultures

ASP.NET Core ermöglicht Ihnen die Angabe von zwei Werte für die Kultur, `SupportedCultures` und `SupportedUICultures`. Die [CultureInfo](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo) -Objekt für `SupportedCultures` bestimmt die Ergebnisse der Kultur abhängigen Funktionen, z. B. Datum, Uhrzeit, Zahl und Währung formatieren. `SupportedCultures`bestimmt auch die Sortierreihenfolge der Text, Groß-/ Kleinschreibungskonventionen und Zeichenfolgenvergleiche. Finden Sie unter [CultureInfo.CurrentCulture](https://docs.microsoft.com/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) Weitere Informationen zur wie der Server für die Kultur abruft. Die `SupportedUICultures` bestimmt die Zeichenfolgen übersetzt (aus *resx* Dateien) gesucht werden, durch die [ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager). Die `ResourceManager` einfach kulturspezifische Zeichenfolgen, die nach richtet sucht `CurrentUICulture`. Jeder Thread in .NET hat `CurrentCulture` und `CurrentUICulture` Objekte. ASP.NET Core überprüft diese Werte beim Rendern der Kultur abhängigen Funktionen. Angenommen, wenn die aktuelle Threadkultur auf "En-US" (Englisch, USA) festgelegt ist `DateTime.Now.ToLongDateString()` "Donnerstag, 18 Februar 2016, an" angezeigt, aber wenn `CurrentCulture` festgelegt ist, "es-ES" (Spanisch, Spanien) für die Ausgabe soll "Jueves, 18 de Febrero de 2016".

## <a name="resource-files"></a>Ressourcendateien

Eine Ressourcendatei handelt es sich um einen Mechanismus zur Trennung von lokalisierbarer Zeichenfolgen aus Code. Übersetzte Zeichenfolgen für die Standardsprache sind isoliert *resx* Ressourcendateien. Möglicherweise möchten z. B. Spanisch Ressourcendatei namens erstellen *Welcome.es.resx* mit Zeichenfolgen übersetzt. ""es endenden"ist der Sprachcode für Spanisch. So erstellen Sie diese Ressourcendatei in Visual Studio

1. In **Projektmappen-Explorer**, klicken Sie mit der rechten Maustaste auf den Ordner bzw. die die Ressourcendatei enthält > **hinzufügen** > **neues Element**.

    ![Geschachtelte Kontextmenü: im Projektmappen-Explorer ein Kontextmenü für die Ressourcen geöffnet ist. Eine zweite Kontextmenü ist geöffnet zum Hinzufügen, die mit dem Befehl neue Element hervorgehoben.](localization/_static/newi.png)

2. In der **nach installierten Vorlagen suchen** Feld, geben Sie "Ressource" aus, und nennen Sie die Datei.

    ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

3. Geben Sie den Schlüsselwert (systemeigenen Zeichenfolge) in der **Namen** Spalte und die übersetzte Zeichenfolge in der **Wert** Spalte.

    ![Welcome.es.resx-Datei (die Willkommensseite Ressourcendatei für Spanisch) mit dem Wort "Hello", in der Spalte Name und das Wort Hola (Hello in Spanisch) in der Spalte Wert](localization/_static/hola.png)

    Visual Studio zeigt die *Welcome.es.resx* Datei.

    ![Projektmappen-Explorer mit der Ressourcendatei Willkommen Spanisch (es)](localization/_static/se.png)

<a name="error"></a>

Wenn Sie Visual Studio 2017 Preview-Version 15.3 verwenden, erhalten Sie einen Fehlerindikator im Ressourcen-Editor. Entfernen Sie die *ResXFileCodeGenerator* Wert aus der *benutzerdefiniertes Tool* Eigenschaftenraster, um zu verhindern, dass diese Fehlermeldung wird ausgegeben:

![RESX-editor](localization/_static/err.png)

Alternativ können Sie diesen Fehler ignorieren. Wir hoffen, diese in der nächsten Version behoben.

## <a name="resource-file-naming"></a>Ressource Dateibenennung

Ressourcen sind für den vollständigen Typnamen, der ihre Klasse minus der Name der Assembly mit dem Namen. Z. B. eine französische Ressource in einem Projekt, dessen Hauptassembly `LocalizationWebsite.Web.dll` für die Klasse `LocalizationWebsite.Web.Startup` Namen *Startup.fr.resx*. Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` Namen *Controllers.HomeController.fr.resx*. Wenn Ihre Zielklasse Namespace nicht als Name der Assembly identisch ist, benötigen Sie den vollständigen Typnamen. Beispielsweise in der Stichprobe Projekt eine Ressource für den Typ `ExtraNamespace.Tools` Namen *ExtraNamespace.Tools.fr.resx*.

Im Beispielprojekt das `ConfigureServices` Methode legt die `ResourcesPath` "Resources", also den relativen Pfad des Projekts für den home-Controller französischen Ressourcendatei ist *Resources/Controllers.HomeController.fr.resx*. Alternativ können Sie Ordner verwenden, um Ressourcendateien zu organisieren. Für den home-Controller, wäre der Pfad *Resources/Controllers/HomeController.fr.resx*. Wenn Sie nicht verwenden die `ResourcesPath` -Option der *resx* Datei im Projektverzeichnis Basis gehen würde. Die Ressourcendatei für `HomeController` Namen *Controllers.HomeController.fr.resx*. Die Wahl der Verwendung der Benennungskonvention Punkt oder ein Pfad, hängt davon ab, wie Sie die Ressourcendateien organisieren möchten.

| Ressourcenname | Punkt oder ein Pfad zu benennen |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Punkt  |
| Resources/Controllers/HomeController.fr.resx  | Pfad |
|    |     |

Ressourcendateien, die mit `@inject IViewLocalizer` in Razor-Ansichten folgen ein ähnlichen Muster. Die Ressourcendatei für eine Sicht kann mit Punkt benennen oder Pfad naming benannt werden. Ressourcendateien für Razor-Ansicht imitieren, die den Pfad der deren zugeordnete Ansicht-Datei. Angenommen, wir setzen die `ResourcesPath` auf "Ressourcen" die französischen Ressourcendatei zugeordneten der *Views/Home/About.cshtml* Ansicht möglicherweise eine der folgenden:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Wenn Sie nicht verwenden die `ResourcesPath` -Option der *resx* -Konfigurationsdatei für eine Sicht im gleichen Ordner wie die Sicht befinden würde.

## <a name="culture-fallback-behavior"></a>Kultur fallbackverhaltens

Als Beispiel wenn die Kultur auf Französisch festgelegt haben, und Sie die Kultur ".de"-Kennzeichner entfernen, die Standardressourcendatei gelesen und Zeichenfolgen lokalisiert werden. Der Ressourcen-Manager kennzeichnet eine Standardinstanz oder eine Fallbackressource für, wenn keine die angeforderte Kultur entspricht. Wenn den Schlüssel nur zurückgegeben, wenn eine Ressource für die angeforderte Kultur fehlt eine Standarddatei für die Ressource nicht benötigen werden soll.

### <a name="generate-resource-files-with-visual-studio"></a>Generieren von Ressourcendateien mit Visual Studio

Wenn Sie eine Ressourcendatei in Visual Studio, ohne eine Kultur im Dateinamen erstellen (z. B. *Welcome.resx*), Visual Studio erstellt eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge. Das ist in der Regel nicht mit ASP.NET Core wollten; Sie haben in der Regel keinen Standardwert *resx* Ressourcendatei (ein *resx* Datei ohne den Namen der Kultur). Empfohlen, die Sie erstellen die *resx* Datei mit einem Kulturnamen (z. B. *Welcome.fr.resx*). Beim Erstellen einer *resx* Datei mit einem Kulturnamen, Visual Studio generiert keinen die Klassendatei. Wir erwarten, dass viele Entwickler **nicht** erstellen Sie eine Ressourcendatei der Standardsprache.

### <a name="add-other-cultures"></a>Hinzufügen von anderen Kulturen

Jede Kombination aus Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei. Sie erstellen Ressourcendateien für unterschiedliche Kulturen und Gebietsschemas durch Erstellen von neuen Ressourcendateien, die in der ISO-Sprachcodes Teil des Dateinamens sind (z. B. **En-us**, **fr-ca**, und  **En-gb-**). Diese Codes ISO befinden sich zwischen den Dateinamen und die *resx* Dateinamenerweiterung, wie in *Welcome.es-MX.resx* (Spanisch/Mexiko). Um eine für neutrale Sprache angeben, entfernen Sie die Landeskennzahl (`MX` im vorherigen Beispiel). Ist das kulturübergreifende spanische Ressourcendateinamen *Welcome.es.resx*.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementieren Sie eine Strategie zum Auswählen der Sprache/Kultur für jede Anforderung  

### <a name="configure-localization"></a>Konfigurieren Sie die Lokalisierung

Lokalisierung ist so konfiguriert, der `ConfigureServices` Methode:

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet1)]

* `AddLocalization`Der Container für hinzugefügt die Lokalisierung Dienste. Der Code oben wird auch den Ressourcen-Pfad zu "Resources" festgelegt.

* `AddViewLocalization`Bietet Unterstützung für lokalisierte Anzeigen von Dateien. In dieser beispielsicht Lokalisierung der Dateisuffixes Ansicht basiert. Z. B. "fr" in der *Index.fr.cshtml* Datei.

* `AddDataAnnotationsLocalization`Fügt Unterstützung für lokalisierte `DataAnnotations` Überprüfung Meldungen über `IStringLocalizer` Abstraktionen.

### <a name="localization-middleware"></a>Lokalisierung middleware

Die aktuelle Kultur auf eine Anforderung festgelegt ist, die die Lokalisierung [Middleware](middleware.md). Die Middleware für die Lokalisierung aktiviert ist, der `Configure` Methode *"Program.cs"* Datei. Beachten Sie, dass die Lokalisierung Middleware muss konfiguriert werden, bevor die Middleware, die die Kultur für die Anforderung prüfen, ob möglicherweise (z. B. `app.UseMvcWithDefaultRoute()`).

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet2)]

`UseRequestLocalization`Initialisiert ein `RequestLocalizationOptions` Objekt. Bei jeder Anforderung der Liste der `RequestCultureProvider` in der `RequestLocalizationOptions` aufgelistet und verwendet der ersten Anbieter, die erfolgreich die Anforderung Kultur bestimmen kann. Der Standardanbieter stammen aus den `RequestLocalizationOptions` Klasse:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Die Standardliste wechselt von der spezifischsten spezifischen. Später in diesem Artikel sehen wir, wie Sie die Reihenfolge ändern und sogar einen benutzerdefinierten Kultur Anbieter hinzufügen. Wenn keine der Anbieter die Anforderung Kultur bestimmen kann die `DefaultRequestCulture` verwendet wird.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Einige apps verwendet eine Abfragezeichenfolge zum Festlegen der [Kultur und Benutzeroberflächenkultur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Für apps, die Cookie oder Accept-Language-Header-Methode verwenden, ist der URL eine Abfragezeichenfolge hinzugefügt nützlich zum Debuggen und Testen von Code. Wird standardmäßig die `QueryStringRequestCultureProvider` registriert ist, als der erste Lokalisierungsanbieter in der `RequestCultureProvider` Liste. Übergeben Sie die Abfrage Zeichenfolgenparametern `culture` und `ui-culture`. Im folgenden Beispiel wird die spezifische Kultur (Sprache und Region), Spanisch/Mexiko an:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Wenn Sie nur einen der beiden übergeben (`culture` oder `ui-culture`), der Zeichenfolge Abfrageanbieter wird legen Sie beide Werte, die mit der Sie übergeben. Z. B. nur die Kultur festlegen wird legen Sie sowohl die `Culture` und `UICulture`:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

Produktion Anwendungen bieten einen Mechanismus zum Festlegen der Kultur mit dem ASP.NET Core Kultur Cookie häufig. Verwenden der `MakeCookieValue` Methode, um ein Cookie zu erstellen.

Die `CookieRequestCultureProvider` `DefaultCookieName` gibt der standardcookienamen verwendet, um den Benutzer nachzuverfolgen bevorzugte kulturinformationen. Der Name des Cookies ist ". AspNetCore.Culture".

Das Cookie-Format ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` ist `Culture` und `uic` ist `UICulture`, beispielsweise:

    c=en-UK|uic=en-US

Wenn Sie nur kulturinformationen und Benutzeroberflächenkultur angeben, wird die angegebene Kultur Info Kultur und Benutzeroberflächenkultur verwendet werden.

### <a name="the-accept-language-http-header"></a>Der Accept-Language-HTTP-header

Die [Accept-Language-Headers](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern festgelegt werden und wurde ursprünglich beabsichtigt, um die Sprache des Benutzers anzugeben. Diese Einstellung gibt an, was den Browser senden festgelegt wurde, oder aus dem zugrunde liegenden Betriebssystem geerbt wurde. Der Accept-Language-HTTP-Header aus einer Browseranforderung ist eine ausfallsichere Möglichkeit zum Erkennen von bevorzugte Sprache des Benutzers nicht (siehe [Festlegen der bevorzugten Sprache in einem Browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Eine produktionsanwendung sollte eine Möglichkeit für einen Benutzer anpassen ihrer Wahl der Kultur enthalten.

### <a name="set-the-accept-language-http-header-in-ie"></a>Legen Sie den Accept-Language-HTTP-Header in IE

1. Tippen Sie in das Symbol "Zahnrad"-Symbols auf **Internetoptionen**.

2. Tippen Sie auf **Sprachen**.

    ![Internet-Optionen](localization/_static/lang.png)

3. Tippen Sie auf **Festlegen der bevorzugten Sprache**.

4. Tippen Sie auf **Sprache hinzufügen**.

5. Fügen Sie die Sprache hinzu.

6. Tippen Sie auf die Sprache, und tippen Sie dann **nach oben**.

### <a name="use-a-custom-provider"></a>Verwenden Sie einen benutzerdefinierten Anbieter

Angenommen Sie, Sie können Ihre Kunden ihre Sprache und Kultur in die angegebenen Datenbanken speichern möchten. Sie könnten einen Anbieter aus, um diese Werte für den Benutzer suchen schreiben. Der folgende Code zeigt, wie Sie einen benutzerdefinierten Anbieter hinzufügen:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Verwendung `RequestLocalizationOptions` hinzufügen oder Entfernen von Anbietern Lokalisierung.

### <a name="set-the-culture-programmatically"></a>Legen Sie die Kultur programmgesteuert

In diesem Beispiel **Localization.StarterWeb** Projekt auf [GitHub](https://github.com/aspnet/entropy) Versionen haben Benutzeroberflächenelemente zum Festlegen der `Culture`. Die *Views/Shared/_SelectLanguagePartial.cshtml* Datei können Sie die Kultur aus der Liste der unterstützten Kulturen auswählen:

[!code-cshtml[Main](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Die *Views/Shared/_SelectLanguagePartial.cshtml* Datei hinzugefügt wird, die `footer` Abschnitt der Layoutdatei, sodass er auf alle Sichten verfügbar sind:

[!code-cshtml[Main](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Die `SetLanguage` -Methode legt das Cookie Kultur.

[!code-csharp[Main](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Sie können nicht angeschlossen werden die *_SelectLanguagePartial.cshtml* Beispielcode für dieses Projekt. Die **Localization.StarterWeb** Projekt auf [GitHub](https://github.com/aspnet/entropy) hat Code fließen die `RequestLocalizationOptions` zu einer Razor teilweise über das [Abhängigkeitsinjektion](dependency-injection.md) Container.

## <a name="globalization-and-localization-terms"></a>Globalisierung und Lokalisierung Begriffe

Lokalisieren von Ihrer app muss auch ein grundlegendes Verständnis der entsprechenden Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet und einen Überblick über die Probleme, die ihnen zugeordneten. Obwohl alle Computer Text als Zahlen (Codes) speichern, Speichern verschiedener Systeme den gleichen Text, der mit anderen Zahlen. Der Lokalisierungsprozess bezieht sich auf die app-Benutzeroberfläche (UI) für eine bestimmte Kultur bzw. das angegebene Gebietsschema zu übersetzen.

[Lokalisierbarkeit](https://docs.microsoft.com/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt für überprüfen, ob eine globalisierte app für die Lokalisierung bereit ist.

Die [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format für den Namen der Kultur wird `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Teilkulturcode. Beispielsweise `es-CL` für Spanisch (Chile) `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination von ISO 639-Kleinbuchstaben zwei Buchstaben bestehende Kulturcode von einer anderen Sprache zugeordnet und ein ISO 3166 zweibuchstabige Großbuchstaben Teilkulturcode verknüpft mit einem Land oder Region sind. Finden Sie unter [Sprache Kulturname](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Internationalisierung ist häufig "I18N" abgekürzt. Die Abkürzung nimmt die ersten und letzten Buchstaben ein, und die Anzahl der Buchstaben zwischen ihnen also 18 steht für die Anzahl der zwischen dem ersten Buchstaben "I" und dem letzten "N". Das gleiche gilt für die Globalisierung (G11N) und Lokalisierung (L10N).

Begriffe:

* Globalisierung (G11N): Die Schritte zum Erstellen einer app, die unterschiedliche Sprachen und Regionen zu unterstützen.
* Lokalisierung (L10N): Der Prozess von einer app für eine bestimmte Sprache und Region anpassen.
* Internationalisierung (I18N): Beschreibt die Globalisierung und Lokalisierung.
* Kultur: Es ist eine Sprache und optional eine Region.
* Neutrale Kultur: eine Kultur, die einer bestimmten Sprache, aber nicht in einer Region aufweist. (z. B. "En", ""es endenden")
* Bestimmte Kultur: eine Kultur, die der angegebenen Sprache und Region verfügt. (z. B. "En-US" "En-GB", "es-CL")
* Übergeordnete Kultur: die neutrale Kultur, die eine bestimmte Kultur enthält. (z. B. "En" für die übergeordnete Kultur "En-US" und "En-GB" ist)
* Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Localization.StarterWeb Projekt](https://github.com/aspnet/entropy) im Artikel verwendet.
* [Ressourcendateien in Visual Studio](https://docs.microsoft.com/cpp/windows/resource-files-visual-studio)
* [Ressourcen in RESX-Dateien](https://docs.microsoft.com/dotnet/framework/resources/working-with-resx-files-programmatically)
