---
title: Cookies zwischen apps freigeben
author: rick-anderson
description: Informationen zum Freigeben von Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core-apps.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/compatibility/cookie-sharing
ms.openlocfilehash: e87caa5ba78c6b4c365facc0dea07d747e7c9589
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="sharing-cookies-among-apps"></a>Cookies zwischen apps freigeben

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)

Websites bestehen häufig aus einzelnen Web-apps, die durch die Zusammenarbeit. Um ein einmaliges Anmelden für (Unternehmen SSO) zu bieten zu können, müssen die Web-apps innerhalb eines Standorts Authentifizierungscookies freigeben. Zur Unterstützung dieses Szenarios kann Data Protection Stapel Katana-Cookieauthentifizierung und ASP.NET Core Cookie Authentifizierungstickets freigeben.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/data-protection/compatibility/cookie-sharing/sample/) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

Im Beispiel werden alle drei apps, die Cookieauthentifizierung gemeinsam Cookie veranschaulicht:

* ASP.NET 2.0-Razor-Pages Core app ohne [ASP.NET Core Identity](xref:security/authentication/identity)
* ASP.NET Core 2.0 MVC-Anwendung mit ASP.NET Core Identität
* ASP.NET Framework 4.6.1 MVC-Anwendung mit ASP.NET Identity

In den folgenden Beispielen:

