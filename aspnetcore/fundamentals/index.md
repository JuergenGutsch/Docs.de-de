---
title: "ASP.NET Core – Grundlagen"
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen einer ASP.NET Core-Anwendung kennen.
keywords: "ASP.NET Core, Grundlagen, Übersicht"
ms.author: riande
manager: wpickett
ms.date: 09/30/2017
ms.topic: get-started-article
ms.assetid: a19b7836-63e4-44e8-8250-50d426dd1070
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/index
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e707bb92b2d8b1776ae2970001f1699248580e5f
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2017
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core – Grundlagen

Eine ASP.NET Core-Anwendung ist eine Konsolenanwendung, in deren `Main`-Methode ein Webserver erstellt wird:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs)]

Die `Main`-Methode ruft die `WebHost.CreateDefaultBuilder`-Methode auf, die nach dem Builder-Muster einen Webanwendungshost erstellt. Der Builder verfügt über Methoden, die den Webserver (z.B. `UseKestrel`) und die Startklasse (`UseStartup`) definieren. Im obigen Beispiel wird der [Kestrel](xref:fundamentals/servers/kestrel)-Webserver automatisch zugeordnet. Wenn IIS verfügbar ist, wird versucht, den Webhost von ASP.NET Core auf IIS auszuführen. Andere Webserver wie [HTTP.sys](xref:fundamentals/servers/httpsys) können durch den Aufruf der entsprechenden Erweiterungsmethode verwendet werden. `UseStartup` wird im nächsten Abschnitt näher erläutert.

Der Rückgabetyp `IWebHostBuilder` des `WebHost.CreateDefaultBuilder`-Aufrufs stellt viele optionale Methoden zur Verfügung. Zu diesen Methoden gehören `UseHttpSys` für das Hosten der Anwendung in HTTP.sys und `UseContentRoot` für das Festlegen des Stamminhaltsverzeichnisses. Mit den Methoden `Build` und `Run` wird das `IWebHost`-Objekt erstellt, das die Anwendung hostet und auf HTTP-Anforderungen lauscht.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs)]

Die `Main`-Methode verwendet eine Instanz von `WebHostBuilder`, die nach dem Builder-Muster einen Webanwendungshost erstellt. Der Builder verfügt über Methoden, die den Webserver (z.B. `UseKestrel`) und die Startklasse (`UseStartup`) definieren. Im obigen Beispiel wird der [Kestrel](xref:fundamentals/servers/kestrel)-Webserver verwendet. Andere Webserver wie [WebListener](xref:fundamentals/servers/weblistener) können durch den Aufruf der entsprechenden Erweiterungsmethode verwendet werden. `UseStartup` wird im nächsten Abschnitt näher erläutert.

`WebHostBuilder` stellt viele optionale Methoden wie z.B. `UseIISIntegration` für das Hosten in IIS und IIS Express und `UseContentRoot` für das Festlegen des Inhaltsstammverzeichnisses zur Verfügung. Mit den Methoden `Build` und `Run` wird das `IWebHost`-Objekt erstellt, das die Anwendung hostet und auf HTTP-Anforderungen lauscht.

---

## <a name="startup"></a>Start

Die `UseStartup`-Methode in `WebHostBuilder` gibt die `Startup`-Klasse für Ihre Anwendung an:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program2x.cs?highlight=10&range=6-17)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](../getting-started/sample/aspnetcoreapp/Program.cs?highlight=7&range=6-17)]

---

In der `Startup`-Klasse definieren Sie die Pipeline zur Anforderungsverarbeitung. Außerdem werden in dieser Klasse alle von der Anwendung benötigten Dienste konfiguriert. Die `Startup`-Klasse muss öffentlich sein und die folgenden Methoden enthalten:

```csharp
public class Startup
{
    // This method gets called by the runtime. Use this method
    // to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // This method gets called by the runtime. Use this method
    // to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
    }
}
```

