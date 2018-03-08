---
title: "In Softwareschlüsselspeicher-Format"
author: tdykstra
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 832f150b269e36ac35f0d00cafbf4903f7aef4fc
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="key-storage-format"></a>In Softwareschlüsselspeicher-Format

<a name="data-protection-implementation-key-storage-format"></a>

Objekte werden im XML-Darstellung im Ruhezustand gespeichert. Der Standardname für das Verzeichnis zum Speichern von Schlüsseln ist % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.

## <a name="the-key-element"></a>Die \<Schlüssel >-Element

Schlüssel, die als Objekte der obersten Ebene im Repository Schlüssel vorhanden ist. Gemäß der Konvention Schlüssel verfügen über eine der Dateiname **Schlüssel-{Guid} .xml**, wobei {Guid} die Id des Schlüssels ist. Jede dieser Dateien enthält einen einzelnen Schlüssel. Das Format der Datei lautet wie folgt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

Die \<Schlüssel >-Element enthält die folgenden Attribute und untergeordneten Elemente:

* Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt; der Dateiname ist einfach eine äußerst für Menschen lesbar.

* Die Version der \<Schlüssel > Element, das zurzeit auf 1 fest.

* Der Schlüssel erstellen, aktivieren und Ablauf von Datumsangaben.

* Ein \<Deskriptor >-Element, das Objektberechtigungsinformationen für die authentifizierten Verschlüsselung-Implementierung, die als Bestandteil dieser Schlüssel enthält.

Im obigen Beispiel ist der Schlüssel-Id ist {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, erstellt und auf dem 19. März 2015 aktiviert wurde und er verfügt über eine Lebensdauer von 90 Tagen. (Gelegentlich möglicherweise das Datum der Aktivierung etwas vor dem Erstellungsdatum erreicht wie in diesem Beispiel liegen. Dies ist aufgrund einer Nit wie die APIs arbeiten, und ist in der Praxis harmlos.)

## <a name="the-descriptor-element"></a>Die \<Deskriptor >-Element

Die äußere \<Deskriptor >-Element enthält ein Attribut-DeserializerType, also der Assembly qualifizierte Name eines Typs der IAuthenticatedEncryptorDescriptorDeserializer implementiert. Dieser Typ ist verantwortlich für das Lesen des inneren \<Deskriptor >-Element und für die Analyse der enthaltenen Informationen.

Bestimmte Format der \<Deskriptor >-Elements hängt von der authentifizierten Encryptor Implementierung gekapselt durch den Schlüssel, und jedes Typs Deserialisierungsprogramm erwartet ein etwas anderes Format für diese. Im Allgemeinen jedoch dieses Element enthält algorithmische Informationen (Namen, Typen, OIDs oder ähnlich) und Materialien, die für den geheimen Schlüssel. Im obigen Beispiel gibt der Deskriptor an, dass dieses Schlüssels dient als Wrapper für Verschlüsselung mit AES-256-CBC + HMACSHA256-Validierung.

## <a name="the-encryptedsecret-element"></a>Die \<EncryptedSecret >-Element

Ein <encryptedSecret> Element enthält die verschlüsselte Form des Schlüsselmaterials geheimen kann vorhanden sein, wenn [ist die Verschlüsselung von geheimen Schlüsseln im Ruhezustand aktiviert](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest). Das Attribut DecryptorType werden die Assembly qualifizierte Name eines Typs der IXmlDecryptor implementiert. Dieser Typ ist verantwortlich für das Lesen des inneren <encryptedKey> Element- und entschlüsseln, um den ursprünglichen Klartext zu gewinnen.

Wie bei \<Deskriptor >, die bestimmten Format der <encryptedSecret> -Elements hängt von der-ruhender Verschlüsselungsmechanismus verwendet. Im obigen Beispiel wird der Hauptschlüssel verschlüsselt unter Verwendung von Windows DPAPI pro des Kommentars.

## <a name="the-revocation-element"></a>Die \<Revocation >-Element

Revocations, die als Objekte der obersten Ebene im Repository Schlüssel vorhanden ist. Gemäß der Konvention verfügen Revocations den Dateinamen **Revocation-{Zeitstempel} .xml** (für aufheben aller Schlüssel vor einem bestimmten Datum) oder **Revocation-{Guid} .xml** (für das Sperren eines bestimmten Schlüssels). Jede Datei enthält ein einziges \<Revocation > Element.

Für Revocations der einzelnen Schlüssel, den Inhalt der Datei werden folgendermaßen aus.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

In diesem Fall wird nur der angegebene Schlüssel gesperrt. Wenn die Schlüssel-Id ist "*", jedoch wie in der folgenden Beispiel werden alle Schlüssel, deren Datum vor dem angegebenen Sperrdatum ist, gesperrt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Die \<Grund > Element wird vom System nie gelesen. Es ist einfach eine bequeme Möglichkeit, eine lesbare Ursache für die Sperrung zu speichern.
