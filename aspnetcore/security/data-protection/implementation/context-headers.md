---
title: Kontext-Header
author: rick-anderson
description: In diesem Dokument werden die Implementierungsdetails der ASP.NET Core Data Protection Kontext Header.
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: b32a5e2c216137f1191bbee32dbe76f0ef604670
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="context-headers"></a>Kontext-Header

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Hintergrund und die Theorie

In das Data Protection System bedeutet eine "Key" an, dass ein Objekt, das bereitstellen kann Verschlüsselungsdienste authentifiziert. Jeder Schlüssel wird durch eine eindeutige Id (eine GUID) identifiziert, und mit ihm enthält algorithmische Informationen und entropic Materialien. Es richtet sich an, dass jeder Schlüssel enthalten eindeutige Entropie jedoch das System kann nicht zu erzwingen, und wir auch erforderlich, dass Entwickler, die den Schlüssel Ring manuell durch Ändern der algorithmischen Informationen eines vorhandenen Schlüssels im Schlüssel Ring ändern kann. Unsere sicherheitsanforderungen erhält diesen Fällen erzielen die Datenschutzsystem ein initialisierungskonzept hat [kryptografische Flexibilität](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), womit sicher über einen Einzelwert entropic über mehrere kryptografischen Algorithmen.

Die meisten Systeme die kryptografische Flexibilität unterstützen dazu einige identifizierende Informationen zum Algorithmus in die Nutzlast einschließlich. Der Algorithmus die OID ist im Allgemeinen eignet sich für die dies. Ist jedoch ein Problem, das wir wurde, es mehrere Möglichkeiten gibt, die denselben Algorithmusnamen angeben: "AES" (CNG) und den verwalteten Aes, AesManaged AesCryptoServiceProvider, AesCng und RijndaelManaged (bestimmte bestimmte Parameter) Klassen sind alle tatsächlich identisch Bedeutung, und es müsste eine Zuordnung aller dieser an die richtige OID verwalten. Wenn ein Entwickler ein benutzerdefinierter Algorithmus (oder sogar eine andere Implementierung der AES!) sind möchte, müssten sie uns informieren, dass die OID. Diese zusätzlichen Registrierungsschritt macht Systemkonfiguration besonders qualvoll.

Ausführen in Einzelschritten zurück, haben wir entschieden, dass wir das Problem aus der falschen Richtung fast erreicht wurden. Eine OID gibt an, was der Algorithmus ist, aber wir nicht tatsächlich zu diesem erledigen. Wenn wir einen Einzelwert entropic sicher in zwei verschiedene Algorithmen verwenden müssen, ist es nicht für uns wissen, was tatsächlich die Algorithmen sind erforderlich. Was tatsächlich wichtig ist, wie sie sich Verhalten. Alle ordentliche symmetrischen Block-Verschlüsselungsalgorithmus wird auch eine starke pseudozufälligen Permutation (PRP): Korrigieren Sie die Eingaben (Schlüssel, IV-Modus nur-Text verketten) und die Chiffretext Ausgabe mit einer Wahrscheinlichkeit Überlastung werden andere symmetrische Blockchiffre unterscheiden der Algorithmus den gleichen Eingaben. Auf ähnliche Weise alle ordentliche eines schlüsselgebundenen Hashalgorithmus-Funktion ist auch ein sicheres pseudozufälligen (PRF), und angegebene einen festen Eingabeset seine Ausgabe überwältigend werden alle anderen eines schlüsselgebundenen Hashalgorithmus-Funktion unterscheidet.