`ConfigureServices` definiert die [Dienste](#dependency-injection-services) Ihrer Anwendung (z.B. ASP.NET Core MVC, Entity Framework Core, Identity). `Configure` definiert die [Middleware](xref:fundamentals/middleware) für die Anforderungspipeline.

Weitere Informationen finden Sie unter [Application startup (Starten von Anwendungen)](xref:fundamentals/startup).

## <a name="content-root"></a>Inhaltsstammverzeichnis

Das Inhaltsstammverzeichnis ist der Basispfad zu allen von der Anwendung verwendeten Inhalten wie Ansichten, [Razor-Seiten](xref:mvc/razor-pages/index) und statischen Objekten. Standardmäßig entspricht das Inhaltsstammverzeichnis dem Anwendungsbasispfad der ausführbaren Datei, mit der die Anwendung gehostet wird.

## <a name="web-root"></a>Webstammverzeichnis

Das Webstammverzeichnis einer Anwendung ist das Projektverzeichnis, in dem sich öffentliche statische Ressourcen wie etwa CSS-, JavaScript- und Bilddateien befinden.

## <a name="dependency-injection-services"></a>Abhängigkeitsinjektion (Dienste)

Ein Dienst ist eine Komponente, die für die gemeinsame Nutzung in einer Anwendung vorgesehen ist. Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung gestellt. ASP.NET Core enthält einen nativen IoC-Container (**I**nversion **o**f **C**ontrol, Steuerungsumkehr), der die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) standardmäßig unterstützt. Bei Bedarf können Sie den nativen Standardcontainer durch einen anderen ersetzen. Neben der losen Kopplung besteht ein weiterer Vorteil darin, dass durch die Abhängigkeitsinjektion Dienste in der gesamten Anwendung zur Verfügung gestellt werden (z.B. die [Protokollierung](xref:fundamentals/logging)).

Weitere Informationen finden Sie unter [Dependency injection (Abhängigkeitsinjektion)](xref:fundamentals/dependency-injection).

## <a name="middleware"></a>Middleware

In ASP.NET Core erstellen Sie Ihre Anforderungspipeline mithilfe von [Middleware](xref:fundamentals/middleware/index). ASP.NET Core-Middleware führt asynchrone Logikoperationen im `HttpContext` aus und ruft anschließend die nächste Middlewareanwendung in der Sequenz auf oder beendet die Anforderung direkt. Eine Middlewarekomponente mit dem Namen „XYZ“ wird durch den Aufruf einer `UseXYZ`-Erweiterungsmethode in der `Configure`-Methode hinzugefügt.

ASP.NET Core enthält standardmäßig zahlreiche Middlewareanwendungen:

* [Statische Dateien](xref:fundamentals/static-files)
* [Routing](xref:fundamentals/routing)
* [Authentifizierung](xref:security/authentication/index)
* [Antworten komprimierende Middleware](xref:performance/response-compression)
* [URL-umschreibende Middleware](xref:fundamentals/url-rewriting)

