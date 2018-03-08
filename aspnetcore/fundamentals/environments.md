---
title: Arbeiten mit mehreren Umgebungen in ASP.NET Core
author: rick-anderson
description: "Erfahren Sie, wie ASP.NET Core Unterstützung für das Steuern des Verhaltens der app über mehrere Umgebungen bereitstellt."
ms.author: riande
manager: wpickett
ms.date: 12/25/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: becdfa647acb6483b39f5421ab881c4817f31c40
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="working-with-multiple-environments"></a>Arbeiten mit mehreren Umgebungen

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core bietet Unterstützung für das Verhalten der Anwendung zur Laufzeit mit Umgebungsvariablen festlegen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="environments"></a>Umgebungen

ASP.NET Core verweist auf eine bestimmte Umgebungsvariable `ASPNETCORE_ENVIRONMENT` die Umgebung beschreiben Sie die Anwendung im derzeit ausgeführt wird. Diese Variable kann festgelegt werden, auf einen beliebigen Wert gewünscht jedoch drei Werte werden gemäß der Konvention verwendet: `Development`, `Staging`, und `Production`. Finden Sie diese in den Beispielen verwendeten Werte und die Vorlagen mit ASP.NET Core bereitgestellt.

[!code-csharp[Main](environments/sample/WebApp1/Startup.cs?name=snippet)]

Der vorangehende Code:

* Aufrufe [UseDeveloperExceptionPage](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_DeveloperExceptionPageExtensions_UseDeveloperExceptionPage_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseBrowserLink](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.browserlinkextensions.usebrowserlink?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_BrowserLinkExtensions_UseBrowserLink_Microsoft_AspNetCore_Builder_IApplicationBuilder_) Wenn `ASPNETCORE_ENVIRONMENT` festgelegt ist, um `Development`.
* Aufrufe [UseExceptionHandler](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_ExceptionHandlerExtensions_UseExceptionHandler_Microsoft_AspNetCore_Builder_IApplicationBuilder_) Wenn der Wert der `ASPNETCORE_ENVIRONMENT` festgelegt ist, eine der folgenden:

    * `Staging`
    * `Production`
    * `Staging_2`

Die [Umgebung Tag Helper ](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) verwendet den Wert der `IHostingEnvironment.EnvironmentName` einschließen oder Ausschließen von Markup im Element:

[!code-html[Main](environments/sample/WebApp1/Pages/About.cshtml)]

Hinweis: Auf Windows- und Mac OS sind Umgebungsvariablen und die Werte nicht Groß-/Kleinschreibung beachtet. Linux-Umgebungsvariablen und Werte sind **Groß-/Kleinschreibung beachtet** standardmäßig.

### <a name="development"></a>Entwicklung

