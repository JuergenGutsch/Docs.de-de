---
title: Razor-Pages Route und app-Konvention Funktionen in ASP.NET Core
author: guardrex
description: Ermitteln Sie, wie Route und app-Anbieter Konvention Modellfunktionen Steuerelement Seite routing, Ermittlungs- und Verarbeitung Ihnen helfen.
ms.author: riande
manager: wpickett
ms.date: 10/23/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/razor-pages-convention-features
ms.openlocfilehash: 69475ca9abd4e732dc704ad6a8a2fffe219984f7
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="razor-pages-route-and-app-convention-features-in-aspnet-core"></a>Razor-Pages Route und app-Konvention Funktionen in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Erfahren Sie, wie Seite Route und app-Anbieter Konvention Modellfunktionen verwenden, um Seite routing, Ermittlungs- und Verarbeitung in Razor-Pages-apps zu steuern. Wenn Sie benutzerdefinierte Seite die Routen für einzelne Seiten konfigurieren müssen, Konfigurieren des Routings zu Seiten mit den [AddPageRoute Konvention](#configure-a-page-route) weiter unten in diesem Thema beschrieben.

Verwenden der [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/razor-pages-convention-features/sample) ([zum Herunterladen von](xref:tutorials/index#how-to-download-a-sample)), die in diesem Thema beschriebenen Funktionen zu untersuchen.

| Features | Im Beispiel wird veranschaulicht... |
| -------- | --------------------------- |
| [Route und app-Modell-Konventionen](#add-route-and-app-model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li></ul> | Hinzufügen einer routenvorlage und Header zu einer app-Seiten. |
| [Seite Route Aktion Konventionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Fügen Sie eine routenvorlage für die, Seiten in einem Ordner und den einer einzelnen Seite. |
| [Seite Modell Aktion Konventionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambda-Ausdruck oder Filter Factory)</li></ul> | Hinzufügen eines Headers zu einer einzelnen Seite Hinzufügen eines Headers zu Seiten in einem Ordner, und Konfigurieren einer [Filter Factory](xref:mvc/controllers/filters#ifilterfactory) , um eine app Seiten einen Header hinzuzufügen. |
| [Seite "app Standardanbieter](#replace-the-default-page-app-model-provider) | Ersetzen den Standardanbieter für Seite um den Konventionen für die Benennung von Handler zu ändern. |

## <a name="add-route-and-app-model-conventions"></a>Hinzufügen der Route und der app Model-Konventionen

Fügen Sie einen Delegaten für [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) hinzuzufügenden Route und app-Modell-Konventionen, die für die Razor-Pages gelten.

**Hinzufügen einer Route Modell Konvention für alle Seiten**

Verwendung [Konventionen](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions) erstellen und Hinzufügen einer [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) auf die Auflistung von [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) Instanzen, die während der Route und die Seite Modell angewendet werden Konstruktion.

Die Beispiel-app Fügt eine `{globalTemplate?}` routenvorlage für alle Seiten in der app:

[!code-csharp[Main](razor-pages-convention-features/sample/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

> [!NOTE]
> Die `Order` -Eigenschaft für die `AttributeRouteModel` festgelegt ist, um `0` (null). Dadurch wird sichergestellt, dass diese Vorlage Priorität für die Position der ersten Route-Daten-Wert angegeben wird, wenn ein einzelne routenwert angegeben wird. Das Beispiel fügt beispielsweise eine `{aboutTemplate?}` routenvorlage weiter unten in diesem Thema. Die `{aboutTemplate?}` Vorlage erhält eine `Order` von `1`. Bei der Anforderung der Seite "Info" am `/About/RouteDataValue`, "RouteDataValue" geladenen `RouteData.Values["globalTemplate"]` (`Order = 0`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 1`) aufgrund der Einstellung der `Order` Eigenschaft.

*Startup.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet1)]

Anfordern von Informationen zur Seite "im Beispiel" unter `localhost:5000/About/GlobalRouteValue` , und überprüfen Sie das Ergebnis:

![Die Seite "Info" mit einer Route Segment GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-convention-features/_static/about-page-global-template.png)

**Fügen Sie eine app-Modell-Konvention für alle Seiten**

Verwendung [Konventionen](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.razorpagesoptions.conventions) erstellen und Hinzufügen einer [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) auf die Auflistung von [IPageConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageconvention) Instanzen, die während der Route und Seite angewendet werden Erstellung des Modells.

Um diese und andere Konventionen weiter unten in diesem Thema demonstrieren, die Beispiel-app enthält ein `AddHeaderAttribute` Klasse. Der Klassenkonstruktor akzeptiert eine `name` Zeichenfolge und ein `values` Array von Zeichenfolgen. Diese Werte werden verwendet, dessen `OnResultExecuting` Methode, um einen Antwortheader einzurichten. Die vollständige Klasse wird angezeigt, der [Seite Modell Aktion Konventionen](#page-model-action-conventions) Abschnitt weiter unten in diesem Thema.

Das Beispiel-app verwendet die `AddHeaderAttribute` Klasse, um einen Header hinzuzufügen `GlobalHeader`, um alle Seiten in der app:

[!code-csharp[Main](razor-pages-convention-features/sample/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet2)]

Anfordern von Informationen zur Seite "im Beispiel" unter `localhost:5000/About` , und überprüfen Sie die Kopfzeilen so, dass das Resultset anzuzeigen:

![Antwort-Header, der die Seite "Info" anzeigen, dass die GlobalHeader hinzugefügt wurde.](razor-pages-convention-features/_static/about-page-global-header.png)

## <a name="page-route-action-conventions"></a>Seite Route Aktion Konventionen

Die Route Standardanbieter, die abgeleitet [IPageRouteModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelprovider) Konventionen, die entwickelt wurden, um Erweiterungspunkte Geben Sie zum Konfigurieren von Routen Seite aufruft.

**Ordner Route Modell Konvention**

Verwenden [AddFolderRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderroutemodelconvention) erstellen und Hinzufügen einer [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) , die eine Aktion aufruft, auf die [PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel) für alle Seiten unter der Der angegebene Ordner.

Das Beispiel-app verwendet `AddFolderRouteModelConvention` Hinzufügen einer `{otherPagesTemplate?}` routenvorlage für die Seiten in der *OtherPages* Ordner:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet3)]

> [!NOTE]
> Die `Order` -Eigenschaft für die `AttributeRouteModel` festgelegt ist, um `1`. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (Set weiter oben in diesem Thema) Priorität erhält, für die erste Routendaten Position Wert, wenn kein routenwert für die einzelnen angegeben ist. Wenn auf der Seite "Page1" angefordert wird `/OtherPages/Page1/RouteDataValue`, "RouteDataValue" geladenen `RouteData.Values["globalTemplate"]` (`Order = 0`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 1`) aufgrund der Einstellung der `Order` Eigenschaft.

Fordern Sie das Beispiel Page1 Seite am `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` und überprüfen Sie das Ergebnis:

![Page1 im Ordner "OtherPages" wird mit einer Route Segment GlobalRouteValue und OtherPagesRouteValue angefordert. Die gerenderte Seite zeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-convention-features/_static/otherpages-page1-global-and-otherpages-templates.png)

**Seite Route Modell Konvention**

Verwenden [AddPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageroutemodelconvention) erstellen und Hinzufügen einer [IPageRouteModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageroutemodelconvention) , die eine Aktion aufruft, auf die [PageRouteModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageroutemodel) für die Seite mit dem angegebenen Der Name.

Das Beispiel-app verwendet `AddPageRouteModelConvention` Hinzufügen einer `{aboutTemplate?}` routenvorlage auf der Seite "Info":

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet4)]

> [!NOTE]
> Die `Order` -Eigenschaft für die `AttributeRouteModel` festgelegt ist, um `1`. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (Set weiter oben in diesem Thema) Priorität erhält, für die erste Routendaten Position Wert, wenn kein routenwert für die einzelnen angegeben ist. Wenn auf der Seite "Info" angefordert wird `/About/RouteDataValue`, "RouteDataValue" geladenen `RouteData.Values["globalTemplate"]` (`Order = 0`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 1`) aufgrund der Einstellung der `Order` Eigenschaft.

Anfordern von Informationen zur Seite "im Beispiel" unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` , und überprüfen Sie das Ergebnis:

![Zu den Seiten mit Route Segmente für GlobalRouteValue und AboutRouteValue angefordert. Die gerenderte Seite zeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-convention-features/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Konfigurieren Sie eine Route für die Seite "

Verwendung [AddPageRoute](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.addpageroute) so konfigurieren Sie eine Route zu einer Seite im angegebenen Pfad. Generierte Links auf der Seite verwenden, die angegebene Route. `AddPageRoute`verwendet `AddPageRouteModelConvention` herstellen die Route.

Die Beispiel-app erstellt eine Route zum `/TheContactPage` für *Contact.cshtml*:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet5)]

Die Seite "Kontakt" auch am erreicht werden kann `/Contact` über die Standardroute.

Die Beispiel-app benutzerdefinierte Route zur Seite "Kontakt" ermöglicht, einen optionalen `text` Route-Segment (`{text?}`). Die Seite enthält auch diese optionale-Segment in seiner `@page` Richtlinie für den Fall, dass der Besucher der Seite zur uefa greift auf die `/Contact` Route:

[!code-cshtml[Main](razor-pages-convention-features/sample/Pages/Contact.cshtml?highlight=1)]

Beachten Sie, die für die URL generiert die **Kontakt** Link in der gerenderten Seite spiegelt wider, die aktualisierte Route:

![Kontakt-Link der Beispiel-app in der Navigationsleiste](razor-pages-convention-features/_static/contact-link.png)

![Überprüfen den Link in der gerenderten HTML-Code gibt an, dass das Href, um festgelegt ist "/ TheContactPage"](razor-pages-convention-features/_static/contact-link-source.png)

Besuchen Sie die Kontakt-Seite an seiner normalen Route `/Contact`, oder die benutzerdefinierte Route `/TheContactPage`. Wenn Sie ein zusätzliches angeben `text` Route-Segment die Seite zeigt die HTML-codierte Segment, bereitzustellen:

![Edge-Browser Beispiel ein optionalen 'Text'-Route-Segments "Textwert" in der URL angeben. Die gerenderte Seite zeigt den Wert des datenbanksegments 'Text'.](razor-pages-convention-features/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Seite Modell Aktion Konventionen

Die Seite Modell Standardanbieter, die implementiert [IPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelprovider) ruft Konventionen denen Erweiterungspunkte für das Konfigurieren von Seite Modelle bereitstellen werden. Diese Konventionen sind hilfreich beim Erstellen und Ändern von Seite Ermittlung und Verarbeitungsfunktionen.

Die Beispiele in diesem Abschnitt die Beispiel-app verwendet eine `AddHeaderAttribute` Klasse, die eine [ResultFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.resultfilterattribute), das einen Antwortheader anwendet:

[!code-csharp[Main](razor-pages-convention-features/sample/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut für alle Seiten in einem Ordner und eine einzelne Seite gelten.

**Ordner app Modell Konvention**

Verwendung [AddFolderApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addfolderapplicationmodelconvention) erstellen und Hinzufügen einer [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) , die auf eine Aktion aufruft [PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel) für Instanzen alle Seiten in dem angegebenen Ordner.

Das Beispiel veranschaulicht die Verwendung von `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers `OtherPagesHeader`, zu den Seiten innerhalb der *OtherPages* Ordner der Anwendung aus:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet6)]

Fordern Sie das Beispiel Page1 Seite am `localhost:5000/OtherPages/Page1` und überprüfen Sie die Kopfzeilen so, dass das Resultset anzuzeigen:

![Antwort-Header der Seite OtherPages/Page1 anzeigen, dass die OtherPagesHeader hinzugefügt wurde.](razor-pages-convention-features/_static/page1-otherpages-header.png)

**Seite "app-Modell Konvention**

Verwenden [AddPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageconventioncollection.addpageapplicationmodelconvention) erstellen und Hinzufügen einer [IPageApplicationModelConvention](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.ipageapplicationmodelconvention) , die eine Aktion aufruft, auf die [PageApplicationModel](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.pageapplicationmodel) für die Seite mit dem Namen Speciifed.

Das Beispiel veranschaulicht die Verwendung von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers `AboutHeader`, auf der Seite "Info":

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet7)]

Anfordern von Informationen zur Seite "im Beispiel" unter `localhost:5000/About` , und überprüfen Sie die Kopfzeilen so, dass das Resultset anzuzeigen:

![Antwort-Header, der die Seite "Info" anzeigen, dass die AboutHeader hinzugefügt wurde.](razor-pages-convention-features/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter) konfiguriert die angegebenen Filter angewendet. Implementieren Sie eine Filterklasse, aber die Beispiel-app zeigt, wie einen Filter in einem Lambdaausdruck implementieren, die hinter den Kulissen als Factory implementiert wird, die einen Filter zurückgibt:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet8)]

Die Seite app-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die auf der Seite Page2 verursachen die *OtherPages* Ordner. Wenn die Bedingung übergibt, wird eine Kopfzeile hinzugefügt. Wenn dies nicht der Fall, wird die `EmptyFilter` angewendet wird.

`EmptyFilter`ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Da Aktionsfilter von Razor-Pages, ignoriert werden die `EmptyFilter` Nein Ops erwartungsgemäß, wenn der Pfad keinen `OtherPages/Page2`.

Fordern Sie das Beispiel Page2 Seite am `localhost:5000/OtherPages/Page2` und überprüfen Sie die Kopfzeilen so, dass das Resultset anzuzeigen:

![Die OtherPagesPage2Header wird die Antwort für Page2 hinzugefügt.](razor-pages-convention-features/_static/page2-filter-header.png)

**Eine Factory Filter konfigurieren**

[ConfigureFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.configurefilter?view=aspnetcore-2.0#Microsoft_Extensions_DependencyInjection_PageConventionCollectionExtensions_ConfigureFilter_Microsoft_AspNetCore_Mvc_ApplicationModels_PageConventionCollection_System_Func_Microsoft_AspNetCore_Mvc_ApplicationModels_PageApplicationModel_Microsoft_AspNetCore_Mvc_Filters_IFilterMetadata__) konfiguriert die angegebene Factory anzuwendende [Filter](xref:mvc/controllers/filters) für alle Razor-Pages.

Die Beispiel-app bietet ein Beispiel für eine [Filter Factory](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen eines Headers `FilterFactoryHeader`, mit zwei Werten, um die app Seiten:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[Main](razor-pages-convention-features/sample/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Anfordern von Informationen zur Seite "im Beispiel" unter `localhost:5000/About` , und überprüfen Sie die Kopfzeilen so, dass das Resultset anzuzeigen:

![Antwort-Header, der die Seite "Info" anzeigen, dass zwei FilterFactoryHeader Header hinzugefügt wurden.](razor-pages-convention-features/_static/about-page-filter-factory-header.png)

## <a name="replace-the-default-page-app-model-provider"></a>Ersetzen Sie den Standardanbieter für Seite-app

Razor-Pages verwendet die `IPageApplicationModelProvider` Benutzeroberfläche zum Erstellen einer [DefaultPageApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider). Sie können von den Standardanbieter für Ihre eigene Implementierungslogik für die Verarbeitung und Handler Ermittlung bereitstellen erben. Die standardmäßige Implementierung ([Verweisquelle](https://github.com/aspnet/Mvc/blob/rel/2.0.1/src/Microsoft.AspNetCore.Mvc.RazorPages/Internal/DefaultPageApplicationModelProvider.cs)) richtet Konventionen für *unbenannte* und *mit dem Namen* Handler benennen, die unten beschrieben ist.

**Unbenannte Handler-Standardmethoden**

Ereignishandlermethoden für HTTP-Verben ("unbenannt" Ereignishandlermethoden) führen Sie eine Konvention: `On<HTTP verb>[Async]` (anhängen `Async` ist optional, jedoch empfohlen, für die Async-Methoden).

| Unbenannte Handler-Methode     | Vorgang                      |
| -------------------------- | ------------------------------ |
| `OnGet`/`OnGetAsync`       | Initialisieren Sie die Seite Status an.     |
| `OnPost`/`OnPostAsync`     | POST-Anforderungen verarbeitet werden.          |
| `OnDelete`/`OnDeleteAsync` | Anforderungen zum Löschen von Handle &#8224;. |
| `OnPut`/`OnPutAsync`       | Handle-PUT-Anforderungen &#8224;.    |
| `OnPatch`/`OnPatchAsync`   | Handle-PATCH-Anforderungen &#8224;.  |

&#8224; Zum treffen von API-Aufrufe an die Seite verwendet.

**Ereignishandlermethoden benannten**

Handlermethoden, die vom Entwickler ("mit dem Namen" Ereignishandlermethoden) zur Verfügung führen Sie eine ähnliche Konvention. Der Ereignishandler-Name wird angezeigt, nach der das HTTP-Verb oder zwischen dem HTTP-Verb und `Async`: `On<HTTP verb><handler name>[Async]` (anhängen `Async` ist optional, jedoch empfohlen, für die Async-Methoden). Beispielsweise können die Methoden, die Nachrichten verarbeiten dauern die Benennung in der folgenden Tabelle gezeigt.

| Beispiel mit dem Namen der Methode des ereignishandlers             | Beispiel-Vorgang        |
| ---------------------------------------- | ------------------------ |
| `OnGetMessage`/`OnGetMessageAsync`       | Eine Meldung zu erhalten.        |
| `OnPostMessage`/`OnPostMessageAsync`     | Senden Sie eine Nachricht an.          |
| `OnDeleteMessage`/`OnDeleteMessageAsync` | Löschen einer Nachricht &#8224;. |
| `OnPutMessage`/`OnPutMessageAsync`       | Fügen Sie eine Nachricht &#8224;.    |
| `OnPatchMessage`/`OnPatchMessageAsync`   | Patch für eine Nachricht &#8224;.  |

&#8224; Zum treffen von API-Aufrufe an die Seite verwendet.

**Anpassen von Handlernamen-Methode**

Angenommen Sie, Sie es vorziehen, die unbenannte und benannte Ereignishandlermethoden heißen ändern. Eine alternative Benennungsschema besteht darin zu vermeiden, starten den Methodennamen mit "On", und verwenden das erste Wort Segment, um das HTTP-Verb zu bestimmen. Sie können andere Änderungen vornehmen, wie die Konvertierung der Verben zum Löschen, einfügen und PATCH, POST. Ein derartiges bietet den Methodennamen, der in der folgenden Tabelle gezeigt.

| Handler-Methode                       | Vorgang                      |
| ------------------------------------ | ------------------------------ |
| `Get`                                | Initialisieren Sie die Seite Status an.     |
| `Post`/`PostAsync`                   | POST-Anforderungen verarbeitet werden.          |
| `Delete`/`DeleteAsync`               | Anforderungen zum Löschen von Handle &#8224;. |
| `Put`/`PutAsync`                     | Handle-PUT-Anforderungen &#8224;.    |
| `Patch`/`PatchAsync`                 | Handle-PATCH-Anforderungen &#8224;.  |
| `GetMessage`                         | Eine Meldung zu erhalten.              |
| `PostMessage`/`PostMessageAsync`     | Senden Sie eine Nachricht an.                |
| `DeleteMessage`/`DeleteMessageAsync` | Senden Sie eine Nachricht zu löschen.      |
| `PutMessage`/`PutMessageAsync`       | Senden Sie eine Nachricht, eingefügt werden soll.         |
| `PatchMessage`/`PatchMessageAsync`   | Senden Sie eine Nachricht beim Patchen.       |

&#8224; Zum treffen von API-Aufrufe an die Seite verwendet.

Um dieses Schema zu erstellen, erben die `DefaultPageApplicationModelProvider` Klasse, und überschreiben die [CreateHandlerModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.internal.defaultpageapplicationmodelprovider.createhandlermodel) Methode, um benutzerdefinierte Logik zum Auflösen von bereitzustellen [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) Handlernamen. Die Beispiel-app wird gezeigt, wie dies funktioniert seiner `CustomPageApplicationModelProvider` Klasse:

[!code-csharp[Main](razor-pages-convention-features/sample/CustomPageApplicationModelProvider.cs?name=snippet1&highlight=1-2,45-46,64-68,78-85,87,92,106)]

Die Klasse bietet folgende Vorteile:

* Die Klasse erbt von `DefaultPageApplicationModelProvider`.
* Die `TryParseHandlerMethod` verarbeitet einen Handler, um zu bestimmen, das HTTP-Verb (`httpMethod`) und benannte Handlernamen (`handlerName`) beim Erstellen der `PageHandlerModel`.
  * Ein `Async` Postfix wird ignoriert, falls vorhanden.
  * Schreibweise wird verwendet, um das HTTP-Verb aus dem Methodennamen zu analysieren.
  * Wenn der Name der Methode (ohne `Async`) ist gleich ist der Name des HTTP-Verb, kein benannte Handler vorhanden ist. Die `handlerName` festgelegt ist, um `null`, und der Methodenname ist `Get`, `Post`, `Delete`, `Put`, oder `Patch`.
  * Wenn der Name der Methode (ohne `Async`) ist länger als der Name des HTTP-Verb, ein benannten Handler vorhanden ist. `handlerName` wird auf `<method name (less 'Async', if present)>` festgelegt. Beispielsweise führen "GetMessage" und "GetMessageAsync" eine Handlernamen "GetMessage" zu.
  * Löschen, PUT und PATCH HTTP-Verben sind in POST konvertiert.

Registrieren der `CustomPageApplicationModelProvider` in die `Startup` Klasse:

[!code-csharp[Main](razor-pages-convention-features/sample/Startup.cs?name=snippet10)]

Der Code-Behind-Datei *Index.cshtml.cs* wird gezeigt, wie die normalen Handler Methode Benennungskonventionen für Seiten in der app geändert werden. Die normalen "On" Präfix Benennung mit Razor-Pages verwendet wird entfernt. Die Methode, die die Seite Status initialisiert heißt jetzt `Get`. Sie können sehen, dass diese Konvention, die in der app verwendet werden, wenn Sie eine Code-Behind-Datei für jede der Seiten, öffnen.

Jedes der anderen Methoden beginnen Sie mit dem HTTP-Verb, das beschreibt, deren Verarbeitung. Die beiden Methoden, die mit beginnt `Delete` normalerweise als DELETE HTTP-Verben, aber die Logik in behandelt werden würde `TryParseHandlerMethod` legt explizit das Verb POST für beide Handler.

Beachten Sie, dass `Async` ist optional, zwischen `DeleteAllMessages` und `DeleteMessageAsync`. Sind beide asynchrone Methoden, aber Sie können auch mithilfe der `Async` postfix oder nicht; es wird empfohlen, dass Sie verwendet wird. `DeleteAllMessages`zur Veranschaulichung wird hier verwendet, aber es wird empfohlen, dass Sie eine solche Methode benennen `DeleteAllMessagesAsync`. Er wirkt sich nicht auf die Verarbeitung des des Beispiels Implementierung, aber mit der `Async` postfix-Aufrufe die Tatsache, dass es sich um eine asynchrone Methode handelt.

[!code-csharp[Main](razor-pages-convention-features/sample/Pages/Index.cshtml.cs?name=snippet1&highlight=1,6,16,29)]

Beachten Sie die Handlernamen in bereitgestellten *Index.cshtml* entsprechen den `DeleteAllMessages` und `DeleteMessageAsync` Ereignishandlermethoden:

[!code-cshtml[Main](razor-pages-convention-features/sample/Pages/Index.cshtml?range=29-60&highlight=7-8,24-25)]

`Async`im Handlermethodennamen `DeleteMessageAsync` freigehaltenem durch Verkleinern der `TryParseHandlerMethod` für den Handler Abgleich der POST-Anforderung an die Methode. Die `asp-page-handler` Name des `DeleteMessage` abgeglichen wird, um die Handlermethode `DeleteMessageAsync`.

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC-Filter und die Seitenfilter (IPageFilter)

MVC [Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor-Pages ignoriert, da Razor-Pages Ereignishandlermethoden verwenden. Andere Arten von MVC-Filtern für Ihre Verwendung verfügbar sind: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters), und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie unter der [Filter](xref:mvc/controllers/filters) Thema.

Die Seite zu filtern ([IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter)) ist ein Filter, die für die Razor-Pages gilt. Es schließt die Ausführung einer Seite Handler-Methode. Sie können Sie benutzerdefinierten Code in Phasen der seitenausführung Handler-Methode zu verarbeiten. Hier ist ein Beispiel aus der Beispiel-app:

[!code-csharp[Main](razor-pages-convention-features/sample/Filters/ReplaceRouteValueFilterAttribute.cs?name=snippet1)]

Dieser Filter überprüft, ob ein `globalTemplate` Wert von "TriggerValue" und Swaps in "Ersatzwert" weiterzuleiten.

Die `ReplaceRouteValueFilter` Attribut kann direkt angewendet eine `PageModel` im Code-Behind:

[!code-csharp[Main](razor-pages-convention-features/sample/Pages/OtherPages/Page3.cshtml.cs?range=10-12&highlight=1)]

Die Beispiel-app mit auf der Seite "Page3" anfordern `localhost:5000/OtherPages/Page3/TriggerValue`. Beachten Sie, wie der Filter den routenwert ersetzt:

![OtherPages/Page3 mit einem TriggerValue Route Segment-Ergebnissen in den Filter, und Ersetzen Sie dabei den routenwert mit Ersatzwert anfordern.](razor-pages-convention-features/_static/otherpages-page3-filter-replacement-value.png)

## <a name="see-also"></a>Siehe auch

* [Autorisierungskonventionen für Razor-Pages](xref:security/authorization/razor-pages-authorization)
