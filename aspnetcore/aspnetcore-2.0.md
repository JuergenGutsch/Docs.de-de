---
title: Neuigkeiten in ASP.NET Core 2.0
author: rick-anderson
description: Neuigkeiten in ASP.NET Core 2.0
ms.author: riande
manager: wpickett
ms.date: 07/10/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: aspnetcore-2.0
ms.openlocfilehash: ca02de7954002dcbc125898c9f570438f88db818
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
# <a name="whats-new-in-aspnet-core-20"></a>Neuigkeiten in ASP.NET Core 2.0

In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 2.0 hervorgehoben und Links zu relevanter Dokumentation bereitgestellt.

## <a name="razor-pages"></a>Razor-Pages

Razor-Pages ist ein neues Feature von ASP.NET Core MVC, mit dem codierungsseitige Szenarios einfacher und produktiver werden.

Weitere Informationen finden Sie in der Einführung und im Tutorial:

* [Introduction to Razor Pages (Einführung in Razor-Pages)](xref:mvc/razor-pages/index)
* [Getting started with Razor Pages (Erste Schritte mit Razor-Pages)](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Core-Metapaket

Ein neues ASP.NET Core-Metapaket umfasst alle Pakete, die von den ASP.NET Core- und Entity Framework Core-Teams erstellt und unterstützt werden, sowie deren interne Abhängigkeiten und Abhängigkeiten von Drittanbietern. Sie müssen nicht länger einzelne Microsoft ASP.NET Core-Funktionen nach Paket auswählen. Alle Funktionen sind im Paket [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) enthalten. Die Standardvorlagen verwenden dieses Paket.

Weitere Informationen finden Sie unter [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0 (Microsoft.AspNetCore.All-Metapaket für ASP.NET Core 2.0)](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Laufzeitspeicher

Anwendungen, die das `Microsoft.AspNetCore.All`-Metapaket verwenden, profitieren automatisch vom neuen .NET Core-Laufzeitspeicher. Der Speicher enthält alle Runtime-Objekte, die zum Ausführen von ASP.NET Core 2.0-Anwendungen erforderlich sind. Durch die Verwendung des `Microsoft.AspNetCore.All`-Metapakets werden keine Objekte von referenzierten ASP.NET Core NuGet-Paketen mit der Anwendung bereitgestellt, da sich diese bereits im Zielsystem befinden. Die Objekte im Laufzeitspeicher sind ebenso zur Verbesserung der Startzeit der Anwendung vorkompiliert.

Weitere Informationen finden Sie unter [Runtime store (Laufzeitspeicher)](https://docs.microsoft.com/dotnet/core/deploying/runtime-store).

## <a name="net-standard-20"></a>.NET-Standard 2.0

Die ASP.NET Core 2.0-Pakete sind auf .NET Standard 2.0 ausgelegt. Auf die Pakete kann von anderen .NET Standard 2.0-Bibliotheken verwiesen werden. Außerdem können sie unter Implementierungen von .NET ausgeführt werden, die mit .NET Standard 2.0 konform sind, darunter .NET Core 2.0 und .NET Framework 4.6.1. 

Das `Microsoft.AspNetCore.All`-Metapaket ist ausschließlich auf .NET Core 2.0 ausgelegt, da es dafür vorgesehen ist, mit dem .NET Core 2.0-Laufzeitspeicher verwendet zu werden.

## <a name="configuration-update"></a>Aktualisierung der Konfiguration

Standardmäßig wird in ASP.NET Core 2.0 eine `IConfiguration`-Instanz zum Dienstcontainer hinzugefügt. Die `IConfiguration`-Instanz im Dienstcontainer macht es Anwendungen leichter, Konfigurationswerte aus dem Container abzurufen.

Weitere Informationen zum Status der geplanten Dokumentation finden Sie unter [GitHub-Problem](https://github.com/aspnet/Docs/issues/3387).

## <a name="logging-update"></a>Aktualisierung der Protokollierung

In ASP.NET Core 2.0 wird die Protokollierung standardmäßig in das System der Abhängigkeitsinjektion (dependency injection, DI) integriert. Sie können Anbieter hinzufügen und Filter in der Datei *Program.cs* statt in der Datei *Startup.cs* konfigurieren. Die standardmäßige `ILoggerFactory` unterstützt die Filterung so, dass Sie einen flexiblen Ansatz für die anbieterübergreifende Filterung und die Filterung nach bestimmten Anbietern verwenden können.

Weitere Informationen finden Sie unter [Introduction to Logging (Einführung in die Protokollierung)](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Aktualisierung der Authentifizierung

Ein neues Authentifizierungsmodell erleichtert das Konfigurieren der Authentifizierung für eine Anwendung mithilfe der Abhängigkeitsinjektion.

Es sind neue Vorlagen für die Konfiguration der Authentifizierung für Web-Apps und Web-APIs mithilfe von [Azure AD B2C] verfügbar (https://azure.microsoft.com/services/active-directory-b2c/).

Weitere Informationen zum Status der geplanten Dokumentation finden Sie unter [GitHub-Problem](https://github.com/aspnet/Docs/issues/3054).

## <a name="identity-update"></a>Aktualisierung der Identität

Wir haben die Erstellung sicherer Web-APIs mithilfe von Identität in ASP.NET Core 2.0 vereinfacht. Sie erhalten Zugriffstoken für den Zugriff auf Ihre Web-APIs über die [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Weitere Informationen zu Änderungen bei der Authentifizierung in 2.0 finden Sie in den folgenden Ressourcen:

* [Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)](xref:security/authentication/accconfirm)
* [Enabling QR Code generation for authenticator apps in ASP.NET Core (Aktivieren der QR-Code-Generierung für Authentifikator-Apps in ASP.NET Core)](xref:security/authentication/identity-enable-qrcodes)
* [Migrating Authentication and Identity to ASP.NET Core 2.0 (Migration von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0)](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>SPA-Vorlagen

Es sind Projektvorlagen der Einzelseitenanwendung (Single Page Application, SPA) für Angular, Aurelia, Knockout.js, React.js und React.js mit Redux verfügbar. Die Angular-Vorlage wurde auf Angular 4 aktualisiert. Die Vorlagen für Angular und React sind standardmäßig verfügbar. Weitere Informationen zum Abrufen der anderen Vorlagen finden Sie unter [Creating a new SPA project (Erstellen eines neuen SPA-Projekts)](xref:client-side/spa-services#creating-a-new-project). Informationen zum Erstellen einer SPA in ASP.NET Core finden Sie unter [Using JavaScriptServices for Creating Single Page Applications (Verwendung von JavaScriptServices zum Erstellen von einseitigen Anwendungen)](xref:client-side/spa-services).

## <a name="kestrel-improvements"></a>Kestrel-Verbesserungen

Der Kestrel-Webserver verfügt über neue Funktionen, die ihn geeigneter als mit dem Internet verbundenen Server machen. Wir haben der neuen Eigenschaft `Limits` der `KestrelServerOptions`-Klasse eine Reihe von Optionen für die Servereinschränkungskonfiguration hinzugefügt. Nun können Sie Grenzwerte für Folgendes hinzufügen:

- Maximale Anzahl der Clientverbindungen
- Maximale Größe des Anforderungstexts
- Minimale Datenrate des Anforderungstexts

Weitere Informationen finden Sie unter [Kestrel web server implementation in ASP.NET Core (Kestrel-Webserverimplementierung in ASP.NET Core)](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>Umbenennung von WebListener in HTTP.sys

Die Pakete `Microsoft.AspNetCore.Server.WebListener` und `Microsoft.Net.Http.Server` wurden in einem neuen Paket zusammengeführt: `Microsoft.AspNetCore.Server.HttpSys`. Die Namespaces wurden entsprechend aktualisiert.

Weitere Informationen finden Sie unter [HTTP.sys web server implementation in ASP.NET Core (HTTP.sys-Webserverimplementierung in ASP.NET Core)](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Verbesserte Unterstützung von HTTP-Headern

Wenn Sie MVC zum Übertragen von `FileStreamResult` oder `FileContentResult` verwenden, können Sie jetzt ein `ETag`- oder `LastModified`-Datum für den Inhalt festlegen, den Sie übertragen. Sie können diese Werte auf die zurückgegebenen Inhalte durch Code festlegen, ähnlich wie hier gezeigt:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Die Datei, die an Ihre Besucher zurückgegeben wird, wird mit den entsprechenden HTTP-Headern für die Werte `ETag` und `LastModified` ergänzt.

Wenn ein Besucher der Anwendung Inhalt mit einem Range-Anforderungsheader anfordert, erkennt ASP.NET das und verarbeitet diesen Header. Wenn der angeforderte Inhalt nur teilweise geliefert werden kann, überspringt ASP.NET diese Teile entsprechend und gibt nur den angeforderte Bytesatz zurück.  Sie müssen keine bestimmten Handler in Ihren Methoden schreiben, um dieses Feature anzupassen oder zu verarbeiten, da dies automatisch für Sie erledigt wird.

## <a name="hosting-startup-and-application-insights"></a>Hosting – Start und Application Insights

Hostingumgebungen können nun während des Starts der Anwendung zusätzliche Paketabhängigkeiten injizieren und Code ausführen. Die Anwendung muss nicht explizit eine Abhängigkeit übernehmen oder Methoden aufrufen. Mit dieser Funktion kann bestimmten Umgebungen ermöglicht werden, Funktionen zu „starten“, die für diese Umgebung spezifisch sind, ohne dass die Anwendung vorher informiert sein muss. 

In ASP.NET Core 2.0 wird diese Funktion verwendet, um automatisch die Application Insights-Diagnose beim Debuggen in Visual Studio oder (nach der Anmeldung) bei der Ausführung in Azure App Services zu aktivieren. Aus diesem Grund fügen die Projektvorlagen nicht mehr standardmäßig Application Insights-Pakete und Code hinzu.

Weitere Informationen zum Status der geplanten Dokumentation finden Sie unter [GitHub-Problem](https://github.com/aspnet/Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Automatische Verwendung von Fälschungssicherheitstoken

ASP.NET Core hat Sie standardmäßig immer bei der HTML-Codierung Ihres Inhalts unterstützt. Mit der neuen Version gehen wir noch einen Schritt weiter, um websiteübergreifende Anforderungsfälschungen (XSRF) zu verhindern. ASP.NET Core gibt nun standardmäßig Fälschungssicherheitstoken aus und validiert diese ohne zusätzliche Konfiguration auf POST-Aktionsformularen und -Seiten.

Weitere Informationen finden Sie unter [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks in ASP.NET Core (Verhindern von websiteübergreifenden Anforderungsfälschungen (XSRF/CSRF) in ASP.NET Core)](xref:security/anti-request-forgery).

## <a name="automatic-precompilation"></a>Automatisches Vorkompilieren

Die Razor-Ansichtenvorkompilierung ist standardmäßig während der Veröffentlichung aktiviert. Hierdurch wird die Größe der Ausgabe reduziert und die Startzeit der Anwendung verringert.

## <a name="razor-support-for-c-71"></a>Razor-Unterstützung für C# 7.1

Das Razor-Anzeigemodul wurde aktualisiert, damit es mit dem neuen Roslyn-Compiler funktioniert. Dies umfasst Unterstützung für C# 7.1-Funktionen wie Standardausdrücke, abgeleitete Tupelnamen und Mustervergleich mit Generika. Fügen Sie Ihrer Projektdatei die folgende Eigenschaft hinzu, um C# 7.1 in Ihrem Projekt zu verwenden, und laden Sie die Projektmappe dann erneut:

```xml
<LangVersion>latest</LangVersion>
```

Informationen zum Status der C# 7.1-Funktionen finden Sie im [Roslyn GitHub-Repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Andere Aktualisierungen der Dokumentation für 2.0

* [Visual Studio-Veröffentlichungsprofile für die Bereitstellung von ASP.NET Core-Apps](xref:host-and-deploy/visual-studio-publish-profiles)
* [Schlüsselverwaltung](xref:security/data-protection/implementation/key-management)
* [Configuring Facebook authentication (Konfigurieren der Facebook-Authentifizierung)](xref:security/authentication/facebook-logins)
* [Configuring Twitter authentication (Konfigurieren der Twitter-Authentifizierung)](xref:security/authentication/twitter-logins)
* [Configuring Google authentication (Konfigurieren der Google-Authentifizierung)](xref:security/authentication/google-logins)
* [Configuring Microsoft Account authentication (Konfigurieren der Microsoft-Kontoauthentifizierung)](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Migrationsanleitung

Anleitungen zum Migrieren von ASP.NET Core 1.x-Anwendungen zu ASP.NET Core 2.0 finden Sie in den folgenden Ressourcen:

* [Migrating from ASP.NET Core 1.x to ASP.NET Core 2.0 (Migration von ASP.NET Core 1.x zu ASP.NET Core 2.0)](xref:migration/1x-to-2x/index)
* [Migrating Authentication and Identity to ASP.NET Core 2.0 (Migration von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0)](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Zusätzliche Informationen

Die vollständige Liste aller Änderungen finden Sie unter [ASP.NET Core 2.0 Release Notes (ASP.NET Core 2.0 – Anmerkungen zu dieser Version)](https://github.com/aspnet/Home/releases/tag/2.0.0).

Wenn Sie über den Fortschritt und die Pläne des ASP.NET Core-Entwicklungsteams auf dem Laufenden bleiben wollen, sehen Sie sich das wöchentliche [ASP.NET Community Standup](https://live.asp.net/) an.