Die Entwicklungsumgebung kann Funktionen aktivieren, die in der Produktion verfügbar gemacht werden sollten nicht. Z. B. die Vorlagen für ASP.NET Core aktivieren die [Developer Ausnahmeseite](xref:fundamentals/error-handling#the-developer-exception-page) in der Entwicklungsumgebung.

Die Umgebung für die Entwicklung des lokalen Computers kann festgelegt werden, der *Properties\launchSettings.json* -Datei des Projekts. Umgebungswerte festgelegt *launchSettings.json* in die Umgebung für die festgelegte Werte zu überschreiben.

>[!WARNING]
> In Umgebungsvariablen gespeichert *launchSettings.json* nicht in keiner Weise gesichert werden und werden Teil der Quellcoderepository für Ihr Projekt aus, wenn Sie einen. **Nie Anmeldeinformationen oder anderen vertraulichen Daten in dieser Datei gespeichert werden.** Wenn Sie solche Daten speichern möchten, verwenden die *geheimen Manager* Tool beschrieben, [sichere Speicherung von app-Kennwörter während der Entwicklung](xref:security/app-secrets).

[!code-xml[Main](environments/sample/WebApp1/Properties/launchSettings.json?highlight=10,11,18,26)]

Beim Start der Anwendung mit `dotnet run`, das erste Profil mit `"commandName": "Project"` verwendet werden. Der Wert der `commandName` gibt den Webserver zu starten. `commandName`möglich:

* IIS Express
* IIS
* Projekt (die Kestrel gestartet)

Beim Start einer Anwendung mit `dotnet run`:

* *launchSettings.json* wird gelesen, falls verfügbar. `environmentVariables`Einstellungen im *launchSettings.json* Überschreiben von Umgebungsvariablen.
* Die Hostingumgebung wird angezeigt.


Die folgende Ausgabe zeigt eine app Einstieg `dotnet run`:
```bash
PS C:\Webs\WebApp1> dotnet run
Using launch settings from C:\Webs\WebApp1\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Webs\WebApp1
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio **Debuggen** Registerkarte finden Sie eine GUI so bearbeiten Sie die *launchSettings.json* Datei:

![Projekt Umgebungsvariablen Eigenschaften festlegen](environments/_static/project-properties-debug.png)

Änderungen an Projekt Profile können erst nach dem Neustart des Servers wirksam. Kestrel muss neu gestartet werden, bevor es an ihre Umgebung vorgenommenen Änderungen erkannt werden.

>[!WARNING]
> *launchSettings.json* geheime Schlüssel darf nicht speichern. Die [Secret-Manager-Tool](xref:security/app-secrets) Speichern von geheimen Schlüsseln für die lokale Entwicklung verwendet werden können.

### <a name="production"></a>Produktion

Die produktionsumgebung sollte konfiguriert werden, um Sicherheit, Leistung und Stabilität der Anwendung zu maximieren. Einige allgemeinen Einstellungen, die möglicherweise von eine produktiven Umgebung, die von der Entwicklung unterscheiden würde gehören:

* Das Zwischenspeichern.
* Die clientseitige Ressourcen sind gebündelt, verkleinert und potenziell von einem CDN bedient.
* Diagnose Fehlerseiten deaktiviert.
* Angezeigter Fehlerseiten aktiviert.
* Produktions-Protokollierung und Überwachung aktiviert. Beispielsweise [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/).

## <a name="setting-the-environment"></a>Einrichten der Umgebung

Es ist häufig nützlich, um eine bestimmte Umgebung zu Testzwecken festgelegt. Wenn die Umgebung nicht festgelegt ist, wird standardmäßig `Production` deaktiviert die meisten Debugfunktionen.

Die Methode zum Festlegen der umgebungs hängt vom Betriebssystem ab.

### <a name="azure"></a>Azure

Für Azure app Service:

* Wählen Sie die **Anwendungseinstellungen** Blatt.
* Fügen Sie die Schlüssel und Wert im **Anwendungseinstellungen**.


### <a name="windows"></a>Windows
Festlegen der `ASPNETCORE_ENVIRONMENT` für die aktuelle Sitzung, wenn die app gestartet wird mit `dotnet run`, werden die folgenden Befehle verwendet.

**Befehlszeile**
```
set ASPNETCORE_ENVIRONMENT=Development
```
**PowerShell**
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Diese Befehle wirksam, nur für das aktuelle Fenster. Wenn das Fenster geschlossen wird, wird die ASPNETCORE_ENVIRONMENT-Einstellung in der Standardeinstellung oder Computer Wert wiederhergestellt. Um den Wert global beim Öffnen von Windows Festlegen der **Systemsteuerung** > **System** > **Erweiterte Systemeinstellungen** und hinzufügen oder Bearbeiten der `ASPNETCORE_ENVIRONMENT` Wert.

![Erweiterte Eigenschaften System](environments/_static/systemsetting_environment.png)

![ASPNET-Core-Umgebungsvariable](environments/_static/windows_aspnetcore_environment.png)


**web.config**

Finden Sie unter der *Einstellungsumgebungsvariablen* Teil der [Konfigurationsverweis ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) Thema.

**Pro IIS-Anwendungspool**

Zum Festlegen von Umgebungsvariablen für die einzelnen apps, die in isolierten Anwendungspools (für IIS 10.0 und höher unterstützt) ausgeführt wird, finden Sie unter der *AppCmd.exe Befehl* Teil der [Umgebungsvariablen \< EnvironmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) Thema.

### <a name="macos"></a>macOS
Festlegen der aktuellen Umgebung für MacOS kann Inline erfolgen, wenn die Anwendung ausgeführt;

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
oder `export` vor dem Ausführen der app festlegen.

```bash
export ASPNETCORE_ENVIRONMENT=Development
```
Level-Umgebungsvariablen festgelegt sind, der *.bashrc* oder *.bash_profile* Datei. Bearbeiten Sie die Datei mit einem beliebigen Texteditor, und fügen Sie die folgende Anweisung hinzu.

```
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a>Linux
Verwenden Sie für Linux-Distributionen der `export` -Befehl an der Befehlszeile für variableneinstellungen sitzungsbasierte und *Bash_profile* -Datei für Computer auf umgebungseinstellungen.

### <a name="configuration-by-environment"></a>Konfiguration nach Umgebung

Finden Sie unter [Konfiguration von Umgebung](xref:fundamentals/configuration/index#configuration-by-environment) für Weitere Informationen.

<a name="startup-conventions"></a>
## <a name="environment-based-startup-class-and-methods"></a>Basierten Umgebung Startklasse und Methoden

Nach dem Start einer app ASP.NET Core der [Startklasse](xref:fundamentals/startup) startet die app. Wenn eine Klasse `Startup{EnvironmentName}` vorhanden ist, dass für diese Klasse aufgerufen werden `EnvironmentName`:

[!code-csharp[Main](environments/sample/WebApp1/StartupDev.cs?name=snippet&highlight=1)]

Hinweis: Aufrufen von [WebHostBuilder.UseStartup<TStartup> ](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup?view=aspnetcore-2.0#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) Konfigurationsabschnitte überschreibt.

[Konfigurieren Sie](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure?view=aspnetcore-2.0#Microsoft_AspNetCore_Hosting_StartupBase_Configure_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [ConfigureServices](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices?view=aspnetcore-2.0) Umgebung bestimmte Versionen des Formulars unterstützen `Configure{EnvironmentName}` und `Configure{EnvironmentName}Services`:

[!code-csharp[Main](environments/sample/WebApp1/Startup.cs?name=snippet_all&highlight=15,37)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Starten von Anwendungen](xref:fundamentals/startup)
* [Konfiguration](xref:fundamentals/configuration/index)
* [IHostingEnvironment.EnvironmentName](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname?view=aspnetcore-2.0#Microsoft_AspNetCore_Hosting_IHostingEnvironment_EnvironmentName)
