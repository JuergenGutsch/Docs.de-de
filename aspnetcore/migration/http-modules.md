---
title: Migrieren von HTTP-Handler und Module auf ASP.NET Core authentifizierungsmiddleware beziehen.
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: tdykstra
manager: wpickett
ms.author: tdykstra
ms.date: 12/07/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: migration/http-modules
ms.openlocfilehash: eb5049d4d63c224ca74fc39072ae2c0d98ba330d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="migrating-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrieren von HTTP-Handler und Module auf ASP.NET Core authentifizierungsmiddleware beziehen. 

Durch [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)

In diesem Artikel wird gezeigt, wie zum Migrieren von vorhandenen ASP.NET [HTTP-Module und Ereignishandler in "System.Webserver"](https://docs.microsoft.com/iis/configuration/system.webserver/) zu ASP.NET Core [Middleware](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Module und Handler revisited

Pausieren zu ASP.NET Core Middleware wir zunächst kurz zusammengefasst, wie HTTP-Module und Handler funktionieren:

![Module-Handler](http-modules/_static/moduleshandlers.png)

**Ereignishandler sind:**

   * Klassen, in denen [IHttpHandler](https://docs.microsoft.com/dotnet/api/system.web.ihttphandler)

   * Verwendet die Verarbeitung von Anforderungen mit einem angegebenen Dateinamen oder eine Erweiterung, z. B. *.report*

   * [Konfiguriert](https://docs.microsoft.com//iis/configuration/system.webserver/handlers/) in *"Web.config"*

**Module sind:**

   * Klassen, in denen [IHttpModule](https://docs.microsoft.com/dotnet/api/system.web.ihttpmodule)

   * Für jede Anforderung aufgerufen

   * Lage Kurzschluss (Weitere Verarbeitung einer Anforderung beenden)

   * Die HTTP-Antwort hinzuzufügen, oder erstellen Sie ihre eigenen Lage

   * [Konfiguriert](https://docs.microsoft.com//iis/configuration/system.webserver/modules/) in *"Web.config"*

**Die Reihenfolge, in der Module eingehende Anforderungen verarbeitet werden, wird durch bestimmt:**

   1. Die [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx), also eine Reihe-Ereignisse, die von ASP.NET ausgelöst: [BeginRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.authenticaterequest)usw. Jedes Modul kann einen Handler für ein oder mehrere Ereignisse erstellt werden.

   2. Nach demselben Ereignis, die Reihenfolge, in der sie in konfiguriert sind *"Web.config"*.

Zusätzlich zu den Modulen, können Sie Handler für die Lebenszyklusereignisse zu hinzufügen Ihrer *Global.asax.cs* Datei. Diese Handler führen Sie nach der Handler in der konfigurierten Module.

## <a name="from-handlers-and-modules-to-middleware"></a>Vom Handler und Module auf authentifizierungsmiddleware beziehen.

**Middleware sind einfacher als HTTP-Module und Ereignishandler:**

   * Module, Handler *Global.asax.cs*, *"Web.config"* (mit Ausnahme von IIS-Konfiguration) und den Lebenszyklus der Anwendung nicht mehr vorhanden sind

   * Die Rollen von Modulen und Handler haben von Middleware übernommen wurden

   * Middleware konfiguriert sind, mithilfe von Code anstatt in *"Web.config"*

   * [Pipeline Verzweigen](xref:fundamentals/middleware/index#middleware-run-map-use) können Sie Anforderungen an bestimmten Middleware, die basierend auf nicht nur die URL, sondern auch von der Anforderungsheader, Abfragezeichenfolgen usw. gesendet werden.

**Middleware Module sehr ähnlich sind:**

   * Im Prinzip für jede Anforderung aufgerufen

   * Kann eine Anforderung von Kurzschluss [nicht die Anforderung an die nächste Middleware übergeben](#http-modules-shortcircuiting-middleware)

   * Erstellen Sie eigene HTTP-Antwort

**Middleware und Module werden in einer anderen Reihenfolge verarbeitet:**

   * Reihenfolge der Middleware basiert auf der Reihenfolge, in dem sie in der Anforderungspipeline, eingefügt sind während die Reihenfolge der Module hauptsächlich basiert [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx) Ereignisse

   * Reihenfolge der Middleware für Antworten ist das Gegenteil aus, die für Anforderungen, während der Reihenfolge von Modulen für Anforderungen und Antworten identisch ist

   * Finden Sie unter [erstellen eine middlewarepipeline mit IApplicationBuilder](xref:fundamentals/middleware/index#creating-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Beachten Sie, wie in der obigen Abbildung die authentifizierungsmiddleware die Anforderung kurzgeschlossen.

## <a name="migrating-module-code-to-middleware"></a>Migrieren von Modulcode auf authentifizierungsmiddleware beziehen.

Ein vorhandenes HTTP-Modul sieht in etwa wie folgt:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Entsprechend der [Middleware](xref:fundamentals/middleware/index) Seite eine Middleware ASP.NET Core ist eine Klasse, die verfügbar macht eine `Invoke` Methode erstellen eine `HttpContext` und Zurückgeben einer `Task`. Ihre neue Middleware wird wie folgt aussehen:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Die vorangehenden Middleware Vorlage stammt aus dem Abschnitt auf [schreiben Middleware](xref:fundamentals/middleware/index#middleware-writing-middleware).

Die *MyMiddlewareExtensions* Hilfsklasse erleichtert das Konfigurieren Ihrer Middleware in Ihre `Startup` Klasse. Die `UseMyMiddleware` Methode der Anforderungspipeline die Middleware-Klasse hinzugefügt. Von der Middleware erforderliche Dienste abrufen in der Middleware-Konstruktor eingefügt.

<a name="http-modules-shortcircuiting-middleware"></a>

Das Modul wird eine Anforderung, z. B. wenn der Benutzer nicht autorisiert ist möglicherweise beendet:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Eine Middleware verarbeitet diese durch Aufrufen von nicht `Invoke` auf die nächste Middleware in der Pipeline. Behalten Sie denken Sie daran, dass dies die Anforderung vollständig beenden nicht, da die vorherige Middlewares weiterhin aufgerufen wird, wenn die Antwort die Transportadresse wieder über die Pipeline macht.

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Bei der Migration des Moduls Funktionalität zu Ihrer neuen Middleware können möglicherweise der Code Kompilieren nicht, da die `HttpContext` -Klasse in ASP.NET Core erheblich geändert hat. [Später](#migrating-to-the-new-httpcontext), sehen Sie, wie auf den neuen ASP.NET Core HttpContext migriert.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrieren von Modul Einfügung in der Anforderungspipeline

HTTP-Module werden in der Regel die Anforderung mithilfe hinzugefügt *"Web.config"*:

[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Konvertieren von [Hinzufügen Ihrer neuen Middleware](xref:fundamentals/middleware/index#creating-a-middleware-pipeline-with-iapplicationbuilder) auf der Anforderungspipeline in Ihre `Startup` Klasse:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Die genaue Stelle in der Pipeline, in dem Sie Ihre neue Middleware einfügen, hängt das Ereignis, das sie als Modul verarbeitet (`BeginRequest`, `EndRequest`usw.) und die Reihenfolge, in der Liste der Module in *"Web.config"*.

Wie bereits erwähnt, besteht keine Anwendungslebenszyklus in ASP.NET Core und die Reihenfolge, in der Antworten von Middleware verarbeitet werden, unterscheidet sich von der Reihenfolge von Modulen verwendet. Dies kann der Reihenfolge Entscheidung anspruchsvoller zugreifen können.

Sollte die Sortierung ein Problem darstellen, konnte Sie Ihr Modul mehrere middlewarekomponenten gemeinsam sind. aufgeteilt, die unabhängig voneinander geordnet werden kann.

## <a name="migrating-handler-code-to-middleware"></a>Migrieren von Handlercode auf authentifizierungsmiddleware beziehen.

Ein HTTP-Handler sieht etwa wie folgt:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

In Ihrem Projekt ASP.NET Core würden Sie dies in eine Middleware, die etwa wie folgt übersetzt:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Diese Middleware ähnelt stark der Middleware Module entspricht. Der einzige Unterschied ist, wird hier nicht aufgerufen, es `_next.Invoke(context)`. Die dann sinnvoll, da der Handler am Ende der Anforderungspipeline, ist sodass es immer keine nächste Middleware aufgerufen.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrieren von Handler-Einfügung in der Anforderungspipeline

Konfigurieren einen HTTP-Handler erfolgt in *"Web.config"* und sieht ungefähr so aus:

[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Sie können dies konvertieren, indem der Anforderungspipeline in Ihrer neuen Handler Middleware hinzugefügt Ihre `Startup` Klasse ähnelt Middleware aus Modulen konvertiert. Das Problem bei diesem Ansatz ist, dass er alle Anforderungen an Ihre neuen Handler Middleware senden würde. Sie möchten jedoch nur Anforderungen mit einer bestimmten Erweiterung zum Erreichen Ihrer Middleware. Die würde Sie die gleiche Funktionalität zur Verfügung stellen, die Sie mit der HTTP-Handler zugewiesen waren.

Eine Lösung besteht darin, die Pipeline für Anforderungen mit einer bestimmten Erweiterung Verzweigung mithilfe der `MapWhen` -Erweiterungsmethode. Hierfür verwenden Sie die gleiche `Configure` Methode, in dem Sie die anderen Middleware hinzufügen:

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen`verwendet diese Parameter an:

1. Ein Lambda-Ausdruck, der akzeptiert die `HttpContext` und gibt `true` Falls Sie die Verzweigung die Anforderung werden soll. Dies bedeutet, dass Sie Anforderungen, die nicht nur basierend auf deren Erweiterung, sondern auch auf die Anforderungsheader, Abfragezeichenfolgen-Parameter usw. verzweigen können.

2. Ein Lambda-Ausdruck, der akzeptiert ein `IApplicationBuilder` und fügt die gesamte Middleware für die Verzweigung hinzu. Dies bedeutet, dass Sie zusätzliche Middleware in der Verzweigung vor Ihrer Middleware Handler hinzufügen können.

Die Middleware für die Pipeline hinzugefügt werden, bevor die Verzweigung für alle Anforderungen aufgerufen wird; die Verzweigung wird keine Auswirkungen darauf haben.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Laden mithilfe des Musters Optionen-middlewareoptionen.

Einige Module und Handler haben verschiedenen Konfigurationsoptionen, die im rowsetcache *"Web.config"*. In ASP.NET Core wird ein neues Konfigurationsmodell jedoch anstelle von verwendet *"Web.config"*.

Die neue [Konfigurationssystem](xref:fundamentals/configuration/index) bietet Ihnen diese Optionen, um dieses Problem zu beheben:

* Die Optionen in der Middleware direkt einfügen, entsprechend der [nächsten Abschnitt](#loading-middleware-options-through-direct-injection).

* Verwenden der [Optionen Muster](xref:fundamentals/configuration/options):

1.  Erstellen Sie eine Klasse, um die middlewareoptionen, z. B. halten:

    [!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2.  Speichern Sie die Optionswerte

    Das Konfigurationssystem können Sie Optionswerte an einer beliebigen Stelle zu speichern, die Sie möchten. Allerdings verwenden die meisten sites *appsettings.json*, damit wir diesen Ansatz werden müssen:

    [!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

    *MyMiddlewareOptionsSection* hier ist ein Abschnittsname. Er hat keine mit den Namen der Optionsklasse identisch sein.

3. Ordnen Sie die Optionswerte der Options-Klasse

    Das Muster für die Optionen des ASP.NET Core Dependency Injection Framework zum Zuordnen von der Optionstyp den verwendet (z. B. `MyMiddlewareOptions`) mit einer `MyMiddlewareOptions` -Objekt, das die tatsächlichen Optionen hat.

    Update der `Startup` Klasse:

    1.  Bei Verwendung von *appsettings.json*, Hinzufügen zum Konfigurations-Generator in der `Startup` Konstruktor:

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

    2.  Konfigurieren Sie den Dienst für die Optionen:

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    3.  Ordnen Sie die Optionen der Options-Klasse:

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4.  Fügen Sie die Optionen in der middlewarekonstruktor. Dies ähnelt der Optionen in einem Controller injiziert.

  [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

  Die [UseMiddleware](#http-modules-usemiddleware) Erweiterungsmethode, die die Middleware, fügt die `IApplicationBuilder` übernimmt die Abhängigkeitsinjektion.

  Dies ist nicht beschränkt auf `IOptions` Objekte. Ein anderes Objekt, das die Middleware erfordert, kann auf diese Weise eingegeben werden.

## <a name="loading-middleware-options-through-direct-injection"></a>Laden über direkte Injection-middlewareoptionen.

Das Muster Optionen hat den Vorteil, den lose Kopplung zwischen Optionen Werte und seinem Consumer erstellt wird. Nachdem Sie die tatsächlichen Optionen Werte eine Optionsklasse zugeordnet haben, kann jede andere Klasse Zugriff auf die Optionen über das Dependency Injection Framework abrufen. Es ist nicht erforderlich, um Optionen Werte übergeben.

Dieser Vorgang unterteilt aber wenn Sie die gleiche Middleware zweimal mit verschiedenen Optionen verwenden möchten. Zum Beispiel eine Autorisierung verwendete Middleware in anderen Verzweigungen, sodass andere Rollen. Die eine Optionsklasse können keine Objekte für zwei unterschiedliche Optionen zugeordnet werden.

Die Lösung besteht darin, erhalten die Optionsobjekte die tatsächlichen Optionen Werte in Ihre `Startup` Klasse, und übergeben Sie die direkt für jede Instanz Ihrer Middleware.

1.  Fügen Sie einen zweiten Schlüssel zum *appsettings.json*

    Um einen zweiten Satz von Optionen zum Hinzufügen der *appsettings.json* Datei, einen neuen Schlüssel verwenden, um eindeutig zu identifizieren:

    [!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2.  Optionen-Werte abzurufen, und dann an die Middleware weitergeleitet. Die `Use...` Erweiterungsmethode (wodurch der Pipeline Ihrer Middleware hinzugefügt) ist ein logischer Ausgangspunkt die Optionswerte übergeben: 

    [!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

4.  Aktiviert die Middleware auszuführenden Options-Parameter. Geben Sie eine Überladung der `Use...` Erweiterungsmethode (, die die Options-Parameter akzeptiert und übergibt es an `UseMiddleware`). Wenn `UseMiddleware` heißt mit Parametern, übergibt die Parameter für die middlewarekonstruktor beim instanziiert die Middleware-Objekt.

    [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

    Beachten Sie, wie dies in der Options-Objekt umschließt ein `OptionsWrapper` Objekt. Dies implementiert `IOptions`, wie von der middlewarekonstruktor erwartet.

## <a name="migrating-to-the-new-httpcontext"></a>Migrieren zu neuen HttpContext

Sie haben bereits gesehen, die die `Invoke` Methode in Ihrer Middleware nimmt einen Parameter vom Typ `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext`in ASP.NET Core wurde erheblich geändert werden. In diesem Abschnitt wird gezeigt, wie die am häufigsten verwendeten Eigenschaften der Verschiebung [System.Web.HttpContext](https://docs.microsoft.com/dotnet/api/system.web.httpcontext) mit dem neuen `Microsoft.AspNetCore.Http.HttpContext`.

### <a name="httpcontext"></a>HttpContext

**HttpContext.Items** übersetzt in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**Eindeutige Anforderungs-ID (kein Gegenstück System.Web.HttpContext)**

Bietet Ihnen eine eindeutige Id für jede Anforderung. Sehr nützlich, um Ihre Protokolle einschließt.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** übersetzt in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** und **HttpContext.Request.RawUrl** übersetzen in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** übersetzt in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** übersetzt in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** übersetzt in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Lesen Formularwerte nur, wenn der Inhalt Untertyp *X-www-form-urlencoded* oder *Formulardaten*.

**HttpContext.Request.InputStream** translates to:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Verwenden Sie diesen Code nur in einem Ereignishandler Typ-Middleware, am Ende einer Pipeline.
>
>Sie können die unformatierten Text lesen, wie oben nur einmal pro Anforderung. Middleware, die versuchen, den Nachrichtentext lesen, nachdem der erste Lesevorgang keinen Text gelesen wird.
>
>Dies gilt nicht für ein Formular wie oben gezeigt lesen, da, die aus einem Puffer erfolgt.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** und **HttpContext.Response.StatusDescription** übersetzen in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** und **HttpContext.Response.ContentType** übersetzen in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** auf einem eigenen auch übersetzt in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** übersetzt in:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

Eine Sicherungskopie der Datei dient wird erläutert [hier](../fundamentals/request-features.md#middleware-and-request-features).

**HttpContext.Response.Headers**

Senden der Antwortheader durch die Tatsache komplizierter ist, wenn Sie festlegen, nachdem alle Elemente in den Antworttext geschrieben wurde, sie wird nicht gesendet.

Die Lösung besteht darin, eine Rückrufmethode festgelegt, die vor dem Schreiben auf die Antwort beginnt rechts aufgerufen werden. Dies erfolgt am besten am Anfang der `Invoke` Methode in Ihrer Middleware. Es ist diese Rückrufmethode, die die Antwortheader festlegt.

Im folgenden Code wird eine Rückrufmethode aufgerufen `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Die `SetHeaders` Rückrufmethode würde wie folgt aussehen:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

Cookies übertragen an den Browser in einem *Set-Cookie* Antwortheader. Senden von Cookies erfordert daher demselben Rückruf verwendet zum Senden der Antwortheader:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Die `SetCookies` Rückrufmethode würde wie folgt aussehen:

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [HTTP-Handler und HTTP-Module (Übersicht)](/iis/configuration/system.webserver/)
* [Konfiguration](xref:fundamentals/configuration/index)
* [Application Startup (Starten von Anwendungen)](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
