---
title: Einfache Autorisierung
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
uid: security/authorization/simple
ms.openlocfilehash: 91f402b1cbf73e212418d197a8a7230ce22e9e1d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="simple-authorization"></a>Einfache Autorisierung

<a name="security-authorization-simple"></a>

Autorisierung in MVC wird gesteuert durch die `AuthorizeAttribute` Attribut und seine verschiedenen Parameter. Am einfachsten, Anwenden der `AuthorizeAttribute` -Attribut auf einen Controller oder die Aktion der Zugriff auf den Controller eingeschränkt oder eine Aktion für alle authentifizierten Benutzer.

Z. B. der folgende Code schränkt den Zugriff auf die `AccountController` für alle authentifizierten Benutzer.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Wenn Sie Autorisierung auf dem Controller, anstatt eine Aktion anwenden möchten, wenden Sie die `AuthorizeAttribute` -Attribut auf die Aktion selbst:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Jetzt nur authentifizierte Benutzer zugreifen können die `Logout` Funktion.

Sie können auch die `AllowAnonymousAttribute` Attribut, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zu ermöglichen. Zum Beispiel:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Dies würde nur authentifizierte Benutzern ermöglichen, die `AccountController`, mit Ausnahme der `Login` Aktion, die jeder Benutzer, unabhängig vom jeweiligen Status authentifiziert oder nicht authentifizierte / anonym zugegriffen werden kann.

>[!WARNING]
> `[AllowAnonymous]`umgeht alle Autorisierung-Anweisungen. Wenn Sie kombinieren anwenden `[AllowAnonymous]` und ein beliebiger `[Authorize]` Attribut anschließend die Authorize-Attribute werden immer ignoriert. Angenommen, Sie gelten `[AllowAnonymous]` auf dem Controller Ebene eine `[Authorize]` Attribute auf demselben Controller oder auf eine beliebige Aktion darin werden ignoriert.