Jede auf [OWIN](http://owin.org) basierende Middleware steht für ASP.NET Core zur Verfügung. Darüber hinaus können Sie auch Ihre eigene benutzerdefinierte Middleware erstellen.

ASP.NET Core verfügt über Features zum Routing von App-Anforderung an Routenhandler.

Weitere Informationen finden Sie unter [Routing](xref:fundamentals/routing).

## <a name="file-providers"></a>Dateianbieter

ASP.NET Core abstrahiert den Dateisystemzugriff mithilfe von Dateianbietern, wodurch eine einfache Schnittstelle zum plattformübergreifenden Arbeiten mit Dateien zur Verfügung gestellt wird.

Weitere Informationen finden Sie unter [Dateianbieter](xref:fundamentals/file-providers).

## <a name="static-files"></a>Statische Dateien

Middleware für statische Dateien kümmert sich um statische Dateien wie etwa HTML, CSS, Image und JavaScript.

Weitere Informationen finden Sie unter [Arbeiten mit statischen Dateien](xref:fundamentals/static-files).

## <a name="hosting"></a>Hosting

ASP.NET Core-Apps konfigurieren und starten einen *Host*, der für das Starten der App und das Verwalten deren Lebensdauer verantwortlich ist.

Weitere Informationen finden Sie unter [Hosting](xref:fundamentals/hosting).

## <a name="session-and-application-state"></a>Sitzungs- und Anwendungszustand

Sitzungszustand ist ein Feature in ASP.NET Core, das Sie verwenden können, um Benutzerdaten zu speichern, während der Benutzer Ihre Web-App durchsucht.

Weitere Informationen finden Sie unter [Session and application state (Sitzungs- und Anwendungszustand)](xref:fundamentals/app-state).

## <a name="environments"></a>Umgebungen

Umgebungen wie „Entwicklung“ und „Produktion“ sind in ASP.NET Core von besonderer Bedeutung und können über Umgebungsvariablen festgelegt werden.

Weitere Informationen finden Sie unter [Working with Multiple Environments (Verwenden von mehreren Umgebungen)](xref:fundamentals/environments).

## <a name="configuration"></a>Konfiguration

ASP.NET Core verwendet ein Konfigurationsmodell, das auf Name/Wert-Paaren basiert. Das Konfigurationsmodell basiert weder auf `System.Configuration` noch auf *web.config*. Die Konfiguration erhält Einstellungen von einer geordneten Menge an Konfigurationsanbietern. Die integrierten Konfigurationsanbieter unterstützen zahlreiche Dateiformate (XML, JSON, INI) und Umgebungsvariablen, durch die eine umgebungsbasierte Konfiguration ermöglicht wird. Sie können auch benutzerdefinierte Konfigurationsanbieter erstellen.

Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration).

## <a name="logging"></a>Protokollierung

ASP.NET Core unterstützt eine Protokollierungs-API, die mit mehreren verschiedenen Protokollanbietern funktioniert. Integrierte Anbieter unterstützen das Senden von Protokollen an mindestens einen Zielanbieter. Protokollierungsframeworks von Drittanbietern sind ebenso zulässig.

[Logging (Protokollierung)](xref:fundamentals/logging)

## <a name="error-handling"></a>Fehlerbehandlung

ASP.NET Core verfügt über integrierte Features zur Fehlerbehandlung in Apps. Dazu zählen die Ausnahmenseite für Entwickler, Seiten für benutzerdefinierte Fehler, Seiten für statischen Statuscode sowie die Startausnahmebehandlung.

Weitere Informationen finden Sie unter [Fehlerbehandlung](xref:fundamentals/error-handling).

## <a name="routing"></a>Routing

ASP.NET Core verfügt über Features zum Routing von App-Anforderung an Routenhandler.

Weitere Informationen finden Sie unter [Routing](xref:fundamentals/routing).

## <a name="file-providers"></a>Dateianbieter

ASP.NET Core abstrahiert den Dateisystemzugriff mithilfe von Dateianbietern, wodurch eine einfache Schnittstelle zum plattformübergreifenden Arbeiten mit Dateien zur Verfügung gestellt wird.

Weitere Informationen finden Sie unter [Dateianbieter](xref:fundamentals/file-providers).

## <a name="static-files"></a>Statische Dateien

Middleware für statische Dateien kümmert sich um statische Dateien wie etwa HTML, CSS, Image und JavaScript.

Weitere Informationen finden Sie unter [Arbeiten mit statischen Dateien](xref:fundamentals/static-files).

## <a name="hosting"></a>Hosting

ASP.NET Core-Apps konfigurieren und starten einen *Host*, der für das Starten der App und das Verwalten deren Lebensdauer verantwortlich ist.

Weitere Informationen finden Sie unter [Hosting](xref:fundamentals/hosting).

## <a name="session-and-application-state"></a>Sitzungs- und Anwendungszustand

Sitzungszustand ist ein Feature in ASP.NET Core, das Sie verwenden können, um Benutzerdaten zu speichern, während der Benutzer Ihre Web-App durchsucht.

Weitere Informationen finden Sie unter [Session and application state (Sitzungs- und Anwendungszustand)](xref:fundamentals/app-state).

## <a name="servers"></a>Server

