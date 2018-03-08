---
title: "Einführung in die Autorisierung"
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
uid: security/authorization/introduction
ms.openlocfilehash: fa6dcbc0627181cd1aca0926fa008f3db907742f
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="introduction"></a>Einführung

<a name="security-authorization-introduction"></a>

Autorisierung bezieht sich auf den Prozess, der bestimmt, was ein Benutzer ausführen können, ist. Beispielsweise ist ein Administrator zum Erstellen einer Dokumentbibliothek Dokumente hinzufügen, Bearbeiten von Dokumenten und löschen Sie diese zulässig. Ein Benutzer ohne Administratorrechte arbeiten mit der Bibliothek ist nur autorisiert, auf die Dokumente zu lesen.

Autorisierung ist orthogonale und unabhängig von der Authentifizierung, dabei der wird ermitteln, wer ein Benutzer ist. Authentifizierung kann eine oder mehrere Identitäten für den aktuellen Benutzer zu erstellen.

## <a name="authorization-types"></a>Autorisierungstypen

ASP.NET Core Autorisierung bietet eine einfache deklarative [Rolle](roles.md) und ein [umfangreiche richtlinienbasierten](policies.md) Modell. Autorisierung ist in Anforderungen ausgedrückt und Handler auswerten ein Benutzer Ansprüche für Anforderungen. Imperative Überprüfungen können basieren auf einfachen oder Richtlinien für die Bewerten der Benutzeridentität und die Eigenschaften der Ressource, die der Benutzer zugreifen möchte.

## <a name="namespaces"></a>Namespaces

Autorisierungskomponenten, einschließlich der `AuthorizeAttribute` und `AllowAnonymousAttribute` Attribute befinden sich in der `Microsoft.AspNetCore.Authorization` Namespace.
