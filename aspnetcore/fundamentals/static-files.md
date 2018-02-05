---
title: Arbeiten Sie mit statischen Dateien in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie dienen, statische Dateien gesichert werden, und konfigurieren statischen Datei hosting Middleware-Verhalten in einer ASP.NET Core-Web-app.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/18/2018
ms.devlang: csharp
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/static-files
ms.openlocfilehash: 60b205bf0a45e2965f9dab46f46956947ae513fd
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="work-with-static-files-in-aspnet-core"></a>Arbeiten Sie mit statischen Dateien in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Addie](https://twitter.com/Scott_Addie)

Statische Dateien, z. B. HTML, CSS, Bilder und JavaScript, sind Objekte, die eine ASP.NET Core app direkt an Clients dient. Einige Konfigurationen ist erforderlich, um die Bereitstellung dieser Dateien ausgeführt werden kann.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Statischen Dateien

Statische Dateien sind im Web-Stammverzeichnis des Projekts gespeichert. Das Standardverzeichnis ist  *\<Content_root > / "Wwwroot"*, aber sie kann geändert werden, über die [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) Methode. Finden Sie unter [Content Stamm](xref:fundamentals/index#content-root) und [Stammweb](xref:fundamentals/index#web-root) für Weitere Informationen.

Webhost, der die app muss für das Stammverzeichnis der Inhalte zur Kenntnis.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die `WebHost.CreateDefaultBuilder` Methode legt die Inhalten-Stamm im aktuellen Verzeichnis:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Legen Sie die Inhalten-Stamm auf das aktuelle Verzeichnis durch den Aufruf [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) innerhalb eines `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

---

Statische Dateien werden über einen Pfad relativ zum Webstamm zugegriffen. Z. B. die **Webanwendung** -Projektvorlage enthält mehrere Ordner innerhalb der *"Wwwroot"* Ordner:

* **wwwroot**
  * **css**
  * **images**
  * **js**

URI-Format, um den Zugriff auf eine Datei in die *Bilder* Unterordner *http://\<einfügen >/Images /\<Bilddateiname >*. Beispielsweise *http://localhost:9189/images/banner3.svg*.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Wenn .NET Framework abzielen, fügen Sie der [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) Paket Ihrem Projekt. Wenn Sie .NET Core als Ziel der [Microsoft.AspNetCore.All Metapackage](xref:fundamentals/metapackage) dieses Paket enthält.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Hinzufügen der [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) Paket Ihrem Projekt.

---

Konfigurieren der [Middleware](xref:fundamentals/middleware) können die Bereitstellung statischer Dateien.

### <a name="serve-files-inside-of-web-root"></a>Dateien Sie in der Webstamm

Aufrufen der [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) Methode innerhalb `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Die parameterlose `UseStaticFiles` -methodenüberladung, die Dateien im Webstammverzeichnis als servable markiert. Die folgenden Markup Verweise *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

### <a name="serve-files-outside-of-web-root"></a>Bereitstellen von Dateien außerhalb der Webstamm

Beachten Sie, eine Verzeichnishierarchie, in der statischen Dateien verarbeitet werden außerhalb der Webstamm befinden:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
      * *banner1.svg*

Eine Anforderung erreichen der *banner1.svg* Datei durch die Middleware für statische Dateien wie folgt konfigurieren:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Im vorangehenden Code der *MyStaticFiles* Verzeichnishierarchie wird öffentlich verfügbar gemacht über die *StaticFiles* URI-Segment. Eine Anforderung zum *http://\<einfügen > /StaticFiles/images/banner1.svg* dient der *banner1.svg* Datei.

Die folgenden Markup Verweise *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>HTTP-Antwortheader festlegen

Ein [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) Objekt kann verwendet werden, um HTTP-Antwortheader festlegen. Zusätzlich zum Konfigurieren von statischen dateibereitstellung über das Stammverzeichnis, den folgenden code wird die `Cache-Control` Header:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

Die [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) Methode vorhanden ist, der [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) Paket.

Die Dateien wurden für 10 Minuten (600 Sekunden) öffentlich zwischenspeicherbaren vorgenommen:

![Antwort-Header, die mit der Cache-Control-Header wurde hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Statische Autorisierung

Die Middleware für statische Dateien stellt keine autorisierungsprüfungen bereit. Alle Dateien von bedient, u. a. die *"Wwwroot"*, öffentlich zugänglich sind. Um Dateien anhand der Autorisierung zu dienen:

* Speichern Sie sie außerhalb von *"Wwwroot"* und einem beliebigen Verzeichnis zugegriffen werden kann, um die Middleware für statische Dateien **und**
* Verarbeiten Sie sie über eine Aktionsmethode, die auf die Autorisierung angewendet wird. Zurückgeben einer [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) Objekt:

[!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Aktiviert die Verzeichnissuche

Verzeichnissuche kann Benutzer Ihre Web-app finden in einer Verzeichnisliste und Dateien in einem angegebenen Verzeichnis. Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert (siehe [Überlegungen](#considerations)). Aktiviert Verzeichnissuche durch Aufrufen der [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) Methode in `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Fügen Sie die erforderlichen Dienste durch Aufrufen der [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) Methode `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Der vorangehende Code ermöglicht es, Verzeichnissuche, der die *"Wwwroot" / Bilder* Ordner mit der URL *http://\<einfügen > / MyImages*, mit Links zu einzelnen Dateien und Ordner:

![Verzeichnissuche](static-files/_static/dir-browse.png)

Finden Sie unter [Überlegungen](#considerations) auf die Sicherheitsrisiken beim Durchsuchen aktivieren.

Beachten Sie die beiden `UseStaticFiles` im folgenden Beispiel aufruft. Der erste Aufruf ermöglicht die Bereitstellung statischer Dateien in der *"Wwwroot"* Ordner. Der zweite Aufruf aktiviert die Verzeichnissuche, der die *"Wwwroot" / Bilder* Ordner mit der URL *http://\<einfügen > / MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Ein Standarddokument dienen

Festlegen einer Standard-Startseite bietet Besucher einen logischen Ausgangspunkt aus, wenn Ihre Site besuchen. Aufrufen, ohne dass der Benutzer, die vollständig qualifizieren den URI eine Standardseite helfen, die [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) Methode `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`muss aufgerufen werden, bevor `UseStaticFiles` Standarddatei dienen. `UseDefaultFiles`ist eine URL-Rewrite-Modul, das tatsächlich die Datei dienen nicht. Aktivieren Sie die Middleware für statische Dateien über `UseStaticFiles` auf die Datei bereitzustellen.

Mit `UseDefaultFiles`, Anforderungen an einen Ordner suchen nach:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Die erste Datei, die aus der Liste gefunden wird verarbeitet, als würde sich die Anforderung den voll gekennzeichneten URI. Die Browser-URL wird entsprechend der angeforderten URI fortgesetzt.

Der folgende Code ändert den Standardnamen für die Datei in *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) kombiniert die Funktionalität der `UseStaticFiles`, `UseDefaultFiles`, und `UseDirectoryBrowser`.

Der folgende Code ermöglicht die Bereitstellung statischer Dateien und die Standarddatei. Verzeichnissuche ist nicht aktiviert.

```csharp
app.UseFileServer();
```

Der folgende Code baut auf die parameterlose Überladung und Verzeichnissuche aktivieren:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Betrachten Sie die folgenden Verzeichnishierarchie aus:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
      * *banner1.svg*
  * *default.html*

Der folgende Code zu ermöglichen, statische Dateien und Standarddateien und Verzeichnissuche von `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser`muss aufgerufen werden, wenn die `EnableDirectoryBrowsing` Eigenschaftswert ist `true`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Mithilfe der Dateihierarchie und vorangehenden Codes, beheben URLs wie folgt:

| URI            |                             Antwort  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Wenn keine Datei mit dem Namen standardmäßig in existiert die *MyStaticFiles* Verzeichnis *http://\<einfügen > / StaticFiles* gibt die Verzeichnisliste mit klickbaren Links:

![Liste der statischen Dateien](static-files/_static/db2.png)

> [!NOTE]
> `UseDefaultFiles`und `UseDirectoryBrowser` verwenden Sie die URL *http://\<einfügen > / StaticFiles* ohne dem nachgestellten Schrägstrich auslösen eine clientseitige Umleitung zu *http://\<einfügen > / StaticFiles /*. Beachten Sie die nachstehenden Schrägstrich. Relativer URLs in den Dokumenten gelten ohne einen nachstehenden Schrägstrich ungültig.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Die [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) Klasse enthält eine `Mappings` Eigenschaft dient als eine Zuordnung von Dateierweiterungen, MIME-Inhaltstypen. Im folgenden Beispiel werden mehrere Dateierweiterungen bekannten MIME-Typen registriert. Die *.rtf* Erweiterung wird ersetzt, und *MP4* wird entfernt.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Finden Sie unter [MIME-Inhaltstypen](http://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Nicht standardmäßige Inhaltstypen

Die Middleware für statische Dateien erkennt fast 400 bekannten Dateiinhaltstypen. Wenn der Benutzer eine Datei mit einer unbekannten Dateityp anfordert, gibt die Middleware für statische Dateien Antworten HTTP 404 (Nichtgefunden) zurück. Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei angezeigt. Der URI zurückgegeben ein HTTP 404-Fehler.

Der folgende Code ermöglicht, dienen die unbekannte Typen und rendert die unbekannte Datei als Bild:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Das vorhergehende Codebeispiel wird eine Anforderung für eine Datei mit einem unbekannten Inhaltstyp als Bild zurückgegeben.

> [!WARNING]
> Aktivieren der [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stellt ein Sicherheitsrisiko dar. Es ist standardmäßig deaktiviert, und seine Verwendung wird abgeraten. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.

### <a name="considerations"></a>Weitere Überlegungen

> [!WARNING]
> `UseDirectoryBrowser`und `UseStaticFiles` kann Offenlegung von geheimen Schlüsseln. Deaktivieren von Verzeichnissuche in der Produktion wird dringend empfohlen. Überprüfen Sie sorgfältig, welche Verzeichnisse aktiviert sind, über `UseStaticFiles` oder `UseDirectoryBrowser`. Das gesamte Verzeichnis und seinen Unterverzeichnissen werden öffentlich zugegriffen werden kann. Store-Dateien für die Bereitstellung für die Öffentlichkeit geeignet ist in einem dedizierten Verzeichnis, z. B.  *\<Content_root > / "Wwwroot"*. Trennen Sie diese Dateien von MVC-Ansichten mit Razor-Pages (nur 2.x), Konfigurationsdateien usw. ein.

* Die URLs für Inhalte, die verfügbar gemacht, mit `UseDirectoryBrowser` und `UseStaticFiles` unterliegen die Groß-/Kleinschreibung und zeichenbeschränkungen des zugrunde liegenden Dateisystems. Windows ist z. B. Groß-/Kleinschreibung beachten&mdash;Mac und Linux nicht.

* ASP.NET Core apps gehostet wird, verwendet IIS die [ASP.NET Core Modul (ANCM)](xref:fundamentals/servers/aspnet-core-module) alle Anforderungen an die app, einschließlich statische dateianforderungen weitergeleitet. Die IIS-Handler für statische Dateien wird nicht verwendet. Er verfügt über keine Möglichkeit zum Verarbeiten von Anforderungen, bevor sie von der ANCM behandelt werden.

* Führen Sie die folgenden Schritte im IIS-Manager, um IIS-Handler für statische Dateien auf den Server oder die Website-Ebene zu entfernen:
    1. Navigieren Sie zu der **Module** Funktion.
    1. Wählen Sie **StaticFileModule** in der Liste.
    1. Klicken Sie auf **entfernen** in der **Aktionen** Randleiste.

> [!WARNING]
> Wenn die IIS-Handler für statische Dateien aktiviert ist **und** der ANCM ist falsch konfiguriert, um statische Dateien verarbeitet werden. Dies geschieht beispielsweise, wenn die *"Web.config"* Datei wird nicht bereitgestellt.

* Platzieren Sie die Codedateien (einschließlich *cs* und *cshtml*) außerhalb der Web-Stamm des app-Projekts. Eine logische Trennung wird zwischen der app die clientseitige Inhalt und serverbasierten Code aus diesem Grund erstellt. Dadurch wird verhindert, dass serverseitiger Code zugreifen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Middleware](xref:fundamentals/middleware)

* [Einführung in ASP.NET Core](xref:index)
