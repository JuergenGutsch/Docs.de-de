---
title: "Mithilfe der Cookieauthentifizierung ohne ASP.NET Core Identität"
author: rick-anderson
description: "Eine Erläuterung der Verwendung von Cookieauthentifizierung ohne ASP.NET Core Identity"
manager: wpickett
ms.author: riande
ms.date: 10/11/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/cookie
ms.openlocfilehash: ea9c93e34a3242b5b3716404228edb8902baf625
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="using-cookie-authentication-without-aspnet-core-identity"></a>Mithilfe der Cookieauthentifizierung ohne ASP.NET Core Identität

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)

Wie Sie in den früheren Authentifizierungsthemen gesehen haben [ASP.NET Core Identity](xref:security/authentication/identity) ist ein vollständige, voll ausgestatteten Authentifizierungsanbieter zum Erstellen und Verwalten von Anmeldungen. Allerdings sollten Sie Ihre eigenen benutzerdefinierten Authentifizierungslogik mit Zeiten Cookie-basierte Authentifizierung zu verwenden. Sie können als eigenständige Authentifizierungsanbieter ohne ASP.NET Core Identity Cookie-basierte Authentifizierung verwenden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

Informationen zum Migrieren von Cookie-basierte Authentifizierung von ASP.NET Core 1.x auf 2.0 finden Sie unter [Migrieren von Authentifizierung und Identität zu ASP.NET Core 2.0 Thema (Cookie-basierte Authentifizierung)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).

## <a name="configuration"></a>Konfiguration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Wenn Sie nicht die [Microsoft.AspNetCore.All Metapackage](xref:fundamentals/metapackage), installieren Sie Version 2.0 + von der [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet-Paket.

In der `ConfigureServices` -Methode den Authentifizierung-Middleware-Dienst mit dem Erstellen der `AddAuthentication` und `AddCookie` Methoden:

[!code-csharp[Main](cookie/sample/Startup.cs?name=snippet1)]

`AuthenticationScheme`übergeben zum `AddAuthentication` legt das Standard-Authentifizierungsschema für die app. `AuthenticationScheme`ist nützlich, wenn mehrere Instanzen der Cookieauthentifizierung vorhanden sind und Sie möchten [mit einem bestimmten Schema autorisieren](xref:security/authorization/limitingidentitybyscheme). Festlegen der `AuthenticationScheme` zu `CookieAuthenticationDefaults.AuthenticationScheme` "Cookies" der Wert für das Schema enthält. Sie können einen beliebigen Zeichenfolgenwert angeben, der das Schema unterscheidet.

In der `Configure` -Methode, mit der `UseAuthentication` Authentifizierung-Middleware aufgerufen werden soll, der festlegt der `HttpContext.User` Eigenschaft. Rufen Sie die `UseAuthentication` Methode vor dem Aufruf `UseMvcWithDefaultRoute` oder `UseMvc`:

[!code-csharp[Main](cookie/sample/Startup.cs?name=snippet2)]

**AddCookie-Optionen**

Die [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) Klasse wird verwendet, um die Anbieteroptionen für die Authentifizierung zu konfigurieren.

