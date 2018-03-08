---
title: Browserlink in ASP.NET Core
author: ncarandini
description: "Erklärt, wie Browserlink eine Visual Studio-Funktion, die die Entwicklungsumgebung mit mindestens einem Webbrowser verknüpft."
manager: wpickett
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95062961877b8da843ce47fb1719ee85224fa8c8
ms.sourcegitcommit: 9c27fa0f0c57ad611aa43f63afb9b9c9571d4a94
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="browser-link-in-aspnet-core"></a>Browserlink in ASP.NET Core 

Durch [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), und [Tom Dykstra](https://github.com/tdykstra)

Browserlink ist ein Feature in Visual Studio, der einen Kommunikationskanal zwischen der Entwicklungs- und eine oder mehrere Webbrowsern erstellt. Browser-Link, um Ihre Webanwendung gleichzeitig in mehreren Browsern aktualisieren können Sie eignet sich für browserübergreifende Tests.

## <a name="browser-link-setup"></a>Setup für Browser Link

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die ASP.NET Core 2.x **Webanwendung**, **leere**, und **Web-API** Vorlage Projekte verwenden die [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) Meta-Paket, das eine Paket-Referenz für enthält [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Verwenden Sie deshalb die `Microsoft.AspNetCore.All` metapaket erfordert keine weitere Aktion um Browserlink für die Verwendung verfügbar zu machen.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die ASP.NET Core 1.x **Webanwendung** -Projektvorlage enthält einen Paket-Verweis für die [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Paket. Die **leere** oder **Web-API** -Vorlagenprojekten erfordern, dass Sie einen Verweis Paket hinzufügen `Microsoft.VisualStudio.Web.BrowserLink`.

Da dies eine Visual Studio-Funktion, die einfachste Möglichkeit darin besteht, das Paket zum Hinzufügen einer **leere** oder **Web-API** Vorlagenprojekt besteht darin, öffnen die **Package Manager Console** (**Ansicht** > **Weitere Fenster** > **Package Manager Console**), und führen Sie den folgenden Befehl aus:

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

Alternativ können Sie **NuGet Package Manager**. Mit der rechten Maustaste in des Namens des Projekts **Projektmappen-Explorer** , und wählen Sie **NuGet-Pakete verwalten**:

![Öffnen von NuGet-Paket-Manager](using-browserlink/_static/open-nuget-package-manager.png)

Suchen Sie und installieren Sie das Paket:

![Paket mit NuGet-Paket-Manager hinzufügen](using-browserlink/_static/add-package-with-nuget-package-manager.png)

---

### <a name="configuration"></a>Konfiguration

In der `Configure` Methode der *Startup.cs* Datei:

```csharp
app.UseBrowserLink();
```

Der Code in der Regel befindet sich innerhalb einer `if` Block, der nur die Browser-Link in der Entwicklungsumgebung ermöglicht, wie hier gezeigt:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Weitere Informationen finden Sie unter [Working with Multiple Environments (Verwenden von mehreren Umgebungen)](xref:fundamentals/environments).

## <a name="how-to-use-browser-link"></a>Gewusst wie: Verwenden von Browserlink

Wenn Sie eine ASP.NET Core-Projekt geöffnet haben, zeigt Visual Studio das Symbolleistensteuerelement Browserlink neben der **Debugziel** Toolbar-Steuerelement:

![Browser Link Dropdown-Menü](using-browserlink/_static/browserLink-dropdown-menu.png)

Aus dem Browserlink-Toolbar-Steuerelement können Sie folgende Aktionen ausführen:

* Die Webanwendung in verschiedenen Browsern gleichzeitig zu aktualisieren.
* Öffnen der **Browserlink-Dashboard**.
* Aktivieren oder deaktivieren Sie **Browserlink**. Hinweis: Browserlink ist in Visual Studio 2017 (15.3) standardmäßig deaktiviert.
* Aktivieren oder deaktivieren Sie [automatische CSS-Synchronisierung](#enable-or-disable-css-auto-sync).

> [!NOTE]
> Einige Visual Studio-Plug-ins, vor allem Kontrollvorgänge *Web Erweiterung Pack 2015* und *Web Erweiterung Pack 2017*, bieten erweiterte Funktionalität für Browserlink, aber einige zusätzlichen Funktionen, die mit ASP funktionieren nicht. NET Core-Projekte.

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a>Aktualisieren Sie die Webanwendung gleichzeitig in mehreren Browsern

Verwenden Sie zum Auswählen eines einzelnen Web-Browsers zum Starten, wenn das Projekt starten das Dropdown-Menü in der **Debugziel** Toolbar-Steuerelement:

![F5-Dropdownmenü](using-browserlink/_static/debug-target-dropdown-menu.png)

Zum Öffnen von mehreren Browsern gleichzeitig wählen **durchsuchen mit...**  aus der gleichen Dropdownliste aus. Halten Sie die STRG-Taste auf die Browsern auswählen, werden soll, und klicken Sie dann auf **Durchsuchen**:

![Viele Browsern gleichzeitig öffnen](using-browserlink/_static/open-many-browsers-at-once.png)

Hier ist ein Screenshot der Visual Studio mit die Indexansicht öffnen und zwei geöffneten Browser:

![Synchronisieren Sie mit zwei Browser-Beispiel](using-browserlink/_static/sync-with-two-browsers-example.png)

Zeigen Sie auf dem Browserlink-Symbolleisten-Steuerelement auf den Browsern, die dem Projekt verbunden sind, finden Sie unter:

![Zeigen Sie Tipp](using-browserlink/_static/hoover-tip.png)

Ändern die Indexansicht und allen verbundenen Browsern werden aktualisiert, wenn Sie auf die Aktualisierungsschaltfläche klicken Browserlink:

![Browser-Synchronisierung auf Änderungen](using-browserlink/_static/browsers-sync-to-changes.png)

Browserlink funktioniert auch mit Browsern, die Sie außerhalb von Visual Studio starten, und navigieren Sie zu der URL der Anwendung.

### <a name="the-browser-link-dashboard"></a>Die Browserlink-Dashboard

Öffnen Sie die Browserlink-Dashboard aus dem Browserlink-Dropdown-Menü zum Verwalten einer Verbindung mit geöffneten Browser:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Wenn keine Browser verbunden ist, können Sie eine nicht-Debugsitzung starten, durch Auswählen der *in Browser anzeigen* Link:

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Andernfalls werden die verbundenen Browser durch den Pfad zu der Seite angezeigt, die jedem Browser angezeigt wird:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Wenn Sie möchten, können Sie auf einen aufgelisteten Browsernamen dieser einzelnen Browsersitzung aktualisieren klicken.

### <a name="enable-or-disable-browser-link"></a>Aktivieren oder Deaktivieren von Browserlink

Wenn Sie Browserlink erneut aktivieren nach dem deaktivieren, müssen Sie die Browser, um sie erneut eine Verbindung herstellen aktualisieren.

### <a name="enable-or-disable-css-auto-sync"></a>Aktivieren Sie oder deaktivieren Sie die automatische CSS-Synchronisierung

Wenn die automatische CSS-Synchronisierung aktiviert ist, werden die verbundenen Browsern automatisch aktualisiert, wenn Sie für jede Änderung an der CSS-Dateien vornehmen.

## <a name="how-does-it-work"></a>Wie funktioniert das?

Browserlink verwendet SignalR, um einen Kommunikationskanal zwischen Visual Studio und den Browser zu erstellen. Wenn Browserlink aktiviert ist, verhält sich Visual Studio als SignalR-Server, dem mehrere Clients (Browser), eine Verbindung herstellen können. Browserlink registriert auch eine middlewarekomponente in der ASP.NET-Pipeline für die Anforderung. Diese Komponente fügt spezielle `<script>` Verweise in jede Seitenanforderung vom Server. Sehen Sie dazu die Skriptverweise **Quelltext anzeigen** im Browser, und scrollen bis zum Ende der `<body>` tag-Inhalte:

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Die Quelldateien werden nicht geändert. Die Komponente fügt die Skriptverweise dynamisch. 

Da der Browser clientseitigen Code alle JavaScript ist, funktioniert es in allen Browsern, die SignalR unterstützt werden, ohne dass einen Browser-Plug-in.
