---
title: Benutzerdefinierte Richtlinie basierende Autorisierung
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e422a1b2-dc4a-4bcc-b8d9-7ee62009b6a3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/policies
ms.openlocfilehash: 24585ed5b4c21a357fc0eed4de6ccedf9fa50d3e
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="custom-policy-based-authorization"></a>Benutzerdefinierte Richtlinie basierende Autorisierung

<a name="security-authorization-policies-based"></a>

Im Hintergrund der [Rolle Autorisierung](roles.md) und [Ansprüche Autorisierung](claims.md) Verwenden einer Anforderung verwendet wird, einen Handler für die Anforderung und eine vorkonfigurierte Richtlinie. Diese Bausteine ermöglichen es Ihnen, Autorisierung auswertungen in Code, sodass eine umfangreichere, wiederverwendet werden kann, und einer leicht testfähig Autorisierung Struktur auszudrücken.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63,72)]

Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt. Er verfügt über eine einzelne Anforderung, dass von einem Mindestalter, die als Parameter an die Anforderung angegeben ist.

Richtlinien werden angewendet, mit der `[Authorize]` Attribut mit dem Richtliniennamen. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="requirements"></a>Anforderungen

Eine autorisierungsanforderung ist eine Auflistung von Datenparametern, die eine Richtlinie zum Auswerten des aktuellen Benutzerprinzipals verwenden können. In unserer "AtLeast21"-Richtlinie, die Anforderung ist ein einzelner Parameter&mdash;das Mindestalter. Implementiert eine Anforderung `IAuthorizationRequirement`, dies ist eine leere Markierungsschnittstelle. Eine parametrisierte Mindestalter-Anforderung kann wie folgt implementiert:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

> [!NOTE]
> Eine Anforderung benötigen nicht Daten oder Eigenschaften.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Autorisierung Handler

Ein Authorization-Handler ist für die Auswertung von Eigenschaften für eine Anforderung verantwortlich. Der Handler für die Autorisierung wertet die Anforderungen für ein bereitgestelltes `AuthorizationHandlerContext` zu bestimmen, ob der Zugriff zulässig ist. Kann die Anforderung besteht [mehrere Handler](#security-authorization-policies-based-multiple-handlers). Handler erben `AuthorizationHandler<T>`, wobei `T` ist die Anforderung behandelt werden.

<a name="security-authorization-handler-example"></a>

Der Handler Mindestalter kann wie folgt aussehen:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<a name="security-authorization-policies-based-handler-registration"></a>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Handler-Registrierung

Handler werden in der Auflistung der Dienste während der Konfiguration registriert. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63-65,72)]

Jeder Handler, die Dienste-Auflistung hinzugefügt wird, durch den Aufruf `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();`.

## <a name="what-should-a-handler-return"></a>Was sollte ein Handler zurückgeben?

Beachten Sie, dass die `Handle` Methode in der [Handler Beispiel](#security-authorization-handler-example) gibt keinen Wert zurück. Wie der Status Erfolg oder Fehler angezeigt wird?

* Ein Handler gibt die erfolgreiche Ausführung durch den Aufruf `context.Succeed(IAuthorizationRequirement requirement)`, übergeben der Anforderung, die erfolgreich überprüft wurde.

* Ein Handler muss im allgemeinen Fehler zu behandeln, wie andere Handler für die gleiche Anforderung erfolgreich ausgeführt werden können.

* Aufrufen, um Fehler, auch wenn andere Handler für die Anforderung erfolgreich zu garantieren, `context.Fail`.

Unabhängig davon, was Sie in den Handler aufrufen werden alle Handler für eine Anforderung aufgerufen werden, wenn eine Richtlinie der Anforderung erforderlich ist. Anforderungen an, wie z. B. Protokollierung, haben Nebeneffekte, die immer stattfinden wird dadurch auch wenn `context.Fail()` in einen anderen Handler aufgerufen wurde.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Warum würde ich mehrere Handler für eine Anforderung?

In Fällen soll Auswertung auf eine **oder** Basis, mehrere Handler für eine einzelne Anforderung zu implementieren. Microsoft hat es sich beispielsweise um Türen, die nur mit Schlüssel Karten zu öffnen. Wenn Sie Ihre Schlüsselkarte zu Hause lassen, wird die Apparate druckt einen temporären Aufkleber und öffnet die Tür für Sie. In diesem Fall müssten Sie eine einzelne Anforderung *BuildingEntry*, aber mehrere Handler, die jeweils eine einzelne Anforderung überprüfen.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Stellen Sie sicher, dass die beiden Ereignishandler sind [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Wenn die beiden Ereignishandler ist erfolgreich, wenn eine Richtlinie ausgewertet wird die `BuildingEntryRequirement`, richtlinienauswertung erfolgreich ausgeführt wird.

## <a name="using-a-func-to-fulfill-a-policy"></a>Func verwenden, um eine Richtlinie zu erfüllen.

Möglicherweise gibt es Situationen, in welche, die verhindert eine Richtlinie einfach zu express im Code ist. Es ist möglich, geben Sie einen `Func<AuthorizationHandlerContext, bool>` beim Konfigurieren der Richtlinie mit den `RequireAssertion` Richtlinie-Generator.

Beispielsweise der vorherigen `BadgeEntryHandler` könnte folgendermaßen umgeschrieben werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=52-53,57-63)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Zugreifen auf die MVC-Anforderungskontext in Handlern abfangen

Die `HandleRequirementAsync` Methode, die Sie, in einem Ereignishandler für die Autorisierung implementieren verfügt über zwei Parameter: eine `AuthorizationHandlerContext` und `TRequirement` Sie verarbeiten. Frameworks wie z. B. Mvc- oder Jabbr sind frei, um ein Objekt zum Hinzufügen der `Resource` Eigenschaft auf die `AuthorizationHandlerContext` um zusätzliche Informationen zu übergeben.

Beispielsweise MVC übergibt eine Instanz des [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in der `Resource` Eigenschaft. Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext`, `RouteData`, und alles, was sonst von Razor-Pages und MVC bereitgestellt.

Die Verwendung der `Resource` Eigenschaft ist für bestimmte Framework. Mithilfe der Informationen in der `Resource` Eigenschaft schränkt die Autorisierungsrichtlinien auf bestimmten Frameworks. Sollten Sie eine Umwandlung der `Resource` Eigenschaft mit der `as` -Schlüsselwort, und bestätigen Sie dann die Umwandlung wurde erfolgreich ausgeführt werden, um sicherzustellen, dass Ihr Code keine stürzt ab mit einer `InvalidCastException` bei Ausführung auf anderen Frameworks:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
