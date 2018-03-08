---
title: Resource Based Autorisierung
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 11/07/2017
ms.devlang: csharp
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/resourcebased
ms.openlocfilehash: d3575619c53e77dadc293ea2bb7dc72501a8a1e3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="resource-based-authorization"></a>Ressourcenbasierte Autorisierung

Autorisierungsstrategie richtet sich nach der Ressource, auf die zugegriffen wird. Betrachten Sie ein Dokument Author-Eigenschaft besitzt. Nur der Ersteller ist zulässig, um das Dokument zu aktualisieren. Daher müssen Sie das Dokument aus dem Datenspeicher abgerufen werden vor der Auswertung der Autorisierung.

Attribut-Auswertung erfolgt, vor dem Datenbindung und vor der Ausführung der Seite "-Ereignishandler oder Aktion, die das Dokument lädt. Aus diesen Gründen deklarative Autorisierung mit einer `[Authorize]` Attribut wird nicht ausreichen. Stattdessen können Sie eine benutzerdefinierten Autorisierungs-Methode aufrufen&mdash;als imperative Autorisierung bezeichnet.

Verwenden der [Beispiel-apps](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([zum Herunterladen von](xref:tutorials/index#how-to-download-a-sample)), die in diesem Thema beschriebenen Funktionen zu untersuchen.

<a name="security-authorization-resource-based"></a>

## <a name="use-imperative-authorization"></a>Verwenden Sie eine imperative Autorisierung

Autorisierung wird als implementiert eine [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service und registriert ist, in die Auflistung innerhalb der `Startup` Klasse. Der Dienst wird über zur Verfügung gestellt [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection#fundamentals-dependency-injection) Seitenhandler oder Aktionen.

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService`verfügt über zwei `AuthorizeAsync` methodenüberladungen: eine akzeptieren, die Ressource und den Richtliniennamen und die andere akzeptiert die Ressource und eine Liste der Anforderungen zum Auswerten.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

<a name="security-authorization-resource-based-imperative"></a>

<a name="security-authorization-resource-based-imperative"></a>

Im folgenden Beispiel wird die Ressource, die gesichert werden in eine benutzerdefinierte geladen `Document` Objekt. Ein `AuthorizeAsync` Überladung wird aufgerufen, um zu bestimmen, ob der aktuelle Benutzer zulässig ist, um das angegebene Dokument zu bearbeiten. Eine benutzerdefinierte Autorisierungsrichtlinie für "EditPolicy" wird die Entscheidung umgerechnet. Finden Sie unter [benutzerdefinierte Richtlinie basierende Autorisierung](xref:security/authorization/policies) Weitere Informationen zum Erstellen von Autorisierungsrichtlinien.

> [!NOTE]
> Der folgende Code-Beispielen wird davon ausgegangen, Authentifizierung ausgeführt wurde und den Satz der `User` Eigenschaft.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

---

## <a name="write-a-resource-based-handler"></a>Schreiben Sie einen Handler ressourcenbasierte

Schreiben einen Handler für ressourcenbasierte Autorisierung ist wesentlich unterscheidet nicht [schreiben einen Handler für einfache Anforderungen](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Erstellen Sie eine benutzerdefinierte Anforderung Klasse und Implementieren einer Anforderung Handler-Klasse. Die Handlerklasse gibt an, die Anforderungen und die Ressourcentyp. Z. B. einen Ereignishandler mit einem `SameAuthorRequirement` Anforderung und eine `Document` Ressource sieht wie folgt aus:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

---

Registrieren Sie die Anforderungen und die Ereignishandler in der `Startup.ConfigureServices` Methode:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a>Prozessanforderungen

Wenn Sie Ihre Entscheidungen basierend auf den Ergebnissen des CRUD vornehmen (**C**chine, **R**eitere, **U**palten aktualisieren, **D**Gesam) Operationen, mit dem die [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) Hilfsklasse. Diese Klasse ermöglicht Ihnen das Schreiben von eines einzelnen Handlers anstelle einer einzelnen Klasse für jeden Vorgangstyp. Geben Sie einige Vorgangsnamen, um es verwenden zu um können:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

Der Handler wird folgendermaßen implementiert, mit einem `OperationAuthorizationRequirement` Anforderung und eine `Document` Ressource:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

---

Die vorherigen Handler überprüft den Vorgang unter Verwendung der Ressource, die Identität des Benutzers und der Anforderung `Name` Eigenschaft.

Um einen Handler für die operative Ressource aufzurufen, geben Sie den Vorgang beim Aufrufen von `AuthorizeAsync` in Ihrer Seitenhandler oder Aktion. Im folgenden Beispiel wird bestimmt, ob der authentifizierte Benutzer berechtigt ist, um das angegebene Dokument anzuzeigen.

> [!NOTE]
> Der folgende Code-Beispielen wird davon ausgegangen, Authentifizierung ausgeführt wurde und den Satz der `User` Eigenschaft.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Wenn die Autorisierung erfolgreich ist, wird die Seite für das Dokument anzeigen zurückgegeben. Wenn die Autorisierung fehlschlägt, aber der Benutzer authentifiziert wird, Zurückgeben von `ForbidResult` informiert authentifizierungsmiddleware, die Fehler bei der Autorisierung. Ein `ChallengeResult` wird zurückgegeben, wenn eine Authentifizierung durchgeführt werden muss. Für interaktive Browser-Clients kann es zum Umleiten des Benutzers zu einer Anmeldeseite um geeignet sein.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Wenn die Autorisierung erfolgreich ist, wird die Ansicht für das Dokument zurückgegeben. Wenn die Autorisierung fehlschlägt, zurückgeben `ChallengeResult` Authentifizierung-Middleware informiert werden, dass Fehler bei der Autorisierung, die Middleware dauert die entsprechende Antwort. Eine entsprechende Antwort konnte einen Statuscode 401 oder 403 zurückgegeben werden. Für die interaktive Browser-Clients, kann dies bedeuten Weiterleiten des Benutzers zu einer Anmeldeseite um.

---
