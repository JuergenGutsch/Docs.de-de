---
title: Layout
author: ardalis
description: 
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/layout
ms.openlocfilehash: e268f045e39188e9cc1e759ff7e6c553662dd669
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
# <a name="layout"></a>Layout

Durch [Steve Smith](https://ardalis.com/)

Ansichten verwenden häufig visuelle und programmgesteuerte Elemente. In diesem Artikel erfahren Sie, wie häufige Layouts verwenden, Teilen Direktiven und allgemeine Codes vor dem Rendern von Ansichten in der ASP.NET app ausführen.

## <a name="what-is-a-layout"></a>Was ist ein Layout

Die meisten Web-apps haben ein gebräuchliches Layout, das den Benutzer ein konsistentes Verhalten bietet, wie sie von einer Seite zu navigieren. Das Layout enthält i. d. r. allgemeine Benutzeroberflächenelemente wie das app-Header, Navigation oder im Menüelemente und Fußzeile.

![Seitenlayout-Beispiel](layout/_static/page-layout.png)

Allgemeine HTML-Strukturen, z. B. Skripts und Stylesheets werden viele Seiten in einer app auch häufig verwendet werden. Alle diese freigegebene Elemente können definiert werden, einem *Layout* -Datei, die dann von einer beliebigen Ansicht innerhalb der app verwendet verwiesen werden kann. Layouts reduzieren doppelte Code in Sichten, sodass führen Sie die [Don't wiederholen selbst (trocken)-Prinzip](http://deviq.com/don-t-repeat-yourself/).

Gemäß der Konvention lautet das Standardlayout für eine ASP.NET app `_Layout.cshtml`. Die Visual Studio ASP.NET Core MVC-Projektvorlage enthält diese Layoutdatei in die `Views/Shared` Ordner:

![Ansichtenordner im Projektmappen-explorer](layout/_static/web-project-views.png)

Dieses Layout definiert eine Vorlage der obersten Ebene für Sichten in der app. Apps erfordern kein Layout und apps können mehr als ein Layout definieren, mit anderen Ansichten, die unterschiedliche Layouts angeben.

Ein Beispiel für `_Layout.cshtml`:

[!code-html[Main](../../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=42,66)]

## <a name="specifying-a-layout"></a>Festlegen eines Layouts

Razor-Ansichten verfügen über eine `Layout` Eigenschaft. Einzelne Ansichten Geben Sie ein Layout durch Festlegen dieser Eigenschaft:

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

Das angegebene Layout können einen vollständigen Pfad (Beispiel: `/Views/Shared/_Layout.cshtml`) oder einen Teilnamen (Beispiel: `_Layout`). Wenn ein partielle Name angegeben ist, werden das Razor-Ansichtsmodul für die Layoutdatei mithilfe der standardmäßigen Ermittlungsprozess durchsucht. Der Controller zugeordnete Ordner durchsucht zuerst, gefolgt von den `Shared` Ordner. Dieser Discovery-Prozess ist identisch mit dem Kennwort verwendet, um ermitteln [Teilansichten](partial.md).

Standardmäßig muss jedes Layout Aufrufen `RenderBody`. Immer, wenn der Aufruf von `RenderBody` ist platziert werden, wird der Inhalt der Ansicht gerendert.

<a name="layout-sections-label"></a>

### <a name="sections"></a>Abschnitte

Ein Layout kann optional eine oder mehrere verweisen *Abschnitte*, durch den Aufruf `RenderSection`. Abschnitte bieten eine Möglichkeit zum Organisieren, wo bestimmte Seitenelemente platziert werden soll. Jeder Aufruf von `RenderSection` können angeben, ob dieses Abschnitts erforderlich oder optional ist. Wenn ein erforderliche Abschnitt nicht gefunden wird, wird eine Ausnahme ausgelöst. Einzelne Ansichten Geben Sie den Inhalt in einen Abschnitt mit gerendert werden die `@section` Razor-Syntax. Wenn eine Sicht ein Abschnitts definiert, gerendert werden müssen (oder tritt ein Fehler auf).

Ein Beispiel für `@section` Definition in einer Ansicht:

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
   }
   ```

Im obigen Code validierungsskripts hinzugefügt werden die `scripts` Abschnitt für eine Sicht, die ein Formular enthält. Anderen Sichten in der gleichen Anwendung zusätzliche Skripts möglicherweise nicht erforderlich, und daher einen Abschnitt zu Skripts definieren müssen.

In einer Sicht definierten Abschnitte sind nur in der Seite "sofortiges Layout für" verfügbar. Sie können nicht von Replikatsgruppenelemente ansichtskomponenten oder anderen Teilen des Systems Sicht verwiesen werden.

### <a name="ignoring-sections"></a>Ignorieren von Abschnitten

Standardmäßig müssen der Text und alle Abschnitte in einer Inhaltsseite alle von der Seite "Layout" gerendert werden. Das Razor-Ansichtsmodul erzwingt dies durch nachverfolgen, ob der Text und jeder Abschnitt gerendert wurde.

Um anzuweisen, das Anzeigemodul, die Text oder Abschnitte ignoriert werden sollen, rufen Sie die `IgnoreBody` und `IgnoreSection` Methoden.

Der Text und jeder Abschnitt in einer Razor-Page müssen entweder gerendert oder ignoriert werden.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Importieren von freigegebenen Direktiven

Sichten können Razor-Direktiven für viele Dinge sein, z. B. Namespaces importieren oder Ausführen von Aufgaben verwenden [Abhängigkeitsinjektion](dependency-injection.md). Anweisungen, die von vielen Ansichten gemeinsam verwendet werden können angegeben werden, in eine gemeinsame `_ViewImports.cshtml` Datei. Die `_ViewImports` Datei unterstützt die folgenden Direktiven:

* `@addTagHelper`

* `@removeTagHelper`

* `@tagHelperPrefix`

* `@using`

* `@model`

* `@inherits`

* `@inject`

Die Datei unterstützt keine andere Razor-Funktionen, z. B. Funktionen und Abschnittsdefinitionen.

Ein Beispiel für `_ViewImports.cshtml` Datei:

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

Die `_ViewImports.cshtml` -Konfigurationsdatei für eine ASP.NET-MVC-Anwendung Core sich in der Regel in befindet der `Views` Ordner. Ein `_ViewImports.cshtml` Datei kann in einem beliebigen Ordner, in dem Fall sie gelten nur für, in diesem Ordner und seine Unterordner Ansichten platziert werden. `_ViewImports`Dateien werden verarbeitet, beginnend auf der Stammebene, und klicken Sie dann für jeden Ordner führende bis zu der Speicherort der die Sicht selbst, weshalb die Angabe von Einstellungen auf der Stammebene können überschrieben werden auf Ordnerebene.

Z. B. wenn ein Stammebene `_ViewImports.cshtml` Datei gibt `@model` und `@addTagHelper`, und eine andere `_ViewImports.cshtml` -Datei in den Ordner Controller zugeordnet sind, der die Sicht gibt ein anderes `@model` und fügt eine andere `@addTagHelper`, die Ansicht Zugriff auf beide Tag Hilfsprogramme und wird mit der zweiten `@model`.

Wenn mehrere `_ViewImports.cshtml` Dateien für eine Sicht ausgeführt werden, Verhalten der Direktiven für die in enthaltenen kombiniert die `ViewImports.cshtml` Dateien werden wie folgt:

* `@addTagHelper`, `@removeTagHelper`: alle ausgeführt werden, in Reihenfolge

* `@tagHelperPrefix`: die nächste aus der Ansicht überschreibt alle anderen

* `@model`: die nächste aus der Ansicht überschreibt alle anderen

* `@inherits`: die nächste aus der Ansicht überschreibt alle anderen

* `@using`: sind enthalten; Duplikate werden ignoriert.

* `@inject`: die nächste aus der Ansicht für jede Eigenschaft überschreibt alle anderen mit dem gleichen Eigenschaftsnamen

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Ausführen von Code vor jeder Ansicht

Wenn Sie über code verfügen, müssen Sie vor jeder Ansicht ausführen, dies sollte der `_ViewStart.cshtml` Datei. Gemäß der Konvention werden die `_ViewStart.cshtml` befindet sich in der `Views` Ordner. Die Anweisungen in aufgeführten `_ViewStart.cshtml` vor jedem vollständige Ansicht (nicht-Layouts und nicht Teilansichten) ausgeführt werden. Wie [ViewImports.cshtml](xref:mvc/views/layout#viewimports), `_ViewStart.cshtml` ist hierarchisch aufgebaut. Wenn eine `_ViewStart.cshtml` Datei im Ordner "Controller zugeordnete Ansicht" definiert ist, er wird ausgeführt werden, nach der Datei im Stammverzeichnis des definiert die `Views` Ordner (sofern vorhanden).

Ein Beispiel für `_ViewStart.cshtml` Datei:

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Die oben genannte Datei gibt an, dass alle Ansichten verwendet die `_Layout.cshtml` Layout.

> [!NOTE]
> Weder `_ViewStart.cshtml` noch `_ViewImports.cshtml` befinden sich in der Regel der `/Views/Shared` Ordner. Die app-Ebene Versionen dieser Dateien sollte direkt in die `/Views` Ordner.
