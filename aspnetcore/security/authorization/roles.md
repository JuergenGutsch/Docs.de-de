---
title: Rollenbasierte Autorisierung
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/roles
ms.openlocfilehash: 3dfba8a492c5d592b1fa9c8893a0ec4b1a2e70b9
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="role-based-authorization"></a>Rollenbasierte Autorisierung

<a name="security-authorization-role-based"></a>

Beim Erstellen eine Identität kann es auf eine oder mehrere Rollen gehören. Beispielsweise kann der Tracy Administrator- und Rollen gehören, Masse Scott nur der Benutzerrolle angehören. Wie diese Rollen erstellt und verwaltet werden, hängt von dem Sicherungsspeicher des Autorisierungsprozesses ab. Rollen werden verfügbar gemacht, für den Entwickler über die [IsInRole](https://docs.microsoft.com/dotnet/api/system.security.principal.genericprincipal.isinrole) Methode für die [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) Klasse.

## <a name="adding-role-checks"></a>Hinzufügen von rollenüberprüfungen

Rollenbasierte autorisierungsüberprüfungen sind deklarative&mdash;Entwickler bettet sie innerhalb ihres Codes für einen Controller oder einer Aktion innerhalb eines Controllers angeben von Rollen, die der aktuelle Benutzer ein Mitglied sein muss, auf die angeforderte Ressource zuzugreifen.

Z. B. der folgende Code, schränkt den Zugriff auf alle Aktionen auf der `AdministrationController` sind für Benutzer ein Mitglied der `Administrator` Rolle:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Sie können mehrere Rollen als durch Trennzeichen getrennte Liste angeben:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Dieser Controller wäre nur zugegriffen werden kann, von Benutzern, die Mitglieder von der `HRManager` Rolle oder die `Finance` Rolle.

Wenn Sie mehrere Attribute gelten muss ein Zugriff auf Benutzer ein Mitglied aller angegebenen Rollen; Im folgende Beispiel erfordert, dass ein Benutzer ein Mitglied sowohl der `PowerUser` und `ControlPanelUser` Rolle.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Sie können weiter Zugriff einschränken, indem Sie zusätzliche Autorisierung benutzerrollenattribute Ebene der Aktion anwenden:

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

In den vorherigen Code Snippet Membern der der `Administrator` Rolle oder die `PowerUser` Rolle kann den Controller zugreifen und die `SetTime` Aktion, aber nur Mitglieder der der `Administrator` Rolle zugreifen kann die `ShutDown` Aktion.

Sie können auch einen Controller Sperren jedoch anonyme, nicht authentifizierte Zugriff auf einzelne Aktionen zulassen.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Richtlinienbasierte rollenüberprüfungen

Anforderungen für Rollen können auch mithilfe der neuen Richtlinie-Syntax wird registriert, in denen ein Entwickler eine Richtlinie beim Starten des als Teil der Dienstkonfiguration Autorisierung ausgedrückt werden. Dieser Fehler tritt normalerweise in `ConfigureServices()` in Ihrer *Startup.cs* Datei.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole", policy => policy.RequireRole("Administrator"));
    });
}
```

Richtlinien werden angewendet, mit der `Policy` Eigenschaft auf die `AuthorizeAttribute` Attribut:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Wenn Sie mehrere zulässige Rollen in einer Anforderung angeben möchten, und klicken Sie dann als Parameter zum Angeben der `RequireRole` Methode:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

In diesem Beispiel autorisiert Benutzer angehören der `Administrator`, `PowerUser` oder `BackupAdministrator` Rollen.
