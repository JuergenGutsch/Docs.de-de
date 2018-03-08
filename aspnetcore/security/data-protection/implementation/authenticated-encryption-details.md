---
title: "Authentifizierte Verschlüsselungsdetails"
author: rick-anderson
description: "Dieses Dokument beschreibt die Implementierungsdetails des Datenschutzes ASP.NET Core authentifiziert Verschlüsselung."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 6229adaa7145ff192104e2243daefb77f516c2c5
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="authenticated-encryption-details"></a>Authentifizierte Verschlüsselungsdetails

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Aufrufe von IDataProtector.Protect sind authentifizierte Verschlüsselungsvorgänge. Die Protect-Methode bietet, Vertraulichkeit und Authentizität und es auf die Zweck-Kette, die verwendet wurde, dessen Stamm IDataProtectionProvider dieser bestimmten IDataProtector Instanz Ableitung gebunden ist.

IDataProtector.Protect nimmt einen Byte []-nur-Text-Parameter auf und erzeugt eine Byte [] geschützten Nutzlast, deren Format unten beschrieben ist. (Es ist auch eine Erweiterung methodenüberladung nimmt einen Zeichenfolgenparameter für nur-Text aus, und gibt eine Nutzlast mit geschützten zurück. Wenn diese API verwendet wird das geschützte nutzlastformat verfügen weiterhin über den unterhalb der Struktur ist jedoch sehr [base64url-codierte](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Geschützte nutzlastformat

Die geschützte nutzlastformat besteht aus drei Hauptkomponenten:

* Eine 32-Bit-Header Magic, der die Version des Data Protection Systems angibt.

* Eine 128-Bit-Schlüssel-Id, die den Schlüssel zum Schützen dieser bestimmten Nutzlast identifiziert.

* Der Rest der geschützten Nutzlast ist [speziell für die Verschlüsselung durch diesen Schlüssel gekapselt](subkeyderivation.md#data-protection-implementation-subkey-derivation). Im folgenden Beispiel den Schlüssel darstellt, ein AES-256-CBC + HMACSHA256-Verschlüsselung und die Nutzlast ist weiter wie folgt unterteilt: * Ein 128-Bit-Schlüssel-Modifizierer. * Eine 128-Bit-Initialisierungsvektor. * 48 Byte der AES-256-CBC-Ausgabe. * Ein Tag für den HMACSHA256-Authentifizierung.

Eine geschützte Beispielnutzlast wird unten veranschaulicht.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

Sind aus dem nutzlastformat über die ersten 32 Bits oder 4 Bytes der Magic-Header identifiziert die Version (09 d0 C9 d0)

Die nächsten 128 Bits oder 16 Byte ist der Schlüsselbezeichner (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

Im weiteren Verlauf enthält die Nutzlast und bezieht sich auf das verwendete Format.

>[!WARNING]
> Alle Nutzlasten, die auf einen bestimmten Schlüssel geschützt beginnt mit dem gleichen 20-Byte (Magic-Wert, Schlüssel-Id)-Header. Administratoren können diese Tatsache zu Diagnosezwecken verwenden, um zu ermitteln, wenn eine Nutzlast generiert wurde. Beispielsweise entspricht die Nutzlast, die oben aufgeführten Schlüssel {0c819c80-6619-4019-9536-53f8aaffee57}. Wenn nach dem Überprüfen der wichtigsten Repository finden Sie, dass dieser bestimmte Schlüssel Aktivierungsdatum 2015-01-01 wurde Ablaufdatum 2015-03-01 wurde, und es angemessen ist, anzunehmen, dass die Nutzlast (sofern nicht manipuliert) innerhalb dieses Zeitfensters, erteilen generiert wurde oder nehmen eine kleine Spielraum Faktor auf beiden Seiten an.
