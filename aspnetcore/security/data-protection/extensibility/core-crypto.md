---
title: Core-kryptografieerweiterbarkeit
author: rick-anderson
description: "Erläutert das IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer sowie der obersten Ebene Factory."
manager: wpickett
ms.author: riande
ms.date: 8/11/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 738bf6841b5364f89fa1bcdd11fc71d9be83c23b
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="core-cryptography-extensibility"></a>Core-kryptografieerweiterbarkeit

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Typen, die jede der folgenden Schnittstellen implementieren, sollten threadsichere werden für mehrere Aufrufer.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

Die **IAuthenticatedEncryptor** Schnittstelle ist der grundlegende Baustein von kryptografischen Subsystem. Im Allgemeinen eine IAuthenticatedEncryptor pro Schlüssel vorhanden ist, und die Instanz IAuthenticatedEncryptor dient als Wrapper für alle kryptografischen schlüsselmaterialien und algorithmische Informationen, die erforderlich sind, kryptografische Vorgänge auszuführen.

Wie der Name andeutet, ist der Typ für die Bereitstellung des authentifizierten Ver- und Entschlüsselung Services verantwortlich. Er stellt die folgenden zwei APIs bereit.

* Entschlüsseln (ArraySegment<byte> Chiffretext ArraySegment<byte> AdditionalAuthenticatedData): Byte]

* Verschlüsseln (ArraySegment<byte> als nur-Text ArraySegment<byte> AdditionalAuthenticatedData): Byte]

Die Encrypt-Methode gibt ein Blob, das die enciphered nur-Text und ein authentifizierungstag enthält. Die authentifizierungstag muss die zusätzlichen authentifizierten Daten (AAD) umfassen, obwohl das AAD selbst nicht über die letzte Nutzlast wiederhergestellt werden muss. Die Decrypt-Methode überprüft die authentifizierungstag und gibt die deciphered Nutzlast. Alle Fehler auf (mit Ausnahme von ArgumentNullException und ähnliche) soll um CryptographicException antibiotischem.

