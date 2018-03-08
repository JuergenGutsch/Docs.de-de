---
title: Data Protection computerweite Supportrichtlinie in ASP.NET Core
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: fde8f75422c9dd84311a65b21e1e38b47fbe0306
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Data Protection computerweite Supportrichtlinie in ASP.NET Core

<a name="data-protection-configuration-machinewidepolicy"></a>

Bei Ausführung auf Windows verfügt das System den Datenschutz über eingeschränkte Unterstützung für das Festlegen von computerweiten Standardrichtlinie für alle apps, die ASP.NET Core-Datenschutz nutzen. Die Idee ist, dass ein Administrator ändern möchten, kann einen Standardwert festlegen, z. B. die Algorithmen verwendet oder Schlüsselgültigkeitsdauer, ohne dass Sie jede app auf dem Computer manuell aktualisieren müssen.

> [!WARNING]
> Der Systemadministrator kann die Standardrichtlinie festlegen, aber sie können keine es erzwingen. App-Entwickler kann einen beliebigen Wert mit einem eigenen auswählen immer außer Kraft setzen. Die Standardrichtlinie wirkt sich nur auf apps, in denen der Entwickler einen expliziten Wert für eine Einstellung angegeben wurde nicht, aus.

## <a name="setting-default-policy"></a>Standardrichtlinie festlegen

Um die Standardrichtlinie festlegen, kann ein Administrator bekannte Werte in der Registrierung unter folgendem Schlüssel festlegen.
Wenn Sie auf einem 64-Bit-Betriebssystem und das Verhalten von 32-Bit-Anwendungen zu beeinflussen möchten, beachten Sie außerdem das Wow6432Node äquivalent zu den oben angegebenen Schlüssels zu konfigurieren.

Die unterstützten Werte sind:

