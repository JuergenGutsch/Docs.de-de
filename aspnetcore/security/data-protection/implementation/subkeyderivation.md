---
title: "Unterschlüssel Ableitung und authentifizierten Verschlüsselung"
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: e070742b5d9966c4772fd2f0a6d637d98a46137c
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="subkey-derivation-and-authenticated-encryption"></a>Unterschlüssel Ableitung und authentifizierten Verschlüsselung

<a name="data-protection-implementation-subkey-derivation"></a>

Die meisten Schlüssel im Schlüssel Ring enthält eine Form der Entropie und algorithmische Informationen, die darauf hinweist, "Verschlüsselung im CBC-Modus + HMAC-Überprüfung" oder "GCM-Verschlüsselung + Überprüfung". In diesen Fällen wir verweisen auf die eingebettete Entropie, als das Schlüsselmaterial (oder KM) für diesen Schlüssel, und wir führen Sie eine schlüsselableitung-Funktion, um die Schlüssel abgeleitet werden, die für die tatsächlichen kryptografischen Vorgänge verwendet werden.

> [!NOTE]
> Schlüssel sind abstrakt, und eine benutzerdefinierte Implementierung Verhalten sich möglicherweise nicht wie unten beschrieben. Wenn der Schlüssel eine eigene Implementierung der bietet `IAuthenticatedEncryptor` statt eines integrierten Factorys, die in diesem Abschnitt beschriebenen Verfahren nicht mehr gilt.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Zusätzliche authentifizierte Daten und Unterschlüsseln Ableitung

Die `IAuthenticatedEncryptor` Schnittstelle als die Kernschnittstelle für alle authentifizierten Verschlüsselungsvorgänge dient. Die `Encrypt` -Methode übernimmt zwei Puffer: nur-Text und AdditionalAuthenticatedData (AAD). Der Ablauf der nur-Text-Inhalt unverändert den Aufruf von `IDataProtector.Protect`, aber das AAD wird vom System generiert und besteht aus drei Komponenten:

1. Der 32-Bit-Header Magic 09 d0 C9 d0, die diese Version des Data Protection Systems angibt.

2. Die 128-Bit-Schlüssel-ID.

3. Eine Zeichenfolge variabler Länge aus der Zweck-Kette, die erstellt formatiert die `IDataProtector` ist, die diesen Vorgang ausführen.

Da das AAD für das Tupel aller drei Komponenten eindeutig ist, können wir neue Schlüssel von KM abgeleitet werden, anstatt KM selbst in allen unseren kryptografischen Vorgänge. Für jeden Aufruf von `IAuthenticatedEncryptor.Encrypt`, die folgenden Schlüsselableitungsfunktion Prozess findet:

( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)

