---
title: Zweck Zeichenfolgen
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 799c3dc2768e264307783efafee626a346a9362c
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="purpose-strings"></a>Zweck Zeichenfolgen

<a name="data-protection-consumer-apis-purposes"></a>

Komponenten, die über nutzen `IDataProtectionProvider` muss eine eindeutige übergeben *Zwecke* Parameter an die `CreateProtector` Methode. Im Sinne *Parameter* ist für die Sicherheit der Datenschutzsystem integriert, da hiermit Isolation zwischen kryptografischen Consumern, selbst wenn der Stamm-Kryptografieschlüssel identisch sind.

Wenn ein Consumer einen Zweck angegeben ist, den Zweck Zeichenfolge zusammen mit der Stamm-Kryptografieschlüssel dient zum eindeutigen kryptografischen Unterschlüssel Consumer abgeleitet werden. Dadurch wird den Consumer aus anderen kryptografischen Consumer in der Anwendung isoliert: keine andere Komponente die Nutzlasten lesen kann, und eine andere Komponente Nutzlasten nicht gelesen. Diese Isolierung rendert auch unmöglich gesamte Kategorien von Angriff auf die Komponente.

![Beispiel für ein Diagramm Zweck](purpose-strings/_static/purposes.png)

In der Abbildung oben `IDataProtector` Instanzen A und B **kann nicht** Lesen des jeweils anderen Nutzlasten und nur ihre eigenen.

Die Zweck Zeichenfolge keine geheim sein. Dies sollte einfach insofern eindeutig sein, dass keine andere gut konzipierte Komponente immer den gleichen Zweck Zeichenfolge bereitstellt.

>[!TIP]
> Unter Verwendung des Namespace und Typ der Komponente, die Datenschutz-APIs nutzen, ist eine gute Faustregel, wie in der Praxis, die diese Informationen nicht in Konflikt steht.
>
>Eine Contoso autorisierten-Komponente, die für das trägertoken minting zuständig ist möglicherweise Contoso.Security.BearerToken als seine Zweck Zeichenfolge verwenden. Oder es möglicherweise - sogar - Contoso.Security.BearerToken.v1 als seine Zweck Zeichenfolge verwenden. Ermöglicht das Anfügen der Versionsnummer einer zukünftigen Version Contoso.Security.BearerToken.v2 als ihren Zweck zu verwenden, und die verschiedenen Versionen wäre vollständig voneinander isoliert, soweit Nutzlasten wechseln.

Seit dem Zwecke Parameter `CreateProtector` ist ein Array von Zeichenfolgen, die oben genannten konnte haben stattdessen als angegeben `[ "Contoso.Security.BearerToken", "v1" ]`. Dies ermöglicht das Erstellen einer Hierarchie von Zwecken und mehrinstanzenfähigkeit Szenarios mit dem Data Protection System öffnet.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Komponenten sollten nicht als alleinige Quelle der Eingabe für die Zwecke-Kette nicht vertrauenswürdigen Benutzereingaben zulassen.
>
>Betrachten Sie beispielsweise eine Komponente Contoso.Messaging.SecureMessage, die zum Speichern von sicheren Nachrichten zuständig ist. Wären die sichere Messagingkomponente Aufrufen `CreateProtector([ username ])`, wird ein böswilliger Benutzer ein Konto mit dem Benutzernamen "Contoso.Security.BearerToken" in beim Abrufen der Komponente aufrufen, erstellt möglicherweise `CreateProtector([ "Contoso.Security.BearerToken" ])`, versehentlich wodurch sicheres messaging System MinZ Nutzlasten, die als Authentifizierungstoken wahrgenommen werden konnte.
>
>Eine bessere Zwecke-Kette für die Messagingkomponente wäre `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, die richtigen Isolation bietet.

Die Isolierung durch und das Verhalten des `IDataProtectionProvider`, `IDataProtector`, und Zwecke lauten wie folgt:

* Für einen angegebenen `IDataProtectionProvider` -Objekt, der `CreateProtector` Methode erstellt ein `IDataProtector` Objekt eindeutig gebunden, sowohl die `IDataProtectionProvider` Objekt, das erstellt hat und der Zwecke-Parameter an die Methode übergeben wurde.

* Der Zweck-Parameter darf nicht null sein. (Wenn Zwecke als Bytearray angegeben ist, bedeutet dies, dass das Array nicht der Länge Null sein darf und alle Elemente des Arrays müssen ungleich Null sein.) Ein leere Zeichenfolge Zweck ist technisch zulässig, wird jedoch abgeraten.

* Argumente für zwei Zwecke sind gleichwertig, wenn sie die gleichen Zeichenfolgen (über einen ordinalen Vergleich) in der gleichen Reihenfolge enthalten. Ein Argument für die einzelnen Zweck ist gleichbedeutend mit der entsprechenden Einzelelement-Zwecke Array.

* Zwei `IDataProtector` Objekte sind äquivalent, wenn sie über entsprechende erstellt werden `IDataProtectionProvider` Objekte mit den Parametern entspricht Zwecke.

* Für einen bestimmten `IDataProtector` -Objekt, einen Aufruf von `Unprotect(protectedData)` zurück, wird der ursprüngliche `unprotectedData` nur, wenn `protectedData := Protect(unprotectedData)` ein entsprechendes `IDataProtector` Objekt.

> [!NOTE]
> Wir sind nicht die Groß-/Kleinschreibung in Erwägung ziehen, in denen eine Komponente absichtlich eine Zeichenfolge Zweck wählt was einen Konflikt mit einer anderen Komponente bezeichnet wird. Dieses System ist nicht vorgesehen, Sicherheitsgarantien angeben, dass bösartiger Code innerhalb der Arbeitsprozess bereits ausgeführt wird, und eine solche Komponente ist im Wesentlichen böswillige berücksichtigt werden.
