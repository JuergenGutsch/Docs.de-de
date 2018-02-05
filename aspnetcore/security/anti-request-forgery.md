---
title: "Verhindern von Cross-Site Request XSRF/Websiteübergreifender Anforderungsfälschung-Angriffen in ASP.NET Core"
author: steve-smith
description: "Verhindern von Cross-Site Request XSRF/Websiteübergreifender Anforderungsfälschung-Angriffen in ASP.NET Core"
manager: wpickett
ms.author: riande
ms.date: 7/14/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/anti-request-forgery
ms.openlocfilehash: 079c36535b8c9e7229952a2f7bcd53174effa6af
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2018
---
# <a name="preventing-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Verhindern von Cross-Site Request XSRF/Websiteübergreifender Anforderungsfälschung-Angriffen in ASP.NET Core

[Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), und [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-attack-does-anti-forgery-prevent"></a>Welche Angriff verhindert das fälschungssicherheitssystem auf einen?

Websiteübergreifende anforderungsfälschung (XSRF oder CSRF, auch bekannt als Aussprache *Siehe Surfen*) ist ein Angriff gegen Web gehostete Anwendungen, bei dem eine bösartige Website die Interaktion zwischen einem Clientbrowser und eine Website, die Vertrauensstellungen beeinflussen können, Dieser Browser. Diese Angriffe sind möglich, da Webbrowsern einige Arten von Authentifizierungstoken automatisch mit jeder Anforderung an eine Website zu senden. Diese Form der Exploit des auch bekannt als ein *einmalklick-Angriff* oder als *Sitzung riding*, da der Angriff nutzt des Benutzers zuvor den authentifizierten Sitzung.

Ein Beispiel von CSRF-Angriffen:

1. Ein Benutzer meldet sich bei `www.example.com`, mit Formularauthentifizierung.
2. Der Server authentifiziert den Benutzer und gibt eine Antwort, die ein Authentifizierungscookie enthält.
3. Der Benutzer besucht eine bösartige Website.

   Die bösartige Website enthält ein HTML-Formular, das etwa wie folgt:

```html
   <h1>You Are a Winner!</h1>
     <form action="http://example.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw" />
       <input type="hidden" name="Amount" value="1000000" />
     <input type="submit" value="Click Me"/>
   </form>
```

Beachten Sie, dass die formaktion an den Standort anfällig für nicht auf die bösartige Website sendet. Dies ist der "Cross-Site" Teil CSRF.

4. Der Benutzer klickt auf die Schaltfläche "Absenden". Der Browser schließt automatisch das Authentifizierungscookie für die angeforderte Domäne (in diesem Fall die anfällig für Standort) mit der Anforderung.
5. Die Anforderung auf dem Server mit dem Kontext des Benutzers Authentifizierung ausgeführt und kann Ausführen aller Aktionen, die ein authentifizierter Benutzer tun darf.

In diesem Beispiel wird der Benutzer auf die Schaltfläche klicken muss. Böswillige Seite konnte:

* Führen Sie ein Skript, das automatisch auf das Formular sendet.
* Sendet eine Formularübermittlung als eine AJAX-Anforderung. 
* Verwenden Sie ein ausgeblendetes Formular mit CSS ein. 

Verwendung von SSL nicht zu verhindern, dass eine CSRF-Angriffen, kann die bösartige Website senden eine `https://` Anforderung. 

Einige Angriffe abzielen, Standort-Endpunkte, auf die reagiert `GET` Anforderungen, in dem Fall Bildtag zum Ausführen der Aktion (diese Form des Angriffs befindet sich häufig auf Forum-Standorte, die Bilder ermöglichen jedoch blockieren JavaScript) verwendet werden kann. Anwendungen, die mit Statuswechsel `GET` Anforderungen vor böswilligen Angriffen anfällig sind.

CSRF-Angriffen sind möglich für Websites, die Cookies für die Authentifizierung verwenden, da der Browser alle relevanten Cookies an die Ziel-Website senden. CSRF-Angriffen sind jedoch nicht beschränkt auf Cookies ausnutzen. Beispielsweise Basis-und Digestauthentifizierung sind auch anfällig. Nachdem ein Benutzer mit Standard oder Digest-Authentifizierung anmeldet, sendet der Browser automatisch die Anmeldeinformationen an, bis die Sitzung beendet.

Hinweis: In diesem Kontext *Sitzung* bezieht sich auf die clientseitige-Sitzung, die während der Authentifizierung des Benutzers. Es ist unabhängig vom stagingstatus serverseitige Sitzungen oder [Sitzung Middleware](xref:fundamentals/app-state).

Benutzer können CSRF Schwachstellen durch erraten:
* Protokollierung von Websites, nach deren Verwendung.
* Löschen Sie ihren Browser-Cookies in regelmäßigen Abständen.

Allerdings sind CSRF Sicherheitsrisiken im Grunde ein Problem mit der Web-app, nicht für den Endbenutzer.

## <a name="how-does-aspnet-core-mvc-address-csrf"></a>Wie befasst Core ASP.NET-MVC CSRF?

> [!WARNING]
> ASP.NET Core implementiert anti-request-Fälschung mithilfe der [ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction). ASP.NET Core Datenschutz muss konfiguriert werden, um in einer Serverfarm zu arbeiten. Finden Sie unter [Konfigurieren von Data Protection](xref:security/data-protection/configuration/overview) für Weitere Informationen.

ASP.NET Core anti-request-Fälschung Data Protection Standardkonfiguration 

In ASP.NET Core MVC 2.0 die [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) fälschungssicherheitstoken für HTML-Formularelemente injiziert. Beispielsweise wird das folgende Markup in einer Razor-Datei automatisch fälschungssicherheitstoken generiert:

```html
<form method="post">
  <!-- form markup -->
</form>
```

Die automatische Generierung von fälschungssicherheitstoken für HTML-Formularelemente geschieht, wenn:

* Die `form` -Tag enthält die `method="post"` Attribut AND

  * Das Action-Attribut ist leer. ( `action=""`) OR
  * Das Action-Attribut nicht angegeben. (`<form method="post">`)

Sie können die automatische Generierung von fälschungssicherheitstoken für HTML-Formularelemente durch deaktivieren:

* Explizit deaktiviert `asp-antiforgery`. Beispiel:

 ```html
  <form method="post" asp-antiforgery="false">
  </form>
  ```

* Entscheiden Sie außerhalb des gültigen Tag Hilfsprogramme Form-Elements, indem Sie unter Verwendung des Hilfsprogramms Tag [! Ausschlussverfahren Symbol](xref:mvc/views/tag-helpers/intro#opt-out).

 ```html
  <!form method="post">
  </!form>
  ```

* Entfernen Sie die `FormTagHelper` aus der Sicht. Entfernen Sie die `FormTagHelper` aus einer Sicht, indem Sie die folgende Anweisung an die Razor-Ansicht:

 ```html
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor-Pages](xref:mvc/razor-pages/index) vor XSRF/CSRF automatisch geschützt sind. Sie müssen keinen zusätzlichen Code schreiben. Finden Sie unter [XSRF/CSRF und Razor-Pages](xref:mvc/razor-pages/index#xsrf) für Weitere Informationen.

Die am häufigsten verwendete Ansatz zum Schutz vor CSRF-Angriffen wird das token für die domänensynchronisierung-Muster (STP). STP ist eine Technik, die verwendet werden, wenn der Benutzer eine Seite mit Daten anfordert. Der Server sendet ein Token, das die Identität des aktuellen Benutzers an den Client zugeordnet. Der Client sendet das Token wieder an den Server für die Überprüfung. Wenn der Server ein Token, die Identität des authentifizierten Benutzers übereinstimmt empfängt, wird die Anforderung abgelehnt. Das Token ist eindeutig und unvorhersehbar. Das Token kann auch verwendet werden, um sicherzustellen, dass ordnungsgemäße Sequenzierung einer Reihe von Anforderungen (sicherstellen, dass Seite 1 vorausgeht Seite 2 vor der Seite "3"). Alle Formulare in ASP.NET Core MVC-Vorlagen generiert antiforgery-Token. Die folgenden zwei Beispiele für ansichtslogik antiforgery Token zu generieren:

```html
<form asp-controller="Manage" asp-action="ChangePassword" method="post">

</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    
}
```

Sie können eine antiforgery Token explizit hinzufügen eine ``<form>`` Element ohne das HTML-Hilfsobjekt Tag Hilfsprogramme mit ``@Html.AntiForgeryToken``:


```html
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

In jedem der vorangegangenen Fälle werden ASP.NET Core ein ausgeblendetes Formularfeld ähnlich der folgenden hinzugefügt:
```html
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkSldwD9CpLRyOtm6FiJB1Jr_F3FQJQDvhlHoLNJJrLA6zaMUmhjMsisu2D2tFkAiYgyWQawJk9vNm36sYP1esHOtamBEPvSk1_x--Sg8Ey2a-d9CV2zHVWIN9MVhvKHOSyKqdZFlYDVd69XYx-rOWPw3ilHGLN6K0Km-1p83jZzF0E4WU5OGg5ns2-m9Yw" />
```

ASP.NET Core umfasst drei [Filter](xref:mvc/controllers/filters) für die Arbeit mit antiforgery Token: ``ValidateAntiForgeryToken``, ``AutoValidateAntiforgeryToken``, und ``IgnoreAntiforgeryToken``.

<a name="vaft"></a>

### <a name="validateantiforgerytoken"></a>ValidateAntiForgeryToken

Die ``ValidateAntiForgeryToken`` ist ein Aktionsfilter, die auf einer einzelnen Aktion, die einen Controller angewendet werden kann oder Global. Anforderungen für Aktionen, die dieser Filter angewendet werden, wenn die Anforderung ein gültiges antiforgery Token enthält blockiert.

```c#
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();
    if (user != null)
    {
        var result = await _userManager.RemoveLoginAsync(user, account.LoginProvider, account.ProviderKey);
        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }
    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

Die ``ValidateAntiForgeryToken`` Attribut erfordert ein Token für Anforderungen an Aktionsmethoden, die es ausstattet, einschließlich `HTTP GET` Anforderungen. Allgemein gelten, überschreiben Sie diese mit der ``IgnoreAntiforgeryToken`` Attribut.

### <a name="autovalidateantiforgerytoken"></a>AutoValidateAntiforgeryToken

ASP.NET Core-apps nicht in der Regel antiforgery Token für die sichere HTTP-Methoden (GET, HEAD, Optionen und TRACE) generieren. Statt Allgemein anzuwenden der ``ValidateAntiForgeryToken`` -Attribut, und überschreiben diese mit ``IgnoreAntiforgeryToken`` Attribute, die Sie verwenden die ``AutoValidateAntiforgeryToken`` Attribut. Dieses Attribut funktioniert genauso wie die ``ValidateAntiForgeryToken`` Attribut, außer dass es keine Token für Anforderungen, die mit den folgenden HTTP-Methoden erforderlich:

* GET
* HEAD-,
* OPTIONEN
* TRACE

Es wird empfohlen, ``AutoValidateAntiforgeryToken`` weitgefasst gilt dies für nicht-API-Szenarien. Dadurch wird sichergestellt, dass Ihre Aktionen POST standardmäßig geschützt werden. Die Alternative besteht darin, antiforgery Token standardmäßig ignoriert, es sei denn, ``ValidateAntiForgeryToken`` auf die einzelnen Aktionsmethode angewendet wird. Es ist wahrscheinlicher in diesem Szenario für eine POST-Aktionsmethode, werden Links nicht geschützt ist, lassen Ihre app CSRF-Angriffe anfällig. Sogar anonyme Beiträge sollten antiforgery Token gesendet werden.

Hinweis: Die APIs nicht automatischen Mechanismus zum Senden des nicht-Cookie-Teils des Tokens verfügen; Ihre Implementierung hängen Ihre Client-Code-Implementierung wahrscheinlich. Einige Beispiele werden unten gezeigt.


Beispiel (auf Klassenebene):

```c#
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Beispiel (global):

```c#
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

<a name="iaft"></a>

### <a name="ignoreantiforgerytoken"></a>IgnoreAntiforgeryToken

Die ``IgnoreAntiforgeryToken`` Filter wird verwendet, um eine antiforgery Token für eine bestimmte Aktion (oder Domänencontroller) vorhanden ist erforderlich. Beim Anwenden dieses Filters wird überschreiben ``ValidateAntiForgeryToken`` und/oder ``AutoValidateAntiforgeryToken`` Filter, die auf einer höheren Ebene angegeben ist (Global oder auf einem Domänencontroller).

```c#
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
  [HttpPost]
  [IgnoreAntiforgeryToken]
  public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
  {
    // no antiforgery token required
  }
}
```

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX- und SPAs

In herkömmlichen HTML-basierten Anwendungen werden die antiforgery Token an den Server mithilfe von ausgeblendeten Formularfeldern übergeben. In modernen JavaScript-basierten apps und einseitige Anwendungen (SPAs) werden viele Anforderungen programmgesteuert vorgenommen. Diese AJAX-Anforderungen möglicherweise andere Techniken (z. B. Anforderungsheader oder Cookies) verwenden, um das Token zu senden. Wenn Cookies zum Speichern von Authentifizierungstoken und zum Authentifizieren von API-Anforderungen auf dem Server verwendet werden, werden CSRF ein potenzielles Problem hin. Jedoch wenn lokaler Speicher zum Speichern von Token verwendet wird, möglicherweise Sicherheitsrisiko FORGERY verringert werden, da die Werte aus dem lokalen Speicher nicht automatisch mit der jede neue Anforderung an den Server gesendet werden. Daher mit dem lokalen Speicher zum Speichern der antiforgery-Token auf dem Client und Senden des Tokens als ein Anforderungsheader und eine empfohlene Vorgehensweise ist.

### <a name="angularjs"></a>AngularJS

AngularJS verwendet stattdessen eine Konvention CSRF-Adresse. Wenn der Server ein Cookie mit dem Namen sendet ``XSRF-TOKEN``, das Drehmoment ``$http`` Dienst wird der Wert hinzufügen dieses Cookie einen Header beim Senden einer Anforderungs auf diesem Server. Dieser Prozess erfolgt automatisch; Sie müssen nicht explizit der Header festgelegt. Der Headername wird ``X-XSRF-TOKEN``. Der Server sollte dieser Header zu erkennen und seinen Inhalt zu überprüfen.

Arbeiten Sie mit dieser Konvention für ASP.NET Haupt-API:

* Konfigurieren Sie Ihre app so, geben Sie ein Token in einem Cookie wird aufgerufen``XSRF-TOKEN``
* Konfigurieren Sie den antiforgery Dienst für einen Header mit dem Namen gesucht werden soll.``X-XSRF-TOKEN``

```c#
services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
```

[Viewer-Beispiel](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample).

### <a name="javascript"></a>JavaScript

Verwenden von JavaScript mit Sichten, können Sie das Token mit einem Dienst in Ihrer Ansicht erstellen. Zu diesem Zweck fügen Sie der `Microsoft.AspNetCore.Antiforgery.IAntiforgery` -Dienst in der Ansicht, und rufen `GetAndStoreTokens`, wie dargestellt:

[!code-csharp[Main](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,24)]

Dadurch entfällt die Notwendigkeit so behandeln Sie direkt mit dem Festlegen von Cookies auf dem Server, oder sie vom Client gelesen werden.

JavaScript kann auch Zugriffstoken in Cookies bereitgestellt, und verwenden Sie das Cookie Inhalt zum Erstellen eines Headers mit dem Wert für das Token, wie unten dargestellt.

```c#
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
  new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Klicken Sie dann zum Senden von Token in einer Kopfzeile namens fordert vorausgesetzt, Sie erstellen das Skript ``X-CSRF-TOKEN``, konfigurieren Sie den antiforgery Dienst gesucht werden soll, für die ``X-CSRF-TOKEN`` Header:

