---
title: Bundling und Minimierung in ASP.NET Core
author: scottaddie
description: "Erfahren Sie, wie statische Ressourcen in einer ASP.NET Core-Web-Anwendung zu optimieren, indem Sie Bündelung und Minimierung Techniken anwenden."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2018
ms.devlang: csharp
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: client-side/bundling-and-minification
ms.openlocfilehash: 6c233d0957ce9974adbc6112e6194c072aab0b41
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="bundling-and-minification"></a>Bundling und Minimierung

Von [Scott Addie](https://twitter.com/Scott_Addie)

Dieser Artikel beschreibt die Vorteile der Anwendung Bündelung und Minimierung, z. B., wie diese Funktionen mit ASP.NET Core webapps verwendet werden können.

## <a name="what-is-bundling-and-minification"></a>Was ist Bündelung und Minimierung?

Bundling und Minimierung sind zwei unterschiedliche leistungsoptimierungen, die Sie in einer Web-app anwenden können. Zusammen verwendet werden, zur Leistungssteigerung Bündelung und Minimierung durch Reduzierung der Anzahl von serveranforderungen und Verringern der Größe der angeforderten statischen Objekte.

Bundling und Minimierung wird in erster Linie die erste Anforderung Seitenladezeit verbessern. Sobald eine Webseite angefordert wurde, speichert der Browser statische Objekte (JavaScript, CSS und Bilder). Folglich leistungsverbesserung nicht Bündelung und Minimierung beim Anfordern von der gleichen Seite oder Seiten am selben Standort die gleichen Ressourcen anfordern. Wenn der Ablauf Header ist nicht ordnungsgemäß festgelegt, auf die Ressourcen und Bündelung und Minimierung wird nicht verwendet, der Browser Aktualität Heuristik zu kennzeichnen, die Anlagen veraltete nach ein paar Tagen. Darüber hinaus muss der Browser eine validierungsanforderung für jedes Medienobjekt. In diesem Fall geben Bündelung und Minimierung verbessert die Leistung auch nach der ersten Seitenanforderung.

### <a name="bundling"></a>Bundling

Bundling kombiniert mehrere Dateien in einer einzelnen Datei. Bundling reduziert die Anzahl der serveranforderungen, die erforderlich sind, um eine Web-Ressource, z. B. eine Webseite rendern. Sie können eine beliebige Anzahl von einzelnen Pakete speziell für CSS, JavaScript usw. erstellen. Weniger Dateien bedeutet weniger HTTP-Anforderungen über den Browser an den Server oder aus dem Dienst, der die Anwendung bereitstellen. Ergibt sich aus diesem verbessert die Leistung beim ersten Seite.

### <a name="minification"></a>Minimierung

Minimierung entfernt überflüssige Zeichen aus Code ohne Funktionen zu ändern. Das Ergebnis ist eine wesentliche Verringerung angeforderten Objekte (z. B. CSS, Bilder und JavaScript-Dateien). Allgemeine Nebeneffekte der Minimierung gehören Verkürzung Variablennamen an, um ein Zeichen und Kommentare und unnötige Leerzeichen entfernt.

Betrachten Sie die folgenden JavaScript-Funktion:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minimierung reduziert die Funktion wie folgt:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Zusätzlich zu entfernen, Kommentare und Leerzeichen nicht erforderlich, wurden die folgenden Namen für Parameter und Variablen wie folgt umbenannt:

Ursprünglich | Umbenannt
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Auswirkungen der Bündelung und Minimierung

In der folgenden Tabelle sind die Unterschiede zwischen einzeln Bestand laden und Verwenden von Bündelung und Minimierung aufgeführt:

Aktion | Mit B/M | Ohne B/M | Änderung
--- | :---: | :---: | :---:
Dateianforderungen  | 7   | 18     | 157%
KB übertragen | 156 | 264.68 | 70%
Laden Sie die Zeit (ms) | 885 | 2360   | 167%

Browser sind ziemlich aufwändig im Hinblick auf HTTP-Anforderungsheader. Die Gesamtanzahl der Bytes zu eine erhebliche Reduzierung von Metrik gesehen haben, wenn Bündelung gesendet. Die Ladezeit zeigt eine erhebliche Verbesserung, jedoch in diesem Beispiel wird lokal ausgeführt wurde. Deutlichere leistungsverbesserungen werden genutzt, wenn Bündelung und Minimierung mit Ressourcen verwenden, die über ein Netzwerk übertragen.

## <a name="choose-a-bundling-and-minification-strategy"></a>Wählen Sie eine Strategie Bündelung und Minimierung

Die Razor-Pages und MVC-Projektvorlagen bieten eine Out-of-Box-Lösung für Bündelung und Minimierung bestehend aus einer JSON-Konfigurationsdatei an. Drittanbieter-tools, z. B. die [Gulp](xref:client-side/using-gulp) und [Grunt](xref:client-side/using-grunt) task Runner, Aufgaben mit etwas mehr Komplexität. Ein Tool eines Drittanbieters ist ideal, wenn der Entwicklungsworkflow Verarbeitung hinter Bündelung und Minimierung erfordert&mdash;z. B. Linting und Image-Optimierung. Verwenden Sie zur Entwurfszeit Bündelung und Minimierung, werden die verkleinerte Dateien vor der Bereitstellung der app erstellt. Bundling und Minimierung mit vor der Bereitstellung bietet den Vorteil des reduzierten Serverlast. Allerdings es ist wichtig zu wissen, während der Entwurfszeit Bündelung und Minimierung Build Komplexität erhöht und kann nur für statische Dateien.

## <a name="configure-bundling-and-minification"></a>Konfigurieren von Bündelung und Minimierung

Geben Sie die Razor-Pages und MVC-Projektvorlagen einen *bundleconfig.json* Konfigurationsdatei an, die die Optionen für jedes Paket definiert. Standardmäßig wird eine Paket-Konfiguration für die benutzerdefinierte JavaScript definiert (*wwwroot/js/site.js*) und Stylesheet (*wwwroot/css/site.css*) Dateien:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Konfigurationsoptionen sind verfügbar:

* `outputFileName`: Der Name der Ausgabe der Paketdatei. Einen relativen Pfad darf das *bundleconfig.json* Datei. **required**
* `inputFiles`: Ein Array von Dateien bündeln. Hierbei handelt es sich um relative Pfade in der Konfigurationsdatei. **optionale**, * führt ein leerer Wert eine leere Ausgabedatei. [Globmodus](http://www.tldp.org/LDP/abs/html/globbingref.html) Muster werden unterstützt.
* `minify`: Die Minimierung-Optionen für die Ausgabe geben. **optionale**, *Standard:`minify: { enabled: true }`*
  * Konfigurationsoptionen sind pro Datei Ausgabetyp verfügbar.
    * [CSS-Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript-Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML-Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Flag gibt an, ob die generierte Dateien zur Projektdatei hinzufügen. **optionale**, *Standard - "false"*
* `sourceMap`: Flag gibt an, ob eine quellzuordnung für die gebündelte Datei zu generieren. **optionale**, *Standard - "false"*
* `sourceMapRootPath`: Der Pfad des Anwendungsstamms für das Speichern der generierten Quelldatei für die Zuordnung.

## <a name="build-time-execution-of-bundling-and-minification"></a>Zur Buildzeit Ausführung Bündelung und Minimierung

Die [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet-Paket ermöglicht die Ausführung von Bündelung und Minimierung zur Buildzeit. Das Paket fügt [MSBuild-Ziele](/visualstudio/msbuild/msbuild-targets) dem zu erstellen und bereinigen Uhrzeit ausgeführt. Die *bundleconfig.json* Datei durch den Buildprozess die Ausgabedateien auf Basis der Konfiguration definierten erzeugen analysiert.

> [!NOTE]
> BuildBundlerMinifier gehört zu einem Projekt Community gesteuerte auf GitHub für die keine Unterstützung von Microsoft bereitgestellt. Sollten Probleme gemeldet werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Hinzufügen der *BuildBundlerMinifier* Paket Ihrem Projekt.

Erstellen Sie das Projekt. Im folgenden wird im Ausgabefenster angezeigt:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Bereinigen Sie das Projekt. Im folgenden wird im Ausgabefenster angezeigt:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli) 

Hinzufügen der *BuildBundlerMinifier* Paket Ihrem Projekt:

```console
dotnet add package BuildBundlerMinifier
```

Bei Verwendung von ASP.NET Core 1.x, stellen Sie das Paket neu hinzugefügte wieder her:

```console
dotnet restore
```

Erstellen Sie das Projekt:

```console
dotnet build
```

Im folgenden wird angezeigt:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Bereinigen Sie das Projekt:

```console
dotnet clean
```

Die folgende Ausgabe wird angezeigt:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Ad-hoc-Ausführung von Bündelung und Minimierung

Es ist möglich, die auf einer ad-hoc-Basis der Bündelung und Minimierung Aufgaben ausgeführt werden, ohne Erstellen des Projekts. Hinzufügen der [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet-Paket Ihrem Projekt:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core gehört zu einem Projekt Community gesteuerte auf GitHub für die keine Unterstützung von Microsoft bereitgestellt. Sollten Probleme gemeldet werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).

Dieses Paket erweitert, die .NET Core CLI enthalten die *Dotnet-Bundle* Tool. Der folgende Befehl kann im Paket-Manager-Konsole (PMC)-Fenster oder in eine Befehlsshell ausgeführt werden:

```console
dotnet bundle
```

> [!IMPORTANT]
> NuGet-Paket-Manager die Datei *.csproj als Abhängigkeiten hinzugefügt `<PackageReference />` Knoten. Die `dotnet bundle` Befehl wird mit der .NET Core-CLI registriert nur, wenn ein `<DotNetCliToolReference />` Knoten verwendet wird. Ändern Sie die Datei *.csproj entsprechend an.

## <a name="add-files-to-workflow"></a>Hinzufügen von Dateien an den workflow

Betrachten Sie ein Beispiel, in dem ein zusätzlicher *custom.css* Datei hinzugefügt wird, wie die folgenden:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Zu verkleinernde *custom.css* und bündeln mit *"Site.CSS" ändern* in einem *site.min.css* Datei, fügen Sie den relativen Pfad zum *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternativ könnte die folgenden Globmodus-Muster verwendet werden:
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]
> ```
>
> Dieses Muster Globmodus entspricht allen CSS-Dateien und schließt die verkleinerte Dateimuster.

Erstellen Sie die Anwendung. Open *site.min.css* , und beachten Sie den Inhalt des *custom.css* am Ende der Datei angefügt wird.

## <a name="environment-based-bundling-and-minification"></a>Umgebung basierende Bündelung und Minimierung

Als bewährte Methode sollte die gebündelten und verkleinerte Dateien Ihrer App in einer produktiven Umgebung verwendet werden. Stellen die ursprünglichen Dateien zum einfacheren Debuggen der app, während der Entwicklung.

Geben Sie die einzuschließenden Dateien aus, auf den Seiten mit den [Umgebung Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in Ihren Ansichten. Die Umgebung Tags Hilfsprogramm seinen Inhalt nur gerendert wird, bei der Ausführung in bestimmten [Umgebungen](xref:fundamentals/environments).

Die folgenden `environment` Tag rendert die unverarbeiteten CSS-Dateien, bei der Ausführung im die `Development` Umgebung:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

---

Die folgenden `environment` Tag rendert die gebündelten und verkleinerte CSS-Dateien aus, wenn in einer Umgebung ausgeführt, außer `Development`. Z. B. Ausführung `Production` oder `Staging` löst diese Stylesheets gerendert:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

---

## <a name="consume-bundleconfigjson-from-gulp"></a>Verwenden von Gulp bundleconfig.json

Es gibt Fälle, in denen eine app Bündelung und Minimierung Workflow zusätzliche Verarbeitung erforderlich ist. Beispiele sind Image Optimierung, Cache Abwehrprogramm und CDN Asset-Verarbeitung. Um diese Anforderungen zu erfüllen, können Sie den Workflow Bündelung und Minimierung zum Verwenden von Gulp konvertieren.

### <a name="use-the-bundler--minifier-extension"></a>Verwenden Sie die Erweiterung Bundle & Minifier

Visual Studio [Bundle & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) Erweiterung übernimmt die Konvertierung in Gulp.

> [!NOTE]
> Die Erweiterung Bundle & Minifier gehört zu einem Projekt Community gesteuerte auf GitHub für die keine Unterstützung von Microsoft bereitgestellt. Sollten Probleme gemeldet werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).

Mit der rechten Maustaste die *bundleconfig.json* im Projektmappen-Explorer die Datei, und wählen Sie **Bundle & Minifier** > **konvertieren, Gulp...** :

![Konvertieren zu Gulp Kontextmenüelement](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Die *gulpfile.js* und *"Package.JSON"* Dateien werden dem Projekt hinzugefügt. Die zur Unterstützung [Npm](https://www.npmjs.com/) Pakete aufgelistet, die der *"Package.JSON"* Datei `devDependencies` Abschnitt installiert sind.

Führen Sie den folgenden Befehl in einem PMC-Fenster auf die CLI Gulp als globale Abhängigkeit installieren:

```console
npm i -g gulp-cli
```

Die *gulpfile.js* Datei liest die *bundleconfig.json* -Datei für die Eingaben, Ausgaben und Einstellungen.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Konvertieren Sie manuell

Wenn Visual Studio und/oder die Bundle & Minifier-Erweiterung nicht verfügbar sind, konvertieren Sie manuell ein.

Hinzufügen einer *"Package.JSON"* -Datei mit den folgenden `devDependencies`, in das Projektstammverzeichnis:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installieren von Abhängigkeiten von den folgenden Befehl ausführen, auf der gleichen Ebene wie *"Package.JSON"*:

```console
npm i
```

Installieren Sie die CLI Gulp als globale Abhängigkeit:

```console
npm i -g gulp-cli
```

Kopieren der *gulpfile.js* Datei unter dem Projektstamm:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Gulp Tasks ausführen

Fügen Sie zum Auslösen der Gulp Minimierung-Aufgabe bevor Sie das Projekt in Visual Studio erstellt die folgenden [MSBuild-Ziel](/visualstudio/msbuild/msbuild-targets) in die Datei *.csproj:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

In diesem Beispiel alle Aufgaben definiert, in der `MyPreCompileTarget` Ziel ausführen, bevor die vordefinierten `Build` Ziel. Ähnlich der folgenden Ausgabe wird in Visual Studio-Fenster "Ausgabe" angezeigt:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Alternativ kann der Task Runner-Explorer von Visual Studio verwendet werden, Gulp Aufgaben an bestimmten Visual Studio-Ereignisse binden. Finden Sie unter [Ausführen von Standardaufgaben](xref:client-side/using-gulp#running-default-tasks) Anweisungen auf diese Weise.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwenden von Gulp](xref:client-side/using-gulp)
* [Verwenden von Grunt](xref:client-side/using-grunt)
* [Arbeiten mit mehreren Umgebungen](xref:fundamentals/environments)
* [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)