Das Hostingmodell von ASP.NET Core lauscht nicht direkt auf Anforderungen. Das Hostingmodell ist darauf angewiesen, dass eine HTTP-Serverimplementierung die Anforderungen an die App weiterleitet. Die weitergeleitete Anforderung wird von Funktionsobjekten umschlossen, auf die Sie über Schnittstellen zugreifen können. ASP.NET Core enthält den verwalteten, plattformübergreifenden Webserver [Kestrel](xref:fundamentals/servers/kestrel). Kestrel wird in der Regel hinter einem Produktionswebserver wie [IIS](https://www.iis.net/) oder [nginx](http://nginx.org) ausgeführt. Kestrel kann als Edgeserver ausgeführt werden.

Weitere Informationen finden Sie unter [Server](xref:fundamentals/servers/index) und in den folgenden Themen:

* [Kestrel](xref:fundamentals/servers/kestrel)
* [ASP.NET Core-Modul](xref:fundamentals/servers/aspnet-core-module)
* [HTTP.sys](xref:fundamentals/servers/httpsys) (früher [WebListener](xref:fundamentals/servers/weblistener))

## <a name="globalization-and-localization"></a>Globalisierung und Lokalisierung

Wenn Sie eine mehrsprachige Website mit ASP.NET Core erstellen, können Sie ein breiteres Publikum erreichen. ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.

Weitere Informationen finden Sie unter [Globalisierung und Lokalisierung](xref:fundamentals/localization).

## <a name="request-features"></a>Anforderungsfeatures

Ausführliche Informationen zur Webserverimplementierung, die mit HTTP-Anforderungen und -antworten in Verbindung stehen, werden in Schnittstellen definiert. Diese Schnittstellen werden von Serverimplementierungen und Middleware verwendet, um die Hostingpipeline der App zu erstellen und anzupassen.

Weitere Informationen finden Sie unter [Request Features (Anforderungsfeatures)](xref:fundamentals/request-features).

## <a name="open-web-interface-for-net-owin"></a>Open Web Interface for .NET (OWIN)

ASP.NET Core unterstützt Open Web Interface for .NET (OWIN). Mit OWIN können Web-Apps von Webservern entkoppelt werden.

Weitere Informationen finden Sie unter [Open Web Interface for .NET (OWIN)](xref:fundamentals/owin).

## <a name="websockets"></a>WebSockets

Bei [WebSockets](https://wikipedia.org/wiki/WebSocket) handelt es sich um ein Protokoll, das bidirektionale persistente Kommunikationskanäle über TCP-Verbindungen ermöglicht. Es wird in Chat-, Börsenticker- und Spiele-Apps sowie überall dort verwendet, wo Echtzeitfunktionen in einer Web-App benötigt werden. ASP.NET Core unterstützt WebSockets-Features.

Weitere Informationen finden Sie unter [WebSockets](xref:fundamentals/websockets).

## <a name="microsoftaspnetcoreall-metapackage"></a>Metapaket „Microsoft.AspNetCore.All“

Das Metapaket [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) für ASP.NET Core enthält:

* alle unterstützten Pakete des ASP.NET Core-Teams
* alle unterstützten Pakete von Entity Framework Core 
* interne und Drittanbieterabhängigkeiten, die von ASP.NET Core und Entity Framework Core verwendet werden

Weitere Informationen finden Sie unter [Microsoft.AspNetCore.All metapackage (Microsoft.AspNetCore.All-Metapaket)](xref:fundamentals/metapackage).

## <a name="net-core-vs-net-framework-runtime"></a>.NET Core-Runtime im Vergleich zur .NET Framework-Laufzeit

Eine ASP.NET Core-Anwendung kann für die .NET Core- oder .NET Framework-Laufzeit entwickelt werden.

Weitere Informationen finden Sie unter [Wahl zwischen .NET Core und .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="choose-between-aspnet-core-and-aspnet"></a>Wählen zwischen ASP.NET und ASP.NET Core

Weitere Informationen, die Ihnen die Wahl zwischen ASP.NET Core und ASP.NET erleichtern, finden Sie unter [Wählen zwischen ASP.NET Core und ASP.NET](xref:fundamentals/choose-between-aspnet-and-aspnetcore).
