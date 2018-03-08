---
title: "Erstellen ansprechender, reaktionsfähiger Websites mit Bootstrap"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/bootstrap
ms.openlocfilehash: f89ad584600c3f12a936599de27f931aff0cd4b5
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="building-beautiful-responsive-sites-with-bootstrap"></a>Erstellen ansprechender, reaktionsfähiger Websites mit Bootstrap

<a name="bootstrap-index"></a>

Durch [Steve Smith](https://ardalis.com/)

Bootstrap ist derzeit die am häufigsten verwendeten Webframework zum Entwickeln von Webanwendungen reaktionsfähig. Er bietet eine Reihe von Features und Vorteile, die Ihre Benutzer Kenntnisse in Bezug auf Ihre Website verbessern können, ob als am Front-End-Entwurf und Entwicklung oder Experte Anfänger. Bootstrap wird als eine Reihe von CSS und JavaScript-Dateien bereitgestellt und dient zur Skalierung Ihrer Website oder Anwendung effizient Telefone, Tablets auf Desktops schützen.

## <a name="getting-started"></a>Erste Schritte

Es gibt mehrere Möglichkeiten zum Einstieg in Bootstrap. Wenn Sie eine neue Webanwendung in Visual Studio starten, können Sie die Standard-Starter-Vorlage für ASP.NET Core, auswählen, in denen Groß-/Kleinschreibung Bootstrap vorinstallierte stammen:

![Bootstrapping in Projektmappenansicht für Starter-Vorlage](bootstrap/_static/bootstrap-in-starter-template.png)

Hinzufügen von Bootstrap zu einer ASP.NET Core Projekt ist lediglich hinzugefügt *"bower.JSON"* als Abhängigkeit:

[!code-json[Main](../common/samples/WebApplication1/bower.json?highlight=5)]

Dies ist die empfohlene Methode zum Bootstrap einem ASP.NET Core-Projekt hinzufügen.

Sie können auch mit einer von mehreren Paket-Manager, z. B. Bower, Npm oder NuGet Bootstrap installieren. In jedem Fall ist der Prozess im Wesentlichen identisch:

### <a name="bower"></a>Bower

```console
bower install bootstrap
```

### <a name="npm"></a>npm

```console
npm install bootstrap
```

### <a name="nuget"></a>NuGet

```console
Install-Package bootstrap
```

> [!NOTE]
> Die empfohlene Methode zum Installieren von Client-Side-Abhängigkeiten wie Bootstrap in ASP.NET Core per Bower ist (mit *"bower.JSON"*, wie oben gezeigt). Die Verwendung von Npm/NuGet werden angezeigt, um zu veranschaulichen, wie einfach Bootstrap auf andere Arten von Webanwendungen, einschließlich früherer Versionen von ASP.NET hinzugefügt werden kann.

Wenn Sie Ihren eigenen lokalen Versionen von Bootstrap verweisen, müssen Sie in alle Seiten, die Sie verweisen, in denen verwendet wird. In einer produktionsumgebung sollten Sie mit einem CDN Bootstrap verweisen. In der Standardeinstellung ASP.NET-Websitevorlage der *_Layout.cshtml* Datei daher wie folgt:

[!code-html[Main](../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=9,13,51,59)]

> [!NOTE]
> Wenn Sie mit der Bootstrap jQuery-Plug-Ins verwendet werden, müssen Sie auch jQuery verweisen.

## <a name="basic-templates-and-features"></a>Grundlegende Vorlagen und Funktionen

Die grundlegendste Bootstrap Vorlage sucht nach sehr ähnlich wie die *_Layout.cshtml* Datei dargestellt, einfach und höher umfasst ein einfachen Menüs für die Navigation und ein Ort, an den Rest der Seite gerendert.

### <a name="basic-navigation"></a>Grundlagen zur navigation

Die Standardvorlage verwendet eine Reihe von `<div>` Elemente zum Rendern einem oberen Navigationsleiste und den Hauptteil der Seite. Wenn Sie HTML5 verwenden, können Sie das erste ersetzen `<div>` tag mit einer `<nav>` -Tag, um dasselbe Ergebnis zu erzielen, aber mit genauere Semantik. In diesem ersten `<div>` können Sie sehen, es gibt noch mehrere andere. Zuerst wird eine `<div>` mit einer Klasse "Container", und dann innerhalb dieses, zwei weitere `<div>` Elemente: "Navbar-Header" und "Navigationsleiste erweitern / reduzieren". Die Navigationsleiste-Header Div enthält eine Schaltfläche, die angezeigt wird, wenn der Bildschirm unten eine bestimmte minimale Breite 3 horizontale Linien angezeigt wird (eine so genannte "Hamburger-Symbol"). Das Symbol wird mithilfe von reinen HTML und CSS gerendert; wurde kein Image ist erforderlich. Dies ist der Code, der das Symbol mit den einzelnen zeigt die <span> Rendern eines weißen Balken Tags:

```html
<button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
</button>
```

Darüber hinaus den Anwendungsnamen, die in der oberen linken Ecke angezeigt wird. Das Navigationsmenü im Hauptbereich gerendert wird die `<ul>` Element innerhalb der zweiten Div und enthält Links zur Startseite, etwa, und wenden Sie sich an. Zusätzliche Links für Registrierung und Anmeldung werden durch die Linie _LoginPartial in Zeile 29 hinzugefügt. Unter der Navigationsleiste wird Hauptteil Rand jeder Seite gerendert, in einer anderen `<div>`mit den Klassen "Container" und "Text-Inhalt" markiert. In der hier gezeigten einfachen Standardwert _Layout-Datei, den Inhalt der Seite von dem jeweiligen Ansichtstyp zugeordnet, die Seite, und klicken Sie dann einen einfachen gerendert werden `<footer>` wird hinzugefügt, bis zum Ende der `<div>` Element. Sie können sehen, wie die integrierte zu den Seiten mit angezeigt wird diese Vorlage:

![Zu den Seiten](bootstrap/_static/about-page-wide.png)

Die reduzierte Navigationsleiste, mit "Hamburger" Schaltfläche oben rechts, wird angezeigt, wenn das Fenster unter einer bestimmten Breite fällt:

![zu den Seiten mit Hamburger-Menü](bootstrap/_static/about-page-hamburger.png)

Durch Anklicken das Symbol wird der Menüelemente in einer vertikalen Bereich, die vom oberen Rand der Seite nach unten Folien:

![zu den Seiten mit Hamburger-Menü Erweitert](bootstrap/_static/about-page-hamburger-open.png)

### <a name="typography-and-links"></a>Typografie und links

Bootstrap richtet grundlegende Typografie, Farben und Formatierung in einer CSS-Datei verknüpfen des Standorts ein. Diese CSS-Datei enthält Standardstile für Tabellen, Schaltflächen, Formularelemente, Bilder und weitere ([erfahren Sie mehr](http://getbootstrap.com/css/)). Eine besonders nützlich ist das Raster Layoutsystem, neben abgedeckt.

### <a name="grids"></a>Raster

Eine der am häufigsten verwendeten Funktionen der Bootstrap ist seine Layoutsystem Raster. Innovative Webanwendungen vermeiden Sie die Verwendung der `<table>` Tag für das Layout, stattdessen die Verwendung dieses Elements mit tatsächlichen Tabellendaten einschränken. Stattdessen Spalten und Zeilen können werden angeordnet, mit einer Reihe von `<div>` Elemente und die entsprechenden CSS-Klassen. Es gibt mehrere Vorteile dieses Ansatzes, einschließlich der Möglichkeit zum Anpassen des Layouts von Rastern anzuzeigende vertikal auf kleine Bildschirme, z. B. Telefone.

[Der Bootstrap-Raster Layoutsystem](http://getbootstrap.com/css/#grid) basiert auf zwölf Spalten. Diese Zahl wurde gewählt, weil gleichmäßig in 1, 2, 3 oder 4 Spalten unterteilt werden und Spaltenbreite, zu innerhalb von 1 variieren können/12. vertikale Breite des Bildschirms. Zum Einstieg in das Raster Layoutsystem, beginnen Sie mit einem Container `<div>` und fügen Sie dann eine Zeile `<div>`, wie hier gezeigt:

```html
<div class="container">
    <div class="row">
        ...
    </div>
</div>
```

Als Nächstes fügen Sie zusätzliche `<div>` Elemente für jede Spalte, und geben Sie die Anzahl der Spalten, die `<div>` sollten als Teil einer CSS-Klasse, die mit "Col - Md-" beginnen (außerhalb des 12) belegen. Wenn einfach zwei Spalten gleicher Größe aufweisen sollen, würden Sie z. B. eine Klasse von "Col-Md-6" für jede einzelne verwenden. In diesem Fall "Md" ist die Kurzform für "Mittel" und bezieht sich auf dem Desktopcomputer Standardgröße Anzeigegrößen. Es gibt vier verschiedene Optionen, denen Sie auswählen können, und jede für höhere Breiten verwendet, es sei denn, Sie überschrieben (Wenn Sie das Layout unabhängig von der Bildschirmbreite korrigiert werden soll, geben Sie einfach Xs-Klassen können).

Präfix der CSS-Klasse | Geräte-Ebene | Breite
:---: | :---: | :---:
col-xs- | Telefone | < 768px
col-sm- | Tablet-PCs | > = 768px
col-md- | Desktops | > = 992px
col-lg- | Größere Desktop zeigt | > = 1200px

Wenn Sie beide zwei Spalten mit "Col-Md-6" das resultierende Layout, werden zwei Spalten mit desktop Auflösung jedoch diese beiden Spalten vertikal gestapelt, beim Rendern auf kleinen Geräten (oder ein schmaler Browserfenster auf einen Desktop), es den Benutzern zur schnellen Ansicht angeben der Inhalt ohne die Notwendigkeit, einen horizontalen Bildlauf durchführen.

Bootstrap wird immer zu einem Layout einspaltige standardmäßig, daher müssen Sie nur Spalten angeben, wenn Sie möchten, dass mehr als eine Spalte. Sollten explizit angeben, dass nur dann eine `<div>` einnehmen alle 12 Spalten wäre das Verhalten einer größeren Gerät Ebene überschreiben. Wenn Sie mehrere Geräteklassen Ebene angeben, müssen Sie möglicherweise die spaltenrenderings an bestimmten Punkten zurücksetzen. Hinzufügen Clearfix Div, die nur innerhalb einer bestimmten Viewport sichtbar ist, kann dies zu erreichen, wie hier gezeigt:

![schmale und breitzeichenfolgen Viewport-Raster](bootstrap/_static/narrow-and-wide-viewport-grid.png)

Im obigen Beispiel freigeben eins und zwei eine Zeile in das Layout "Md" während zwei und drei eine Zeile im Layout "Xs Teilen". Ohne die Clearfix `<div>`, 2 und 3 werden nicht ordnungsgemäß in der Ansicht "Xs" (Beachten Sie, die nur eine, vier und fünf angezeigt werden) angezeigt:

![Raster ohne clearfix](bootstrap/_static/grid-without-clearfix.png)

In diesem Beispiel nur eine einzelne Zeile `<div>` verwendet wurde, und Bootstrap noch größtenteils richtig sei in Bezug auf das Layout und Stapeln der Spalten hat. Sie sollten in der Regel geben Sie eine Zeile `<div>` für jede Zeile der horizontalen Layout erfordert, und Sie können natürlich Bootstrap Rastern in anderen schachteln. In diesem Fall nimmt rasterspezifisch geschachtelte 100 % der Breite des Elements in dem sie abgelegt wird, der mithilfe von Spaltenklassen unterteilt werden können.

### <a name="jumbotron"></a>Jumbotron

Wenn Sie die standardmäßige ASP.NET-MVC-Vorlagen in Visual Studio 2012 oder 2013 verwendet haben, haben Sie wahrscheinlich die Jumbotron in Aktion angezeigt. Er bezieht sich auf eine große voller Breite Abschnitt einer Seite, die verwendet werden kann, um ein großes Hintergrundbild, einen Aufruf der Aktion, einen Rotator oder ähnliche Elemente anzuzeigen. Um eine Seite einer Jumbotron hinzuzufügen, fügen Sie einfach eine `<div>` weisen Sie ihm eine Klasse von "Jumbotron", und platzieren Sie einen Container `<div>` innerhalb und Ihre Inhalte hinzufügen. Wir können leicht anpassen, den Standard zu den Seiten mit einer Jumbotron für die wichtigsten Überschriften, die angezeigt:

![Jumbotron-Beispiel](bootstrap/_static/jumbotron.png)

### <a name="buttons"></a>Schaltflächen

Die Standard-Schaltfläche-Klassen und deren Farben werden in der folgenden Abbildung gezeigt.

![Designs Schaltflächen](bootstrap/_static/theme-buttons.png)

### <a name="badges"></a>Signale

Signale finden Sie in kleinen, in der Regel numerischen Legenden neben ein Navigationselement. Sie können eine Anzahl von Nachrichten oder Benachrichtigungen, die darauf warten, oder das Vorhandensein von Updates hinweisen. Angeben von solchen Signale ist so einfach wie das Hinzufügen einer <span> mit dem Text, mit der Klasse "Badge":

![Designs Signale](bootstrap/_static/theme-badges.png)

### <a name="alerts"></a>Benachrichtigungen

Sie müssen möglicherweise eine Art von Benachrichtigungen, Warnung oder Fehlermeldung, die den Benutzern Ihrer Anwendung angezeigt. Das ist, in dem die Warnungen Standardklassen nützlich sind. Es gibt vier verschiedene Schweregrade mit zugeordneten Farbschemas aus:

![Designs Warnungen](bootstrap/_static/theme-alerts.png)

### <a name="navbars-and-menus"></a>Ansprechend und Menüs

Unsere Layout enthält bereits einen standard-Navigationsleiste, aber das Bootstrap-Design unterstützt zusätzliche Gestaltungsoptionen. Wir können ggf. auch einfach die Navigationsleiste werden vertikal, sondern horizontal Wenn, die bevorzugte hat, ebenfalls Hinzufügen von untergeordneten Navigationsleiste im Dropdown-Menüs Elemente angezeigt. Einfache Navigationsmenüs, z. B. Registerkarte leisten, basieren auf der Basis von <ul> Elemente. Diese können erstellt werden, sehr einfach, indem sie einfach mit der CSS-Klassen "Nav" und "Nav-Registerkarten" bereitstellen:

![Designs Seitenübersichten](bootstrap/_static/theme-tabstrips.png)

Ansprechend auf ähnliche Weise erstellt werden, sind jedoch ein wenig komplexer. Sie starten mit einem `<nav>` oder `<div>` mit der Klasse "Navbar", in dem Container Div die übrigen Elemente enthält. Unsere Seite enthält eine Navigationsleiste bereits im Kopf – das unten abgebildete einfach erweitert, Hinzufügen von Unterstützung für ein Dropdownmenü:

![Designs ansprechend](bootstrap/_static/theme-navbars.png)

### <a name="additional-elements"></a>Zusätzliche Elemente

Das Standarddesign kann auch zum Darstellen von HTML-Tabellen in eine ordentlich formatierte Formatvorlage, einschließlich der Unterstützung für Stripesetvolumes Ansichten verwendet werden. Es gibt Bezeichnungen mit Formatvorlagen, die ähnlich, mit denen die Schaltflächen sind. Sie erstellen benutzerdefinierte Dropdown-Menüs, die zusätzliche Gestaltungsoptionen hinter den standard-HTML-Code zu unterstützen `<select>` Element gemeinsam mit ansprechend, wie Sie unsere Starter-Standardwebsite wird bereits verwendet. Wenn Sie eine Statusanzeige benötigen, stehen mehrere Formate auswählen, sowie Gruppen aufzulisten und Bereiche, die einen Titel und den Inhalt enthalten. Untersuchen Sie zusätzliche Optionen in Bootstrap Standarddesigns hier:

[http://getbootstrap.com/examples/theme/](http://getbootstrap.com/examples/theme/)

## <a name="more-themes"></a>Weitere Themen

Sie können das Standarddesign Bootstrap erweitern, indem überschreiben einiger oder aller die CSS Anpassen der Farben und Formaten entsprechend den Anforderungen Ihrer eigenen Anwendung. Wenn Sie über ein vorgefertigtes Design starten möchten, sind mehrere Design Galerien online optimiert, die Bootstrap-Designs, z. B. WrapBootstrap.com (die eine Vielzahl von kommerziellen Designs aufweist) und Bootswatch.com (dem freien Designs bietet) verfügbar. Einige der verfügbaren kostenpflichtigen Vorlagen ermöglichen sehr viel systemverarbeitungszeit in Funktionalitätsebene auf der grundlegenden Bootstrap-Design, z. B. umfangreiche Unterstützung für die administrative Menüs und Dashboards mit umfangreichen Diagramme und Messgeräte. Ein Beispiel für eine beliebte kostenpflichtigen Vorlage ist Inspinia, derzeit für den Verkauf für $18, darunter eine ASP.NET MVC5-Vorlage zusätzlich zur AngularJS und statische HTML-Versionen. Ein Beispiel-Screenshot ist unten dargestellt.

![Beispiel Design inspinia](bootstrap/_static/theme-inspinia.png)

Wenn Sie das Bootstrap-Design ändern möchten, setzen Sie die *bootstrap.css* Datei für das Design, Sie in möchten, der **"Wwwroot" / Css** Ordner, und ändern Sie die Verweise im *_Layout.cshtml* um es zu verweisen. Ändern Sie die Links für alle Umgebungen:

```html
<environment names="Development">
    <link rel="stylesheet" href="~/css/bootstrap.css" />
```

```html
<environment names="Staging,Production">
    <link rel="stylesheet" href="~/css/bootstrap.min.css" />
```

Wenn Sie Ihr eigenes Dashboard erstellen möchten, können Sie starten aus dem verfügbaren freien Beispiel hier: [http://getbootstrap.com/examples/dashboard/](http://getbootstrap.com/examples/dashboard/).

## <a name="components"></a>Komponenten

Zusätzlich zu diesen Elementen, die bereits besprochen, Bootstrap bietet Unterstützung für eine Vielzahl von [integrierte Benutzeroberflächenkomponenten](http://getbootstrap.com/components/).

### <a name="glyphicons"></a>Glyphicons

Bootstrap enthält Symbolsätze aus Glyphicons ([http://glyphicons.com](http://glyphicons.com)), mit mehr als 200 Symbole für die Verwendung innerhalb der Bootstrap-fähigen Anwendung frei verfügbar. So sieht nur ein kleiner Teil aus:

![Glyphicons](bootstrap/_static/theme-glyphicons.png)

### <a name="input-groups"></a>Input-Gruppen

Eingabe Gruppen erlauben Bündeln von zusätzlichen Text und Schaltflächen mit ein input-Element, den Benutzer eine intuitivere Benutzeroberfläche bereitstellen:

![Input-Gruppen](bootstrap/_static/input-groups.png)

### <a name="breadcrumbs"></a>Brotkrümelnavigation

Breadcrumbs sind eine allgemeine Benutzeroberflächenkomponente verwendet, um einem Benutzer ihrer jüngsten Verlauf oder Tiefe innerhalb einer Standorthierarchie Navigation anzuzeigen. Problemlos hinzufügen, indem Sie die Klasse "Breadcrumb" an ein beliebiges anwenden `<ol>` Listenelement. Integrierten Unterstützung für die Paginierung einbinden, indem Sie die Klasse "Paginierung" verwenden, auf eine `<ul>` Element innerhalb einer `<nav>`. Reaktionsfähig eingebettete abzuspielen und Videos mit hinzufügen `<iframe>`, `<embed>`, `<video>`, oder `<object>` -Elemente, die Bootstrap automatisch formatieren wird. Geben Sie eine bestimmte Seitenverhältnis mithilfe von bestimmten Klassen wie "Einbetten-reaktionsfähig-16by9 an".

## <a name="javascript-support"></a>JavaScript-Unterstützung

Bootstrap JavaScript-Bibliothek enthält die API-Unterstützung für die enthalten Komponenten, die Sie zum Steuern des Verhaltens programmgesteuert innerhalb der Anwendung. Darüber hinaus *bootstrap.js* enthält mehr als einem Dutzend benutzerdefinierte jQuery-Plug-Ins, zusätzliche Funktionen wie Übergänge, modale Dialogfelder, führen Sie einen Bildlauf Erkennung (Aktualisieren von Formatvorlagen basierend auf, in dem vom Benutzer in das Dokument verwendete hat), Reduzieren Verhalten Laufbänder und Menüs zu dem Fenster, sodass sie außerhalb des Bildschirms einen Bildlauf nicht anfügen. Es ist nicht ausreichend Speicherplatz vorhanden ist, behandelt die JavaScript-Add-Ons Bootstrap – erfahren Sie mehr besuchen integriert [http://getbootstrap.com/javascript/](http://getbootstrap.com/javascript/).

## <a name="summary"></a>Zusammenfassung

Bootstrap bietet ein Web-Framework, die schnell und produktiver gestalten und Formatieren einer Vielzahl von Websites und Anwendungen verwendet werden kann. Seine grundlegenden Typografie und Formatvorlagen bieten ein angenehmeren Aussehen und Verhalten, die einfach durch die Unterstützung für benutzerdefiniertes Design, bearbeitet werden kann, die manuell erstellte oder im Handel erworben werden können. Unterstützt eine Vielzahl von Webkomponenten, die in der Vergangenheit teure Drittanbieter-Steuerelemente und unterstützen moderne und offene Webstandards zu erreichen, würde angefordert haben.