> [!NOTE]
> Die IAuthenticatedEncryptor-Instanz selbst muss nicht tatsächlich das Schlüsselmaterial enthalten. Beispielsweise könnte die Implementierung in ein HSM für alle Vorgänge delegieren.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Vorgehensweise: Erstellen einer IAuthenticatedEncryptor

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **IAuthenticatedEncryptorFactory** Schnittstelle darstellt, einen Typ, der weiß, wie zum Erstellen einer [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz. Seine API lautet wie folgt.

* CreateEncryptorInstance (IKey Key): IAuthenticatedEncryptor

Für eine bestimmte Instanz IKey alle authentifizierten Verschlüsselungsprogramme mit seiner CreateEncryptorInstance-Methode erstellte sollte als äquivalent betrachtet, wie in der im folgenden Codebeispiel.

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die **IAuthenticatedEncryptorDescriptor** Schnittstelle darstellt, einen Typ, der weiß, wie zum Erstellen einer [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz. Seine API lautet wie folgt.

* CreateEncryptorInstance(): IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Wie IAuthenticatedEncryptor wird eine Instanz von IAuthenticatedEncryptorDescriptor angenommen, um einen bestimmten Schlüssel zu umschließen. Dies bedeutet, dass für eine bestimmte Instanz IAuthenticatedEncryptorDescriptor alle authentifizierten Verschlüsselungsprogramme mit seiner CreateEncryptorInstance-Methode erstellte entspricht, wie in einzustufen ist die im folgenden Codebeispiel.

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **IAuthenticatedEncryptorDescriptor** Schnittstelle darstellt, einen Typ, der weiß, wie Sie selbst in XML exportieren. Seine API lautet wie folgt.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML-Serialisierung

Der Hauptunterschied zwischen IAuthenticatedEncryptor und IAuthenticatedEncryptorDescriptor ist, dass der Deskriptor weiß, wie die Verschlüsselung zu erstellen und versorgt sie mit gültigen Argumente. Erwägen Sie eine IAuthenticatedEncryptor, deren Implementierung SymmetricAlgorithm und KeyedHashAlgorithm abhängt. Die Verschlüsselung der Auftrag ist, um diese Typen zu nutzen, aber es unbedingt weiß nicht, in denen diese Typen stammen, damit sie wirklich nicht ordnungsgemäße beschrieben, wie sich selbst neu erstellen schreiben kann, wenn die Anwendung neu gestartet wird. Der Deskriptor fungiert als eine höhere zusätzlich. Da der Deskriptor weiß, wie die Verschlüsselung-Instanz zu erstellen (z. B. er kennt die Algorithmen zu erstellen), damit die Verschlüsselung-Instanz neu erstellt werden kann, nach dem Zurücksetzen auf einer Anwendung kann dieses Wissen im XML-Format serialisieren.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Der Deskriptor kann über seine ExportToXml Routine serialisiert werden. Diese Routine gibt eine XmlSerializedDescriptorInfo enthält zwei Eigenschaften: die "XElement"-Darstellung des Deskriptors und den Typ der darstellt ein [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) erfolgen kann zum Aufrufen dieses Deskriptors erhält die entsprechenden "XElement" verwendet.

Der serialisierte Deskriptor enthalten möglicherweise vertrauliche Informationen wie z. B. kryptografischen schlüsselmaterialien. Die Datenschutzsystem verfügt über integrierte Unterstützung für die Verschlüsselung von Informationen, bevor sie in den Speicher beibehalten wurde. Davon profitieren, kennzeichnen der Deskriptor das Element, das sensible Informationen mit den Namen des Attributs ""RequiresEncryption "" (Xmlns "http://schemas.asp.net/2015/03/dataProtection"), Wert "True" enthält.

>[!TIP]
> Für das Festlegen dieses Attributs ist ein Hilfs-API. Rufen Sie die Erweiterungsmethode XElement.MarkAsRequiresEncryption() im Namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel befindet.

Es kann auch Fälle, in denen der serialisierte Deskriptor vertrauliche Informationen enthalten, nicht, vorhanden sein. Schauen wir uns erneut die Groß-/Kleinschreibung ein kryptografischer Schlüssel in einem HSM gespeichert. Der Deskriptor kann nicht das Schlüsselmaterial beim selbst zu serialisieren, da das HSM des Materials in Klartext verfügbar gemacht wird nicht schreiben. Stattdessen kann der Deskriptor schreiben, die Schlüssel eingebunden Version des Schlüssels (falls das HSM Export auf diese Weise kann) oder den HSMs-Bezeichner für den Schlüssel.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

Die **IAuthenticatedEncryptorDescriptorDeserializer** Schnittstelle darstellt, einen Typ, der weiß, wie eine Instanz eines "XElement" IAuthenticatedEncryptorDescriptor zu deserialisieren. Macht eine einzelne Methode:

* ImportFromXml ("XElement"-Element): IAuthenticatedEncryptorDescriptor

Die ImportFromXml-Methode verwendet die "XElement", der von zurückgegeben [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) und entspricht der ursprünglichen IAuthenticatedEncryptorDescriptor erstellt.

Typen, die IAuthenticatedEncryptorDescriptorDeserializer implementieren müssen eine der folgenden zwei öffentlichen Konstruktoren aufweisen:

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> Der an den Konstruktor übergebene IServiceProvider kann null sein.

## <a name="the-top-level-factory"></a>Die Factory auf oberster Ebene

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **AlgorithmConfiguration** Klasse stellt einen Typ, der weiß, wie erstellen [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) Instanzen. Er stellt eine einzelne API bereit.

* CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor

Sichereres AlgorithmConfiguration der obersten Ebene Factory. Die Konfiguration dient als Vorlage. Es dient als Wrapper für algorithmische Informationen (z. B. diese Konfiguration erzeugt Deskriptoren mit einem Hauptschlüssel AES-128-GCM), aber es wurde noch nicht mit einem bestimmten Schlüssel zugewiesen.

Wenn CreateNewDescriptor aufgerufen wird, neue Schlüsselmaterial wird ausschließlich für diesen Aufruf erstellt, und eine neue IAuthenticatedEncryptorDescriptor erstellt umschließt die dieser Schlüsselmaterial sowie die algorithmische Informationen erforderlich, um das Material zu verwenden. Das Schlüsselmaterial konnte in der Software erstellt (und im Speicher gehalten werden), erstellt und innerhalb einer HSM usw. werden konnte. Der wichtige Punkt ist, dass zwei Aufrufe CreateNewDescriptor nie entsprechende IAuthenticatedEncryptorDescriptor-Instanzen erstellen soll.

Der Typ AlgorithmConfiguration fungiert als Einstiegspunkt für die schlüsselerstellung Routinen wie z. B. [automatische Schlüssel parallelen](../implementation/key-management.md#key-expiration-and-rolling). Um die Implementierung für alle zukünftigen Schlüssel ändern zu können, legen Sie die Eigenschaft AuthenticatedEncryptorConfiguration in KeyManagementOptions.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die **IAuthenticatedEncryptorConfiguration** Schnittstelle darstellt, einen Typ, der weiß, wie erstellen [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) Instanzen. Er stellt eine einzelne API bereit.

* CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor

Sichereres IAuthenticatedEncryptorConfiguration der obersten Ebene Factory. Die Konfiguration dient als Vorlage. Es dient als Wrapper für algorithmische Informationen (z. B. diese Konfiguration erzeugt Deskriptoren mit einem Hauptschlüssel AES-128-GCM), aber es wurde noch nicht mit einem bestimmten Schlüssel zugewiesen.

Wenn CreateNewDescriptor aufgerufen wird, neue Schlüsselmaterial wird ausschließlich für diesen Aufruf erstellt, und eine neue IAuthenticatedEncryptorDescriptor erstellt umschließt die dieser Schlüsselmaterial sowie die algorithmische Informationen erforderlich, um das Material zu verwenden. Das Schlüsselmaterial konnte in der Software erstellt (und im Speicher gehalten werden), erstellt und innerhalb einer HSM usw. werden konnte. Der wichtige Punkt ist, dass zwei Aufrufe CreateNewDescriptor nie entsprechende IAuthenticatedEncryptorDescriptor-Instanzen erstellen soll.

Der Typ IAuthenticatedEncryptorConfiguration fungiert als Einstiegspunkt für die schlüsselerstellung Routinen wie z. B. [automatische Schlüssel parallelen](../implementation/key-management.md#key-expiration-and-rolling). Um die Implementierung für alle zukünftigen Schlüssel zu ändern, registrieren Sie einen Singleton-IAuthenticatedEncryptorConfiguration im Dienstcontainer.

---