Hier entgegen der NIST SP800 108 KDF im Leistungsindikator-Modus (finden Sie unter [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sek. 5.1) mit den folgenden Parametern:

* Schlüsselableitungsfunktion Schlüssel (KDK) = K_M

* PRF = HMACSHA512

* label = additionalAuthenticatedData

* context = contextHeader || keyModifier

Der Kontextheader mit variabler Länge ist und dient im Wesentlichen als einen Fingerabdruck-Algorithmen für die wir K_E und K_H abgeleitet sind. Der Key-Modifizierer ist eine 128-Bit-Zeichenfolge, die nach dem Zufallsprinzip generiert für jeden Aufruf von `Encrypt` und dient, um sicherzustellen, dass mit einer Überlastung der Wahrscheinlichkeit, dass "KE" und KH für diesen bestimmten Authentifizierung Verschlüsselungsvorgang eindeutig sind, auch wenn alle anderen Eingabe für die KDF konstant ist.

Für die Verschlüsselung im CBC-Modus + Validierungsvorgänge HMAC | K_E | die Länge des Schlüssels Cipher Block symmetrische und | K_H | ist die Digest-Größe der HMAC-Routine. Für die Verschlüsselung von GCM + Validierungsvorgänge, | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>Verschlüsselung im CBC-Modus + HMAC-Überprüfung

Nachdem K_E über die oben genannten Mechanismus generiert wird, werden wir einen zufälligen Initialisierungsvektor zu generieren und Ausführen der Block symmetrischen Verschlüsselungsalgorithmus zum Verschlüsseln des nur-Text. Der Initialisierungsvektor und Chiffretext werden dann über die HMAC-Routine, die mit dem Schlüssel K_H auf dem Mac erzeugen initialisiert ausgeführt. Dieser Vorgang und der Rückgabewert wird grafisch im folgenden dargestellt.

![Prozess CBC-Modus und der Rückgabewert](subkeyderivation/_static/cbcprocess.png)

*Ausgabe: = KeyModifier || IV || E_cbc (Data K_E, iv) || HMAC (K_H, iv || E_cbc (K_E, iv Data))*

> [!NOTE]
> Die `IDataProtector.Protect` Implementierung wird [das Magic-Header und Schlüssel-Id vorangestellt](authenticated-encryption-details.md) Ausgabe vor der Rückgabe an den Aufrufer. Da das Magic-Header und die Schlüssel-Id implizit sind Teil des [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), und da der Modifizierertaste als Eingabe für die KDF eingezogen wird, dies bedeutet, dass jedes einzelnes Byte der letzte zurückgegebene Nutzlast, von dem Mac authentifiziert

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/Leistungsindikator Modus Verschlüsselung + Überprüfung

Nachdem K_E über die oben genannten Mechanismus generiert wird, werden wir generiert eine zufällige 96-Bit-Nonce und der Block symmetrischen Verschlüsselungsalgorithmus zum Verschlüsseln des Klartext und erzeugen die 128-Bit-authentifizierungstag ausführen.

![GCM-Modus-Prozess und Rückgabetypen](subkeyderivation/_static/galoisprocess.png)

*Ausgabe: = KeyModifier || Nonce || E_gcm (K_E, Nonce, Data) || authTag*

> [!NOTE]
> Obwohl GCM systemintern das Konzept der AAD unterstützt, sind wir noch AAD nur für die ursprüngliche KDF gespeist Aktivierung, um eine leere Zeichenfolge an GCM für ihr AAD-Parameter übergeben. Der Grund dafür ist kombinierter. Erstens [zur Unterstützung von Flexibilität](context-headers.md#data-protection-implementation-context-headers) nie K_M direkt als Verschlüsselungsschlüssel verwendet werden soll. Darüber hinaus erzwingt GCM sehr strenge eindeutigkeitsanforderungen an ihre Eingaben. Die Wahrscheinlichkeit, dass die GCM-Verschlüsselung-Routine jemals aufgerufene für zwei oder mehr unterschiedlichen ist das Sätze von Eingabedaten mit dem gleichen (Schlüssel, Nonce) Paar darf maximal 2 ^ 32. Die Behebung K_E es nicht möglich, wenn mehr als 2 ^ 32 Verschlüsselungsvorgängen, bevor wir führen afoul die 2, die der Mandant ^-32 zu beschränken. Dies scheint eine sehr große Anzahl von Vorgängen kann, jedoch ein Webserver mit hohem Datenverkehrsaufkommen über 4 Milliarden Anforderungen in reine Tagen angeben, auch in die normale Lebensdauer für diese Schlüssel. Der 2 kompatibel bleiben ^-32 Wahrscheinlichkeit der Grenzwert für den Vorgang fortgesetzt werden kann, verwenden Sie einen 128-Bit-Modifizierertaste und 96-Bit-Nonce, die die Anzahl der verwendbaren Vorgänge für alle angegebenen K_M radikal erweitert. Aus Gründen der Einfachheit des Entwurfs nutzen wir der Codepfad KDF zwischen CBC und GCM-Vorgänge und da in die KDF AAD berücksichtigten besteht keine Notwendigkeit für die GCM-Routine weiterleiten.