Wir verwenden dieses Konzept der starken PRPs und PRFs einen Kontextheader erstellen. Diese Kontextheader fungiert im Wesentlichen als einen stabilen Fingerabdruck über die Algorithmen für einen bestimmten Vorgang verwendet, und bietet die kryptografische Flexibilität, die von der Datenschutzsystem benötigt werden. Dieser Header ist reproduzierbar und wird später verwendet, als Teil der [Unterschlüssel Ableitung Prozess](subkeyderivation.md#data-protection-implementation-subkey-derivation). Es gibt zwei Möglichkeiten, um die Kontextheader je nach den Betriebsmodi der zugrunde liegenden Algorithmen zu erstellen.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Verschlüsselung im CBC-Modus + HMAC-Authentifizierung

<a name="data-protection-implementation-context-headers-cbc-components"></a>

Die Kontextheader besteht aus folgenden Komponenten:

* [16 Bits] Der Wert 00 00, also ein Marker bedeutet "CBC-Verschlüsselung + HMAC-Authentifizierung".

* [32 Bits] Die Schlüssellänge (in Byte, big-Endian) von der Block symmetrischen Verschlüsselungsalgorithmus.

* [32 Bits] Die Blockgröße (in Byte, big-Endian) von der Block symmetrischen Verschlüsselungsalgorithmus.

* [32 Bits] Die Schlüssellänge (in Byte, big-Endian) des HMAC-Algorithmus. (Derzeit entspricht die Größe des Schlüssels immer die Digest-Größe.)

* [32 Bits] Die Digest-Größe (in Bytes, big-Endian) des HMAC-Algorithmus.

* EncCBC (K_E, IV, ""), die die Ausgabe der Block symmetrischen Verschlüsselungsalgorithmus eine leere Zeichenfolge Eingaben und IV ist ein Vektor Nullen. Die zur Erstellung der K_E wird im folgenden beschrieben.

* MAC (K_H, ""), ist die Ausgabe des HMAC-Algorithmus eine leere Zeichenfolge Eingaben. Die zur Erstellung der K_H wird im folgenden beschrieben.

Im Idealfall könnten wir Nullen Vektoren für K_E und K_H übergeben. Allerdings möchten wir die Situation zu vermeiden, der zugrunde liegende Algorithmus, in denen das Vorhandensein der schwachen Schlüssel vor der Durchführung aller Vorgänge (insbesondere DES und 3DES) überprüft werden kann, die unter Verwendung eines einfachen oder repeatable-Musters wie ein Vektor Nullen, schließt.

Stattdessen verwenden wir die NIST SP800 108 KDF in Counter-Modus (finden Sie unter [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sek. 5.1) mit NULL-Schlüssel, Bezeichnung und Kontext und HMACSHA512 als zugrunde liegenden PRF. Wir leiten | K_E | + | K_H | Bytes der Ausgabe, klicken Sie dann das Ergebnis wird in grundtypelemente zerlegt K_E und K_H selbst. Mathematisch, dies wie folgt dargestellt wird.

(K_E || K_H) = SP800_108_CTR (prf = HMACSHA512 Key = "", Label = "", Kontext = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Beispiel: AES-192-CBC + HMACSHA256

Betrachten Sie beispielsweise die Groß-/Kleinschreibung, wobei der Block symmetrischen Verschlüsselungsalgorithmus AES-192-CBC und der Validierungsalgorithmus ist HMACSHA256, aus. Das System würde die Kontextheader mithilfe der folgenden Schritte generieren.

Erstens können (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512 Key = "", Label = "", Kontext = ""), wobei | K_E | = 192 Bits und | K_H | = 256 Bits pro angegebenen Algorithmen. This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Als Nächstes berechnen Enc_CBC (K_E, IV, "") für AES-192-CBC angegebene IV = 0 * und K_E wie oben beschrieben.

result := F474B1872B3B53E4721DE19C0841DB6F

Als Nächstes berechnen MAC (K_H, "") für HMACSHA256 K_H wie oben angegeben.

result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Dadurch wird die vollständige Kontextheader unten generiert:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Dieser Kontextheader ist der Fingerabdruck des Paars Algorithmus authentifizierten Verschlüsselung (Verschlüsselung mit AES-192-CBC + HMACSHA256-Überprüfung). Die Komponenten, wie beschrieben [oben](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) sind:

* die Markierung (00 00)

* Block Cipher Schlüssellänge (00-00-00-18)

* die Blockgröße für das "Cipher Block" (00-00-00-10)

* der HMAC-Schlüssellänge (00-00-00-20)

* der HMAC-Digest-Größe (00-00-00-20)

* die Blockchiffre PRP Ausgabe (F4 74 - DB 6F) und

* die Ausgabe des HMAC-PRF (D4 79 - End).

> [!NOTE]
> Die Verschlüsselung im CBC-Modus + HMAC Authentifizierungsheader-Kontext basiert die gleiche Weise, unabhängig davon, ob die Algorithmen Implementierungen von Windows CNG oder von verwalteten SymmetricAlgorithm und KeyedHashAlgorithm Typen bereitgestellt werden. Dadurch können Anwendungen, die auf anderen Betriebssystemen, die die gleichen Kontextheader zuverlässig zu erzeugen, obwohl Betriebssysteme Implementierungen der Algorithmen unterscheiden. (In der Praxis nicht die KeyedHashAlgorithm muss einen richtigen HMAC sein. Es kann eines schlüsselgebundenen Hashalgorithmus Algorithmustyp sein.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Beispiel: 3DES-192-CBC + HMACSHA1

Erstens können (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512 Key = "", Label = "", Kontext = ""), wobei | K_E | = 192 Bits und | K_H | = 160 Bits pro angegebenen Algorithmen. Dies führt zu K_E = A219... E2BB und K_H = DC4A... B464 im folgenden Beispiel:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Als Nächstes berechnen Enc_CBC (K_E, IV, "") für 3DES-192-CBC angegebene IV = 0 * und K_E wie oben beschrieben.

Ergebnis: ABB100F81E53E10E =

Als Nächstes berechnen MAC (K_H, "") für HMACSHA1 K_H wie oben angegeben.

result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Dies erzeugt die vollständige Kontextheader also einen Fingerabdruck, der die authentifizierte Verschlüsselung Algorithmus Paar (3DES-192-CBC-Verschlüsselung + HMACSHA1 Überprüfung) unten angezeigt:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Die Komponenten aufgliedern wie folgt:

* die Markierung (00 00)

* Block Cipher Schlüssellänge (00-00-00-18)

* die Blockgröße für das "Cipher Block" (00-00-00-08)

* der HMAC-Schlüssellänge (00-00-00-14)

* der HMAC-Digest-Größe (00-00-00-14)

* die Blockchiffre PRP Ausgabe (B1 AB - E1 0E) und

* die Ausgabe des HMAC-PRF (76 "EB" - End).

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/Counter-Modus-Verschlüsselung und Authentifizierung

Die Kontextheader besteht aus folgenden Komponenten:

* [16 Bits] Der Wert 00 01, also ein Marker bedeutet "GCM-Verschlüsselung + Authentifizierung".

* [32 Bits] Die Schlüssellänge (in Byte, big-Endian) von der Block symmetrischen Verschlüsselungsalgorithmus.

* [32 Bits] Die Nonce Größe (in Byte, big-Endian) während der authentifizierten Verschlüsselungsvorgänge verwendet. (Für unser System ist dies korrigiert in Nonce Größe = 96 Bit.)

* [32 Bits] Die Blockgröße (in Byte, big-Endian) von der Block symmetrischen Verschlüsselungsalgorithmus. (Für GCM, wird dies zur Blockgröße behoben = 128 Bit.)

* [32 Bits] Die Authentifizierung Tag Größe (in Bytes, big-Endian) von den authentifizierten Verschlüsselungsfunktion generiert wurde. (Für unser System ist dies am Tag Größe behoben = 128 Bit.)

* [128 Bits] Das Tag Enc_GCM (K_E Nonce, ""), die die Ausgabe der Block symmetrischen Verschlüsselungsalgorithmus eine leere Zeichenfolge Eingaben und Nonce ist ein 96-Bit-Nullen Vektor.

Nutzen denselben Mechanismus wie die Verschlüsselung CBC + HMAC-Szenario für Verbundauthentifizierung K_E abgeleitet. Da im Play hier keine K_H vorhanden ist, wird im Wesentlichen haben jedoch | K_H | = 0, und der Algorithmus wird reduziert die im folgenden Format.

K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-256-gcm"></a>Beispiel: AES-256-GCM

Zunächst können K_E = SP800_108_CTR (prf HMACSHA512 = Schlüssel = "", Label = "", Kontext = ""), wobei | K_E | = 256 Bits.

K_E: 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8 =

Als Nächstes Berechnen der authentifizierungstag von Enc_GCM (K_E Nonce, "") für AES-256-GCM angegebene Nonce = 096 und K_E wie oben beschrieben.

result := E7DCCE66DF855A323A6BB7BD7A59BE45

Dadurch wird die vollständige Kontextheader unten generiert:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Die Komponenten aufgliedern wie folgt:

   * die Markierung (00 01)

   * Block Cipher Schlüssellänge (00-00-00-20)

   * die Größe des Nonce (00 00 00 0 C)

   * die Blockgröße für das "Cipher Block" (00-00-00-10)

   * die Authentifizierung Tag Größe (00 00 00 10) und

   * die authentifizierungstag von der Ausführung der Blockchiffre (E7 DC - End).