| Option | Beschreibung |
| ------ | ----------- |
| [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | Gibt den Pfad zu einer 302 gefunden (URL-Umleitung) zur Verfügung stellen, wenn von ausgelöst `HttpContext.ForbidAsync`. Der Standardwert ist `/Account/AccessDenied`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | Der Aussteller, verwenden Sie für die [Aussteller](/dotnet/api/system.security.claims.claim.issuer) Eigenschaft für Ansprüche, die vom Cookie Authentication-Dienst erstellt. |
| [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | Der Domänenname, in denen das Cookie bedient wird. Standardmäßig ist dies der Hostname der Anforderung. Der Browser sendet das Cookie nur bei den Anforderungen auf einen übereinstimmenden Hostnamen. Sie möchten möglicherweise passen Sie diese Option, um Cookies zu einem Host in Ihrer Domäne verfügen. Z. B. Festlegen der Domäne des Cookies auf `.contoso.com` zur Verfügung `contoso.com`, `www.contoso.com`, und `staging.www.contoso.com`. |
| [Cookie.Expiration](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.expiration?view=aspnetcore-2.0) | Ruft ab oder legt die Lebensdauer von Cookies. Aktuell, dies keine Ops option und veraltete Elemente in ASP.NET Core 2.1 + werden. Verwenden der `ExpireTimeSpan` Ablauf der Cookies festzulegende Option. Weitere Informationen finden Sie unter [verdeutlichen Verhalten des CookieAuthenticationOptions.Cookie.Expiration](https://github.com/aspnet/Security/issues/1293). |
| [Cookie.HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | Ein Flag, das angibt, ob das Cookie nur an Server zugegriffen werden soll. Durch Ändern dieses Werts auf `false` ermöglicht die clientseitige Skripts auf das Cookie zugreifen und möglicherweise Ihrer app das Cookie Diebstahl muss Ihrer app ein [siteübergreifendem Skripting (XSS)](xref:security/cross-site-scripting) Sicherheitsrisiko. Der Standardwert ist `true`. |
| [Cookie.Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | Legt den Namen des Cookies. |
| [Cookie.Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | Zum Isolieren von apps auf dem gleichen Hostnamen verwendet. Wenn Sie eine app zu ausgeführt `/app1` und möchten Cookies für diese Anwendung zu beschränken, legen die `CookiePath` Eigenschaft `/app1`. Auf diese Weise das Cookie steht nur für Anforderungen an `/app1` und jede app darunter. |
| [Cookie.SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | Gibt an, ob der Browser das Cookie an demselben Standort Anforderungen nur angefügt werden dürfen (`SameSiteMode.Strict`) oder Cross-Site-Anforderungen, die von sicheren HTTP-Methoden und derselben Website-Anforderungen (`SameSiteMode.Lax`). Bei Festlegung auf `SameSiteMode.None`, der Wert der Cookie-Header nicht festgelegt ist. Beachten Sie, dass [Cookie-Middleware bewirken die Richtlinie](#cookie-policy-middleware) möglicherweise den Wert überschreiben, die Sie bereitstellen. Um die OAuth-Authentifizierung zu unterstützen, der Standardwert ist `SameSiteMode.Lax`. Weitere Informationen finden Sie unter [OAuth-Authentifizierung aufgrund SameSite Cookierichtlinie unterteilt](https://github.com/aspnet/Security/issues/1231). |
| [Cookie.SecurePolicy](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | Ein Flag, das angibt, ob das Cookie auf HTTPS beschränkt werden soll (`CookieSecurePolicy.Always`), HTTP oder HTTPS (`CookieSecurePolicy.None`), oder das gleiche Protokoll wie die Anforderung (`CookieSecurePolicy.SameAsRequest`). Der Standardwert ist `CookieSecurePolicy.SameAsRequest`. |
| [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | Legt die `DataProtectionProvider` , dient zum Erstellen der standardmäßigen `TicketDataFormat`. Wenn die `TicketDataFormat` Eigenschaft festgelegt ist, die `DataProtectionProvider` Option wird nicht verwendet. Wenn nicht angegeben, wird die app Datenschutz-Standardanbieter verwendet. |
| [Ereignisse](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | Der Handler ruft Methoden für den Anbieter, der das app-Steuerelement an bestimmten Punkten Verarbeitung zu ermöglichen. Wenn `Events` sind nicht angegeben wird, eine Standardinstanz bereitgestellt wird, die keine Aktionen ausführt, wenn die Methoden aufgerufen werden. |
| [EventsType](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | Als den Diensttyp verwendet, um das Abrufen der `Events` Instanz statt mit der Eigenschaft. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | Die `TimeSpan` nach dem das Authentifizierungsticket gespeichert, in das Cookie abläuft. `ExpireTimeSpan`wird die aktuelle Uhrzeit auf die Ablaufzeit für das Ticket erstellen hinzugefügt. Die `ExpiredTimeSpan` Wert wird immer in der verschlüsselten Authentifizierungsticket, die vom Server überprüft. Es kann auch ein Wechsel in den [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) Header jedoch nur, wenn `IsPersistent` festgelegt ist. Festzulegende `IsPersistent` zu `true`, konfigurieren Sie die [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) übergeben `SignInAsync`. Der Standardwert von `ExpireTimeSpan` beträgt 14 Tage. |
| [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | Gibt den Pfad zu einer 302 gefunden (URL-Umleitung) zur Verfügung stellen, wenn von ausgelöst `HttpContext.ChallengeAsync`. Die aktuelle URL, die den Statuscode 401 generiert wird hinzugefügt, um die `LoginPath` als ein Abfragezeichenfolgen-Parameter mit dem Namen, indem Sie die `ReturnUrlParameter`. Einmal eine Anforderung an die `LoginPath` eine neue Identität anmelden gewährt der `ReturnUrlParameter` Wert wird verwendet, um den Browser zurück an die URL umgeleitet, die den ursprünglichen Statuscode nicht autorisiert verursacht hat. Der Standardwert ist `/Account/Login`. |
| [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | Wenn die `LogoutPath` wird bereitgestellt, um den Handler auf, dann eine Anforderung an diesen Pfad leitet basierend auf dem Wert, der die `ReturnUrlParameter`. Der Standardwert ist `/Account/Logout`. |
| [ReturnUrlParameter](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | Legt den Namen des Abfragezeichenfolgen-Parameters, der durch den Handler für eine 302 gefunden (URL-Umleitung) Antwort angefügt wird. `ReturnUrlParameter`wird verwendet, wenn eine Anforderung eingeht, auf die `LoginPath` oder `LogoutPath` den Browser die ursprüngliche URL zurückgegeben, nachdem die an- bzw. Abmeldung Aktion ausgeführt wird. Der Standardwert ist `ReturnUrl`. |
| [SessionStore](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | Ein optionaler Container verwendet, um die Identität anforderungsübergreifend gespeichert. Wenn verwendet, wird nur ein Sitzungsbezeichner an den Client gesendet. `SessionStore`kann verwendet werden, um potenzielle Probleme mit großen Identitäten zu verringern. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | Ein Flag, das angibt, ob ein neues Cookie mit einer aktualisierten Ablaufzeit dynamisch ausgegeben werden soll. Dies kann für jede Anforderung vorkommen, in denen das aktuelle Cookie Ablaufdatum mehr als 50 % abgelaufen ist. Neuen Ablaufdatum vorwärts verschoben wird, werden das aktuelle Datum plus dem `ExpireTimespan`. Ein [Cookie absolute Ablaufzeit](xref:security/authentication/cookie#absolute-cookie-expiration) kann festgelegt werden, mithilfe der `AuthenticationProperties` -Klasse beim Aufrufen von `SignInAsync`. Eine absolute Ablaufzeit kann verbessern die Sicherheit Ihrer App beschränken die Zeitspanne, die das Authentifizierungscookie gültig ist. Der Standardwert ist `true`. |
| [TicketDataFormat](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | Die `TicketDataFormat` dient zum Schützen und Aufheben des Schutzes der Identität und andere Eigenschaften, die im Cookiewert gespeichert sind. Wenn nicht angegeben wird, eine `TicketDataFormat` erstellt, wobei die [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0). |
| [Validate](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | Methode, die überprüft, dass die Optionen gültig sind. |

Legen Sie `CookieAuthenticationOptions` in der Dienstkonfiguration für die Authentifizierung in der `ConfigureServices` Methode:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

ASP.NET Core 1.x verwendet Cookies [Middleware](xref:fundamentals/middleware/index) , die einen Benutzerprinzipal in einem verschlüsselten Cookie serialisiert. Bei nachfolgenden Anforderungen das Cookie wird überprüft, und der Prinzipal neu erstellt und zugewiesen wird die `HttpContext.User` Eigenschaft.

Installieren der [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet-Paket im Projekt. Dieses Paket enthält die Cookie-Middleware.

* `AuthenticationScheme`-Dies ist ein Wert, der unter dem ein bestimmtes Cookie-Authentifizierungsschema bekannt ist. Dies ist nützlich, wenn mehrere der Cookieauthentifizierung und der app muss Instanzen [Beschränken der Autorisierung mit einer Instanz](xref:security/authorization/limitingidentitybyscheme).

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

**CookieAuthenticationOptions-Optionen**

Die [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) Klasse wird verwendet, um die Anbieteroptionen für die Authentifizierung zu konfigurieren.

| Option | Beschreibung |
| ------ | ----------- |
| [AuthenticationScheme](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | Legt das Authentifizierungsschema fest. `AuthenticationScheme`ist nützlich, wenn mehrere Instanzen von Authentifizierung vorhanden sind und Sie möchten [mit einem bestimmten Schema autorisieren](xref:security/authorization/limitingidentitybyscheme). Festlegen der `AuthenticationScheme` zu `CookieAuthenticationDefaults.AuthenticationScheme` "Cookies" der Wert für das Schema enthält. Sie können einen beliebigen Zeichenfolgenwert angeben, der das Schema unterscheidet. |
| [AutomaticAuthenticate](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | Legt einen Wert aus, um anzugeben, dass die Cookieauthentifizierung für jede Anforderung ausgeführt werden soll, und versuchen, zu überprüfen und serialisierten Prinzipal selbst erstellten zu rekonstruieren. |
| [AutomaticChallenge](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | Bei "true", behandelt die authentifizierungsmiddleware automatische Herausforderungen. Wenn "false", die authentifizierungsmiddleware nur Antworten, wenn dies ausdrücklich angegeben ändert die `AuthenticationScheme`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | Der Aussteller, verwenden Sie für die [Aussteller](/dotnet/api/system.security.claims.claim.issuer) Eigenschaft für Ansprüche, die von der cookieauthentifizierungsmiddleware erstellt. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | Der Domänenname, in denen das Cookie bedient wird. Standardmäßig ist dies der Hostname der Anforderung. Der Browser dient nur das Cookie auf einen übereinstimmenden Hostnamen. Sie möchten möglicherweise passen Sie diese Option, um Cookies zu einem Host in Ihrer Domäne verfügen. Z. B. Festlegen der Domäne des Cookies auf `.contoso.com` zur Verfügung `contoso.com`, `www.contoso.com`, und `staging.www.contoso.com`. |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | Ein Flag, das angibt, ob das Cookie nur an Server zugegriffen werden soll. Durch Ändern dieses Werts auf `false` ermöglicht die clientseitige Skripts auf das Cookie zugreifen und möglicherweise Ihrer app das Cookie Diebstahl muss Ihrer app ein [siteübergreifendem Skripting (XSS)](xref:security/cross-site-scripting) Sicherheitsrisiko. Der Standardwert ist `true`. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | Zum Isolieren von apps auf dem gleichen Hostnamen verwendet. Wenn Sie eine app zu ausgeführt `/app1` und möchten Cookies für diese Anwendung zu beschränken, legen die `CookiePath` Eigenschaft `/app1`. Auf diese Weise das Cookie steht nur für Anforderungen an `/app1` und jede app darunter. |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | Ein Flag, das angibt, ob das Cookie auf HTTPS beschränkt werden soll (`CookieSecurePolicy.Always`), HTTP oder HTTPS (`CookieSecurePolicy.None`), oder das gleiche Protokoll wie die Anforderung (`CookieSecurePolicy.SameAsRequest`). Der Standardwert ist `CookieSecurePolicy.SameAsRequest`. |
| [Beschreibung](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | Weitere Informationen zu den Authentifizierungstyp für die app zur Verfügung gestellt werden. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | Die `TimeSpan` nach dem das Authentifizierungsticket abläuft. Es wird die aktuelle Uhrzeit auf die Ablaufzeit für das Ticket erstellen hinzugefügt. Mit `ExpireTimeSpan`, müssen Sie festlegen `IsPersistent` auf `true` in der `AuthenticationProperties` übergeben `SignInAsync`. Der Standardwert beträgt 14 Tage. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | Ein Flag, das angibt, ob das Ablaufdatum der Cookie wird, wenn mehr als die Hälfte der zurückgesetzt der `ExpireTimeSpan` Intervall ist verstrichen. Die neue Exipiration Zeit vorwärts verschoben ist, werden das aktuelle Datum plus dem `ExpireTimespan`. Ein [Cookie absolute Ablaufzeit](xref:security/authentication/cookie#absolute-cookie-expiration) kann festgelegt werden, mithilfe der `AuthenticationProperties` -Klasse beim Aufrufen von `SignInAsync`. Eine absolute Ablaufzeit kann verbessern die Sicherheit Ihrer App beschränken die Zeitspanne, die das Authentifizierungscookie gültig ist. Der Standardwert ist `true`. |

Legen Sie `CookieAuthenticationOptions` für die Cookieauthentifizierungsmiddleware in die `Configure` Methode:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

---

## <a name="cookie-policy-middleware"></a>Cookie-Middleware bewirken die Richtlinie

[Cookie-Middleware bewirken die Richtlinie](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) Cookie Richtlinie Funktionen in einer app ermöglicht. Der app-Verarbeitungspipeline die Middleware hinzugefügt ist die Reihenfolge, die vertrauliche; Er wirkt sich nur auf Komponenten, die nach ihm in der Pipeline registriert.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 Die [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) bereitgestellt, um die Cookie-Middleware Richtlinie können Sie globale Eigenschaften der Cookie-Verarbeitung und Hook in Cookie Verarbeitungshandler steuern, wenn Cookies angefügt oder gelöscht werden.

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| [HttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | Wirkt sich auf, ob Cookies HttpOnly sein müssen, die ein Flag, der angibt, wenn das Cookie nur an Server zugegriffen werden soll. Der Standardwert ist `HttpOnlyPolicy.None`. |
| [MinimumSameSitePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | Wirkt sich auf das Cookie gleicher Attribut (siehe unten). Der Standardwert ist `SameSiteMode.Lax`. Diese Option ist verfügbar für ASP.NET Core 2.0 +. |
| [OnAppendCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | Wird aufgerufen, wenn ein Cookie angehängt wird. |
| [OnDeleteCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | Wird aufgerufen, wenn ein Cookie gelöscht wird. |
| [Secure](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | Bestimmt, ob Cookies Secure sein müssen. Der Standardwert ist `CookieSecurePolicy.None`. |

**MinimumSameSitePolicy** (ASP.NET 2.0 + nur Kern)

Die Standardeinstellung `MinimumSameSitePolicy` Wert `SameSiteMode.Lax` "oauth2" Authentifizierung zulassen. Um eine Richtlinie gleichen Standort vom streng erzwingen `SameSiteMode.Strict`legen die `MinimumSameSitePolicy`. Obwohl diese Einstellung "oauth2" und andere Cross-Origin-Authentifizierungsschemas unterbrochen wird, erhöht die Cookies für andere Arten von apps, die Cross-Origin-anforderungsverarbeitung benötigen keine Sicherheitsebene.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Die Einstellung der Cookie-Middleware bewirken die Richtlinie für `MinimumSameSitePolicy` kann Auswirkungen auf die Einstellung für `Cookie.SameSite` in `CookieAuthenticationOptions` Einstellungen entsprechend der Matrix unten.

| MinimumSameSitePolicy | Cookie.SameSite | Resultierende Cookie.SameSite-Einstellung |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="creating-an-authentication-cookie"></a>Erstellen ein Authentifizierungscookie

Um ein Cookie mit Benutzerinformationen zu erstellen, erstellen Sie eine [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). Die Benutzerinformationen serialisiert und im Cookie gespeichert. 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Erstellen einer ["ClaimsIdentity"](/dotnet/api/system.security.claims.claimsidentity) mit allen erforderlichen [Anspruch](/dotnet/api/system.security.claims.claim)s, und rufen [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) der Benutzer zur Anmeldung:

[!code-csharp[Main](cookie/sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Rufen Sie [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) der Benutzer zur Anmeldung:

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

---

`SignInAsync`ein verschlüsseltes Cookies erstellt, und der aktuellen Antwort hinzugefügt. Wenn Sie nicht angeben einer `AuthenticationScheme`, wird das Standardschema verwendet.

Im Hintergrund, wird die Verschlüsselung verwendet ASP.NET Core des [Datenschutz](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) System. Wenn Sie sind auf mehreren Computern, den Lastenausgleich für apps oder mit einer Webfarm app hosten, müssen Sie [Schutz von Daten konfigurieren](xref:security/data-protection/configuration/overview) und mit dem gleichen Schlüssel Ring app-Bezeichner.

## <a name="signing-out"></a>Abmelden

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Rufen Sie den aktuellen Benutzer abmelden, und löschen ihre Cookies, [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):

[!code-csharp[Main](cookie/sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Rufen Sie den aktuellen Benutzer abmelden, und löschen ihre Cookies, [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

---

Wenn Sie nicht `CookieAuthenticationDefaults.AuthenticationScheme` (oder "Cookies") als Formate (z. B. "ContosoCookie"), geben Sie das Schema, das Sie beim Konfigurieren des Authentifizierungsanbieters verwendet. Andernfalls ist das Standardschema verwendet.

## <a name="reacting-to-back-end-changes"></a>Das reagieren auf Back-End-Änderungen

Sobald ein Cookie erstellt wurde, wird es die einzige Quelle der Identität. Auch wenn Sie einen Benutzer in Ihrem Back-End-Systemen deaktivieren, die Cookie-Authentifizierungssystem hat keine Informationen über diese, und ein Benutzer angemeldet bleibt, solange ihre Cookie gültig ist.

Die [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) Ereignis in ASP.NET Core 2.x oder [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) Methode in ASP.NET Core 1.x abfangen und Überschreiben der Überprüfung der Identität des Cookies verwendet werden können. Dieser Ansatz reduziert das Risiko gesperrte Benutzer Zugriff auf die app.

Ein Ansatz für die gültigkeitsprüfung für Cookies basiert auf Nachverfolgen der bei die Datenbank geändert wurde. Wenn die Datenbank nicht geändert wurde, da der Benutzer Cookie ausgestellt wurde, besteht keine Notwendigkeit, den Benutzer erneut zu authentifizieren, wenn ihre Cookie noch gültig ist. Zum Implementieren dieses Szenarios, die Datenbank, die in implementiert wird `IUserRepository` in diesem Beispiel speichert ein `LastChanged` Wert. Wenn jeder Benutzer in der Datenbank aktualisiert wird der `LastChanged` Wert auf die aktuelle Uhrzeit festgelegt wird.

Um ein Cookie für ungültig zu erklären, wenn Änderungen an der Datenbank auf Grundlage der `LastChanged` -Wert, erstellen Sie das Cookie mit einer `LastChanged` Anspruch mit dem aktuellen `LastChanged` Wert aus der Datenbank:

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Implementieren Sie eine Außerkraftsetzung für die `ValidatePrincipal` Ereignis, Schreiben Sie eine Methode mit der folgenden Signatur in einer Klasse, die Sie ableiten [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Ein Beispiel sieht wie folgt aus:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Registrieren die Instanz Ereignisse während der Cookie-Service-Registrierung in der `ConfigureServices` Methode. Geben Sie eine Bereichsbezogene Service-Registrierung für Ihre `CustomCookieAuthenticationEvents` Klasse:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Implementieren Sie eine Außerkraftsetzung für die `ValidateAsync` Ereignis, Schreiben Sie eine Methode mit der folgenden Signatur:

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

ASP.NET Core Identity implementiert diese Überprüfung als Teil seiner [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync). Ein Beispiel sieht wie folgt aus:

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Registrieren Sie das Ereignis während der Konfiguration der Cookie-Authentifizierung in der `Configure` Methode:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

---

Betrachten Sie eine Situation, in dem der Name des Benutzers aktualisiert &mdash; eine Entscheidung, die Sicherheit in keiner Weise beeinflussen nicht. Wenn Sie den Benutzerprinzipalnamen zerstörungsfrei aktualisieren möchten, rufen Sie `context.ReplacePrincipal` und legen Sie die `context.ShouldRenew` Eigenschaft `true`.

> [!WARNING]
> Die hier beschriebene Vorgehensweise wird bei jeder Anforderung ausgelöst. Dies kann in großen Leistungseinbußen bei der app führen.

## <a name="persistent-cookies"></a>Permanente cookies

Sie sollten das Cookie über Browsersitzungen hinweg beibehalten werden. Dieser Persistenz sollte nur mit expliziten benutzerzustimmung mit einem Kontrollkästchen "Anmeldedaten" auf den Anmeldenamen oder einen ähnlichen Mechanismus aktiviert werden. 

Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das über Browser Abschlüsse überdauert. Alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben, werden berücksichtigt. Wenn das Cookie abläuft, während der Browser geschlossen wird, löscht der Browser das Cookie an, nach dem Neustart wird.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Die [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) Klasse befindet sich in der `Microsoft.AspNetCore.Authentication` Namespace.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

Die [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) Klasse befindet sich in der `Microsoft.AspNetCore.Http.Authentication` Namespace.

---

## <a name="absolute-cookie-expiration"></a>Absolute cookieablauf

Sie können festlegen, dass eine absolute Ablaufzeit mit `ExpiresUtc`. Sie müssen auch festlegen `IsPersistent`ist, andernfalls `ExpiresUtc` wird ignoriert, und ein einzelnes Sitzungscookie wird erstellt. Wenn `ExpiresUtc` auf festgelegt ist `SignInAsync`, er überschreibt den Wert für die `ExpireTimeSpan` -Option von `CookieAuthenticationOptions`, sofern festgelegt.

Der folgende Codeausschnitt erstellt eine Identität und die entsprechenden Cookie, das 20 Minuten dauert. Dies ignoriert alle gleitenden ablaufeinstellungen, die zuvor konfiguriert haben.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

---

## <a name="see-also"></a>Siehe auch

* [Auth 2.0 Änderungen / Migration Ankündigung](https://github.com/aspnet/Announcements/issues/262)
* [Einschränken der Identität nach Schema](xref:security/authorization/limitingidentitybyscheme)