* Der Cookienamen für die Authentifizierung wird festgelegt, um einen allgemeinen Wert des `.AspNet.SharedCookie`.
* Die `AuthenticationType` festgelegt ist, um `Identity.Application` entweder explizit oder standardmäßig.
* [CookieAuthenticationDefaults.AuthenticationScheme](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationdefaults.authenticationscheme) is used as the authentication scheme. Die Konstante aufgelöst wird, auf den Wert `Cookies`.
* Die cookieauthentifizierungsmiddleware verwendet eine Implementierung des [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider). `DataProtectionProvider`bietet Datenschutzdienste für die Ver- und Entschlüsselung von Authentifizierungsdaten Cookie-Nutzlast an. Die `DataProtectionProvider` Instanz isoliert die Datenschutzsystem durch andere Teile der app verwendet wird.
  * Eine häufige [Daten Schutzschlüssels](xref:security/data-protection/implementation/key-management) Speicherort verwendet wird. Die Beispiel-app verwendet einen Ordner namens *Schlüsselsammlung* im Stammverzeichnis der Projektmappe, die Data Protection Schlüssel enthalten soll.
  * [DataProtectionProvider.Create(System.IO.DirectoryInfo)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_) accepts a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) for use with authentication cookies. Die Beispiel-app stellt den Pfad zu der *Schlüsselsammlung* Ordner `DirectoryInfo`.
  * [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) erfordert die [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet-Paket. Um dieses Paket für ASP.NET Core 2.0 und höher apps erhalten, verweisen die [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) Metapackage. Wenn .NET Framework abzielen, fügen Sie einen Paket Verweis auf `Microsoft.AspNetCore.DataProtection.Extensions`.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Authentifizierungscookies zwischen ASP.NET Core apps freigeben

Bei Verwendung von ASP.NET Core Identität:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

In der `ConfigureServices` -Methode, mit der [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) Erweiterungsmethode zum Einrichten des Data Protection-Diensts für Cookies.

[!code-csharp[Main](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

Finden Sie unter der *CookieAuthWithIdentity.Core* -Projekt in der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/data-protection/compatibility/cookie-sharing/sample/) ([zum Herunterladen von](xref:tutorials/index#how-to-download-a-sample)).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

In der `Configure` -Methode, mit der [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) einrichten:

* Der Data Protection-Dienst für Cookies.
* Die `AuthenticationScheme` entsprechend der ASP.NET 4.x.

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
{
    options.Cookies.ApplicationCookie.AuthenticationScheme = 
        "ApplicationCookie";

    var protectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"));

    options.Cookies.ApplicationCookie.DataProtectionProvider = 
        protectionProvider;

    options.Cookies.ApplicationCookie.TicketDataFormat = 
        new TicketDataFormat(protectionProvider.CreateProtector(
            "Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", 
            "Cookies", 
            "v2"));
});
```

---

Wenn Sie Cookies direkt verwenden:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

Finden Sie unter der *CookieAuth.Core* -Projekt in der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/data-protection/compatibility/cookie-sharing/sample/) ([zum Herunterladen von](xref:tutorials/index#how-to-download-a-sample)).

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
});
```

---

## <a name="encrypting-data-protection-keys-at-rest"></a>Verschlüsseln von Data Protection Schlüssel im Ruhezustand

Für produktionsbereitstellungen, konfigurieren die `DataProtectionProvider` im Ruhezustand mit DPAPI oder ein X509Certificate Schlüssel verschlüsseln. Finden Sie unter [Schlüssel Verschlüsselung ruhender](xref:security/data-protection/implementation/key-encryption-at-rest) für Weitere Informationen.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("thumbprint");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = DataProtectionProvider.Create(
        new DirectoryInfo(@"PATH_TO_KEY_RING"),
        configure =>
        {
            configure.ProtectKeysWithCertificate("thumbprint");
        })
});
```

---

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Freigeben von Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core-apps

ASP.NET 4.x-apps, die Katana-cookieauthentifizierungsmiddleware verwenden, können um Authentifizierungscookies zu generieren, die mit der ASP.NET Core cookieauthentifizierungsmiddleware kompatibel sind, konfiguriert werden. Dies ermöglicht eine große Site einzelne apps schrittweise beim Bereitstellen einer smooth benutzerumgebung am gesamten Standort aktualisieren.

> [!TIP]
> Wenn eine app Katana-cookieauthentifizierungsmiddleware verwendet, ruft er `UseCookieAuthentication` des Projekts *Startup.Auth.cs* Datei. ASP.NET 4.x-Web-app-Projekte mit Visual Studio 2013 erstellt und später die Katana-cookieauthentifizierungsmiddleware wird standardmäßig verwendet.

> [!NOTE]
> Eine ASP.NET 4.x-app muss als Ziel .NET Framework 4.5.1 oder höher. Andernfalls kann die nötigen NuGet-Pakete nicht installieren.

Um Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core apps gemeinsam nutzen, konfigurieren Sie die ASP.NET Core-app zu, wie bereits erwähnt, und konfigurieren Sie der ASP.NET 4.x-apps, indem Sie die folgenden Schritte aus.

1. Installieren Sie das Paket [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) in jeder ASP.NET 4.x-app.

2. In *Startup.Auth.cs*, suchen Sie den Aufruf von `UseCookieAuthentication` und ändern Sie sie wie folgt. Ändern Sie den Namen des Cookies mit dem Namen verwendet, die für die ASP.NET Core cookieauthentifizierungsmiddleware übereinstimmen. Geben Sie eine Instanz von einem `DataProtectionProvider` mit den allgemeinen Schutz Key Datenspeicherort initialisiert.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

Finden Sie unter der *CookieAuthWithIdentity.NETFramework* -Projekt in der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/data-protection/compatibility/cookie-sharing/sample/) ([zum Herunterladen von](xref:tutorials/index#how-to-download-a-sample)).

Zur Erstellung eine Benutzeridentität übereinstimmen der Authentifizierungstyp definierten Datentyps in einer `AuthenticationType` set mit `UseCookieAuthentication`.

*Models/IdentityModels.cs*:

[!code-csharp[Main](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Legen Sie die `CookieManager` für Interop `ChunkingCookieManager` damit das segmentierungsformat kompatibel ist.

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
    CookieName = ".AspNetCore.Cookies",
    // CookieName = ".AspNetCore.ApplicationCookie", (if using ASP.NET Identity)
    // CookiePath = "...", (if necessary)
    // ...
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create(
                new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware",
                "Cookies", 
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});
```

---

## <a name="use-a-common-user-database"></a>Eine allgemeine Benutzerdatenbank verwenden

Vergewissern Sie sich, dass das Identitätssystem für jede app in der gleichen Benutzerdatenbank gezeigt wird. Andernfalls führt das Identitätssystem Fehler zur Laufzeit, wenn er versucht, die Informationen in das Authentifizierungscookie anhand der Informationen in der Datenbank übereinstimmen.
