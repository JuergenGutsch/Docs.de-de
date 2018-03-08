---
title: View-basierte Autorisierung in ASP.NET Core MVC
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 10/30/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/views
ms.openlocfilehash: 58cafcfdc7946e82d1e0ea5de95e0e497b1b6bcf
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="view-based-authorization"></a>Ansicht basierende Autorisierung

<a name="security-authorization-views"></a>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Wenn Sie dem Prüfdienst in jeder Ansicht möchten, fügen die `@inject` -Direktive in der *_ViewImports.cshtml* Datei von der *Ansichten* Verzeichnis. Weitere Informationen finden Sie unter [Abhängigkeitsinjektion in Sichten](xref:mvc/views/dependency-injection).

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

In einigen Fällen wird die Ressource die Ansichtsmodell sein. Aufrufen `AuthorizeAsync` auf genau die gleiche Weise Sie überprüfen, während der würden [ressourcenbasierte Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
   @if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
   @if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
   {
       <p><a class="btn btn-default" role="button"
           href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
   }
   ```
---

Im vorangehenden Code wird das Modell als eine Ressource, die die richtlinienauswertung ausführen sollten übergeben, zu berücksichtigen.

> [!WARNING]
> Verlassen Sie sich nicht auf die Umschaltfläche Sichtbarkeit Ihrer app von Elementen der Benutzeroberfläche, wie die einzige autorisierungsprüfung. Ausblenden eines Benutzeroberflächenelements möglicherweise nicht vollständig Zugriff auf seine zugeordneten Controlleraktion verhindern. Betrachten Sie beispielsweise die Schaltfläche im vorherigen Codeausschnitt. Benutzer kann Aufrufen der `Edit` Aktionsmethode, wenn der relative Ressource ist die URL ist */Document/Edit/1*. Aus diesem Grund die `Edit` Aktionsmethode sollte eine eigene autorisierungsprüfung ausführen.