```c#
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

Im folgenden Beispiel wird jQuery, um eine AJAX-Anforderung mit den entsprechenden Header zu machen:

```javascript
var csrfToken = $.cookie("CSRF-TOKEN");

$.ajax({
    url: "/api/password/changepassword",
    contentType: "application/json",
    data: JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }),
    type: "POST",
    headers: {
        "X-CSRF-TOKEN": csrfToken
    }
});
```

## <a name="configuring-antiforgery"></a>Konfigurieren von Antiforgery

`IAntiforgery`Stellt die API so konfigurieren Sie die antiforgery System bereit. Es muss angefordert werden der `Configure` Methode der `Startup` Klasse. Im folgenden Beispiel wird die Middleware auf der Startseite der app antiforgery-Token generieren und senden es in der Antwort als Cookie (mithilfe der Angular Dateinamenskonvention oben beschriebene):


```c#
public void Configure(IApplicationBuilder app, 
    IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;
        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // We can send the request token as a JavaScript-readable cookie, 
            // and Angular will use it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
    //
}
```

### <a name="options"></a>Optionen

Sie können anpassen, [antiforgery Optionen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions#fields_summary) in `ConfigureServices`:

```c#
services.AddAntiforgery(options => 
{
  options.CookieDomain = "mydomain.com";
  options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
  options.CookiePath = "Path";
  options.FormFieldName = "AntiforgeryFieldname";
  options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
  options.RequireSsl = false;
  options.SuppressXFrameOptionsHeader = false;
});
```

<!-- QAfix fix table -->

|Option        | Beschreibung |
|------------- | ----------- |
|CookieDomain  | Die Domäne des Cookies. Wird standardmäßig auf `null` festgelegt. |
|CookieName    | Der Name des Cookies. Wenn nicht festgelegt ist, das System eine eindeutige Namen, die mit beginnt generiert, die `DefaultCookiePrefix` (". AspNetCore.Antiforgery."). |
|CookiePath    | Der Pfad für das Cookie festgelegt. |
|FormFieldName | Der Name des Felds verborgene vom antiforgery System zum Rendern von antiforgery Token in Ansichten verwendet werden soll. |
|HeaderName    | Der Name des Headers vom antiforgery System verwendet werden soll. Wenn `null`, das System berücksichtigt nur Formulardaten. |
|RequireSsl    | Gibt an, ob SSL vom antiforgery System erforderlich ist. Wird standardmäßig auf `false` festgelegt. Wenn `true`, nicht-SSL-Anforderungen schlagen fehl. |
|SuppressXFrameOptionsHeader  | Gibt an, ob die Generierung von Unterdrücken der `X-Frame-Options` Header. Standardmäßig wird der Header mit einem Wert von "SAMEORIGIN" generiert. Wird standardmäßig auf `false` festgelegt. |

See https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions for more info.

### <a name="extending-antiforgery"></a>Erweitern von Antiforgery

Die [IAntiForgeryAdditionalDataProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) Typ ermöglicht Entwicklern das Verhalten des Anti-XSRF-Systems durch zusätzliche Round-Tripping-Daten in einzelnen Token zu erweitern. Die [GetAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_GetAdditionalData_Microsoft_AspNetCore_Http_HttpContext_) Methode wird jedes Mal aufgerufen, wenn ein Token wird generiert, und der Rückgabewert in das generierte Token eingebettet ist. Eine Implementierung konnte einen Zeitstempel, eine Nonce oder ein anderer Wert zurückgeben, und rufen dann [ValidateAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_ValidateAdditionalData_Microsoft_AspNetCore_Http_HttpContext_System_String_) dieser Daten validieren, wenn das Token überprüft wird. Der Client Benutzernamen ist bereits in die generierten Token eingebettet, daher keine Notwendigkeit besteht, diese Informationen umfassen. Wenn ein Token ergänzenden Daten, aber keine enthält `IAntiForgeryAdditionalDataProvider` wurde konfiguriert, die zusätzlichen Daten wird nicht überprüft.

## <a name="fundamentals"></a>Grundlagen

CSRF-Angriffen basieren auf dem Standardverhalten der Browser Cookies, die mit einer Domäne verknüpft sind, mit jeder Anforderung an, dass diese Domäne zu senden. Diese Cookies werden innerhalb des Browsers gespeichert. Sie enthalten häufig Sitzungscookies für authentifizierte Benutzer. Cookie-basierte Authentifizierung ist eine gängige Form der Authentifizierung. Token-basierte Authentifizierungssysteme haben in Beliebtheit, vor allem für SPAs und andere Szenarien "smart Client" zugenommen.

### <a name="cookie-based-authentication"></a>Cookie-basierte Authentifizierung

Sobald ein Benutzer mithilfe ihres Benutzernamens und Kennworts authentifiziert wurde, können sie ein Token ausgestellt hat, die verwendet werden kann, um sie zu identifizieren und überprüfen, dass sie authentifiziert wurden. Das Token wird als ein Cookie, die mit jeder Anforderung der Client stellt gespeichert. Generieren und überprüfen dieses Cookie wird von der cookieauthentifizierungsmiddleware ausgeführt. ASP.NET Core bietet Cookie [Middleware](xref:fundamentals/middleware/index) der serialisiert eines Benutzerprinzipals in einem verschlüsselten Cookie, und klicken Sie dann bei nachfolgenden Anforderungen überprüft das Cookie wird den Prinzipal erstellt, und weist sie der `User` Eigenschaft `HttpContext`.

Wenn ein Cookie verwendet wird, ist das Authentifizierungscookie lediglich als Container für das Formularauthentifizierungsticket. Das Ticket wird als Wert des Formularauthentifizierungscookies mit jeder Anforderung übergeben und dient Formularauthentifizierung auf dem Server beim Identifizieren eines authentifiziertes Benutzers verwendet.

Wenn ein Benutzer mit einem System angemeldet ist, wird eine Sitzung des Benutzers wird auf der Serverseite erstellt und in einer Datenbank oder einem anderen permanenten Speicher gespeichert ist. Das System generiert einen Sitzungsschlüssel, der auf die aktuelle Sitzung im Datenspeicher verweist, und sie als Client Side Cookie gesendet wird. Prüft der Webserver diesen Sitzungsschlüssel jedes Mal ein Benutzer eine Ressource anfordert, die Autorisierung erforderlich ist. Das System überprüft, ob die Sitzung zugeordneten Benutzer über die Berechtigung zum Zugriff auf die angeforderte Ressource verfügt. Wenn dies der Fall ist, wird die Anforderung fortgesetzt. Andernfalls gibt die Anforderung als nicht autorisierte zurück. Bei dieser Vorgehensweise Cookies werden verwendet, um die Anwendung angezeigt werden, um statusbehafteten werden, da er "merken" kann der Benutzer wurde zuvor authentifiziert mit dem Server.

### <a name="user-tokens"></a>Benutzertoken

Token-basierte Authentifizierung speichern keine Sitzung auf dem Server. Wenn ein Benutzer angemeldet ist, sind sie ein Token (nicht antiforgery Token) ausgegeben. Dieses Token enthält die Daten, die erforderlich ist, um das Token zu überprüfen. Es enthält auch Benutzerinformationen in Form von [Ansprüche](https://docs.microsoft.com/dotnet/framework/security/claims-based-identity-model). Wenn ein Benutzer möchte den Zugriff auf eine Serverressource, die eine Authentifizierung erforderlich ist, wird das Token mit einer zusätzlichen Authorization-Header in Form von Träger {Token} an den Server gesendet. Dadurch wird die Anwendung zustandslose, da das Token in jede nachfolgende Anforderung für die serverseitige Validierung in der Anforderung übergeben wird. Dieses Token wird nicht *verschlüsselte*; es *codiert*. Auf der Serverseite kann das Token für den Zugriff auf die unformatierten Informationen innerhalb der Token decodiert werden. Um das Token in nachfolgenden Anforderungen zu senden, muss entweder dieses im lokalen Speicher für den Browser oder in einem Cookie. Keine Gedanken Sie über XSRF Sicherheitsrisiko, wenn das Token im lokalen Speicher gespeichert ist, aber es ist ein Problem auf, wenn das Token in einem Cookie gespeichert ist.

### <a name="multiple-applications-are-hosted-in-one-domain"></a>Mehrere Anwendungen werden in einer Domäne gehostet werden.

Obwohl `example1.cloudapp.net` und `example2.cloudapp.net` sind verschiedene Hosts besteht ein implizites Vertrauensverhältnis zwischen Hosts unter der `*.cloudapp.net` Domäne. Diese implizite Vertrauensstellung kann potenziell nicht vertrauenswürdige Hosts Cookies gegenseitig beeinträchtigen (die gleichen-Origin-Richtlinien, die zum Steuern der AJAX-Anforderungen sind nicht unbedingt auf HTTP-Cookies anwendbar). Die ASP.NET Core-Laufzeit bietet einige Entschärfung, insofern, dass der Benutzername in das Feldtoken eingebettet ist. Auch wenn eine böswillige Unterdomäne einen Sitzungstoken überschrieben werden kann, kann nicht er ein gültiger Token für den Benutzer generieren. Wenn in einer derartigen Umgebung gehostet wird, können nicht die integrierte Anti-XSRF-Routinen weiterhin Sitzungsübernahme oder Anmeldung CSRF Verteidigung gegen Angriffe. Freigegebene Hostingumgebungen sind Vunerable Sitzungsübernahme CSRF-Anmeldung und andere Angriffe.

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) auf [öffnen Webanwendungsprojekt Sicherheit](https://www.owasp.org/index.php/Main_Page) (OWASP).