* EncryptionType [Zeichenfolge] – Gibt an, welche Algorithmen für den Datenschutz verwendet werden soll. Dieser Wert kann "CNG-CBC", "CNG-GCM" oder "Verwaltet", und wird ausführlicher beschrieben [unten](#data-protection-encryption-types).

* DefaultKeyLifetime [DWORD -] Gibt die Gültigkeitsdauer für die neu generierten Schlüssel. Dieser Wert wird in Tagen angegeben und muss ≥ 7.

* KeyEscrowSinks [Zeichenfolge] – Gibt die Typen, die zur schlüsselhinterlegung verwendet werden soll. Dieser Wert ist eine durch Semikolons getrennte Liste von schlüsselhinterlegung senken, in dem jedes Element in der Liste der Assembly qualifizierte Name eines Typs ist das IKeyEscrowSink implementiert.

<a name="data-protection-encryption-types"></a>

### <a name="encryption-types"></a>Verschlüsselungstypen

Wenn EncryptionType "CNG-CBC" ist, wird das System konfiguriert um symmetrische Blockchiffre CBC-Modus für die Vertraulichkeit und HMAC Authentizität mit Dienste von Windows CNG zu verwenden (finden Sie unter [angeben benutzerdefinierte Windows CNG-Algorithmen](overview.md#data-protection-changing-algorithms-cng)Weitere Details). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder eine Eigenschaft für die CngCbcAuthenticatedEncryptionSettings-Typ entspricht:

* "EncryptionAlgorithm" [Zeichenfolge] - der Name des Verschlüsselungsalgorithmus einen symmetrischen Block CNG verständlich. Dieser Algorithmus wird im CBC-Modus geöffnet werden.

* EncryptionAlgorithmProvider [Zeichenfolge] - der Name der CNG-Anbieter-Implementierung, die vom Algorithmus "EncryptionAlgorithm" erzeugt werden kann.

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**


Die unterstützten Werte sind unten dargestellt.

| Wert              | Typ   | Beschreibung |
| ------------------ | :----: | ----------- |
| EncryptionType     | Zeichenfolge | Gibt an, welche Algorithmen für den Datenschutz verwendet werden soll. Der Wert kann CNG-CBC, CNG-GCM oder verwaltet und wird im folgenden ausführlicher beschrieben. |
| DefaultKeyLifetime | DWORD  | Gibt die Gültigkeitsdauer für die neu generierten Schlüssel an. Der Wert wird in Tagen angegeben und muss > = 7. |
| KeyEscrowSinks     | Zeichenfolge | Gibt die Typen, die zur schlüsselhinterlegung verwendet werden. Der Wert ist eine durch Semikolons getrennte Liste von schlüsselhinterlegung senken, wobei jedes Element in der Liste ist die Assembly qualifizierte Name eines Typs, der implementiert [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Verschlüsselungstypen

Wenn EncryptionType CNG-CBC ist, das System für die Verwendung konfiguriert symmetrische Blockchiffre CBC-Modus für die Vertraulichkeit und HMAC Authentizität mit Windows CNG-Diensten (finden Sie unter [angeben benutzerdefinierte Windows CNG-Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) für Weitere Informationen). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder eine Eigenschaft für die CngCbcAuthenticatedEncryptionSettings-Typ entspricht.

| Wert                       | Typ   | Beschreibung |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | Zeichenfolge | Der Name des Verschlüsselungsalgorithmus einen symmetrischen Block CNG verständlich. Dieser Algorithmus wird im CBC-Modus geöffnet. |
| EncryptionAlgorithmProvider | Zeichenfolge | Der Name der CNG-Anbieter-Implementierung, die vom Algorithmus "EncryptionAlgorithm" erzeugt werden kann. |
| EncryptionAlgorithmKeySize  | DWORD  | Die Länge (in Bits) des Schlüssels, der für der Block symmetrischen Verschlüsselungsalgorithmus abgeleitet werden. |
| HashAlgorithm               | Zeichenfolge | Der Name eines Hashalgorithmus CNG verständlich. Dieser Algorithmus wird im HMAC-Modus geöffnet. |
| HashAlgorithmProvider       | Zeichenfolge | Der Name der CNG-Anbieter-Implementierung, die vom Algorithmus Hashalgorithmus erzeugt werden können. |

Wenn EncryptionType CNG-GCM ist, das System für die Verwendung konfiguriert symmetrische Blockchiffre Galois/Counter-Modus für die Vertraulichkeit und die Authentizität mit Windows CNG-Diensten (siehe [angeben benutzerdefinierte Windows CNG-Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) Weitere Informationen). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder eine Eigenschaft für die CngGcmAuthenticatedEncryptionSettings-Typ entspricht.

| Wert                       | Typ   | Beschreibung |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | Zeichenfolge | Der Name des Verschlüsselungsalgorithmus einen symmetrischen Block CNG verständlich. Dieser Algorithmus wird im Galois/Leistungsindikator-Modus geöffnet. |
| EncryptionAlgorithmProvider | Zeichenfolge | Der Name der CNG-Anbieter-Implementierung, die vom Algorithmus "EncryptionAlgorithm" erzeugt werden kann. |
| EncryptionAlgorithmKeySize  | DWORD  | Die Länge (in Bits) des Schlüssels, der für der Block symmetrischen Verschlüsselungsalgorithmus abgeleitet werden. |

Wenn EncryptionType verwaltet wird, wird das System konfiguriert, um eine verwaltete SymmetricAlgorithm für Vertraulichkeit und KeyedHashAlgorithm Authentizität verwenden (finden Sie unter [angeben benutzerdefinierter verwaltet Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) Weitere Details). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder eine Eigenschaft für die ManagedAuthenticatedEncryptionSettings-Typ entspricht.

| Wert                      | Typ   | Beschreibung |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | Zeichenfolge | Die Assembly qualifizierte Name eines Typs, der SymmetricAlgorithm implementiert. |
| EncryptionAlgorithmKeySize | DWORD  | Die Länge (in Bits) des Schlüssels, der für den symmetrischen Verschlüsselungsalgorithmus abgeleitet werden. |
| ValidationAlgorithmType    | Zeichenfolge | Die Assembly qualifizierte Name eines Typs, der KeyedHashAlgorithm implementiert. |

Wenn EncryptionType beliebiger anderer Wert als Null oder leer ist, löst die Datenschutz-System eine Ausnahme beim Start.

> [!WARNING]
> Wenn eine Standardeinstellung für die Richtlinie konfigurieren, die Typnamen (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks) umfasst, müssen die Typen der App verfügbar sein. Dies bedeutet, dass für apps, die auf dem Desktop-CLR ausgeführt wird, die Assemblys, die diese Typen enthalten, die im globalen Assemblycache (GAC) vorhanden sein sollte. Für ASP.NET Core-apps unter [.NET Core](https://www.microsoft.com/net/core), die Pakete, die diese Typen enthalten, die installiert werden soll.
