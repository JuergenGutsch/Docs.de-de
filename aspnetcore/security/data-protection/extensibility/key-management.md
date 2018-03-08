---
title: "Schlüsselverwaltungsdienst-Erweiterbarkeit"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 11/22/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: ce23931e72404347ebc17c69ae90e70cd15328bc
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="key-management-extensibility"></a>Schlüsselverwaltungsdienst-Erweiterbarkeit

<a name="data-protection-extensibility-key-management"></a>

>[!TIP]
> Lesen der [schlüsselverwaltung](../implementation/key-management.md#data-protection-implementation-key-management) Abschnitt vor dem Lesen in diesem Abschnitt, wie einige der grundlegenden Konzepte hinter dieser APIs erläutert wird.

>[!WARNING]
> Typen, die jede der folgenden Schnittstellen implementieren, sollten threadsichere werden für mehrere Aufrufer.

## <a name="key"></a>Key

Die `IKey` Schnittstelle ist die grundlegende Darstellung eines Schlüssels in Kryptografiesystem. Der Begriff Schlüssel ist hier insofern abstrakte, nicht in der struktureller von "kryptografischen schlüsselmaterialien" verwendet. Ein Schlüssel hat die folgenden Eigenschaften:

* Aktivierung, Erstellung und Ablaufdaten

* Status der Zertifikatsperre

* Schlüsselbezeichner (eine GUID)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Darüber hinaus `IKey` macht eine `CreateEncryptor` Methode, die verwendet werden kann, erstellen eine [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz gebunden werden, um diesen Schlüssel.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Darüber hinaus `IKey` macht eine `CreateEncryptorInstance` Methode, die verwendet werden kann, erstellen eine [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz gebunden werden, um diesen Schlüssel.

---

> [!NOTE]
> Es ist keine API zum Abrufen der kryptografischen Rohmaterialien aus einer `IKey` Instanz.

## <a name="ikeymanager"></a>IKeyManager

Die `IKeyManager` Schnittstelle stellt ein Objekt, das verantwortlich für allgemeine Schlüsselspeicher, Abruf und Manipulation dar. Macht drei Vorgänge auf höherer Ebene:

* Erstellen Sie einen neuen Schlüssel, und klicken Sie in Speicher gespeichert.

* Alle Schlüssel aus dem Speicher abrufen.

* Einen oder mehrere Schlüssel widerrufen, und die Sperrinformationen in den Speicher beibehalten.

>[!WARNING]
> Schreiben einer `IKeyManager` ist eine sehr komplexe Aufgabe und die meisten Entwickler sollten sie nicht versuchen. Stattdessen die meisten Entwickler sollten nutzen die Funktionen von Angeboten die [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) Klasse.

<a name="data-protection-extensibility-key-management-xmlkeymanager"></a>

## <a name="xmlkeymanager"></a>XmlKeyManager

Die `XmlKeyManager` Typ ist die mitgelieferten konkrete Implementierung der `IKeyManager`. Er bietet mehrere nützliche Funktionen, einschließlich schlüsselhinterlegung und Verschlüsselung von Schlüsseln im Ruhezustand. Schlüssel in diesem System werden als XML-Elementen dargestellt (insbesondere ["XElement"](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager`hängt von mehreren anderen Komponenten im Verlauf die Aufgaben erfüllen:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

* `AlgorithmConfiguration`, der bestimmt, die durch neue Schlüssel verwendeten Algorithmen.

* `IXmlRepository`, welche steuert, auf dem Schlüssel im Speicher gespeichert werden.

* `IXmlEncryptor`[optional] die ermöglicht das Verschlüsseln der Schlüssel im Ruhezustand.

* `IKeyEscrowSink`[optional] stellt schlüsselhinterlegung Dienste.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

* `IXmlRepository`, welche steuert, auf dem Schlüssel im Speicher gespeichert werden.

* `IXmlEncryptor`[optional] die ermöglicht das Verschlüsseln der Schlüssel im Ruhezustand.

* `IKeyEscrowSink`[optional] stellt schlüsselhinterlegung Dienste.

---

Im folgenden finden Sie übersichtliche Diagramme, die angibt, wie diese Komponenten zusammen in sind kabelgebundene `XmlKeyManager`.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![Schlüssel erstellen](key-management/_static/keycreation2.png)

   *Schlüssels Erstellung / CreateNewKey*

In der Implementierung der `CreateNewKey`, `AlgorithmConfiguration` Komponente dient zum Erstellen Sie einen eindeutigen `IAuthenticatedEncryptorDescriptor`, die dann als XML serialisiert wird. Wenn die Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt. Der nicht verschlüsselte XML-Code wird dann ausgeführt, über eine `IXmlEncryptor` (falls erforderlich) das verschlüsselte XML-Dokument generieren. Dieses verschlüsselte Dokument werden beibehalten, in langfristigen Speicher über die `IXmlRepository`. (Wenn kein `IXmlEncryptor` wird konfiguriert, wird die unverschlüsselte Dokument im beibehalten der `IXmlRepository`.)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![Schlüssel erstellen](key-management/_static/keycreation1.png)

   *Schlüssels Erstellung / CreateNewKey*

In der Implementierung der `CreateNewKey`, `IAuthenticatedEncryptorConfiguration` Komponente dient zum Erstellen Sie einen eindeutigen `IAuthenticatedEncryptorDescriptor`, die dann als XML serialisiert wird. Wenn die Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt. Der nicht verschlüsselte XML-Code wird dann ausgeführt, über eine `IXmlEncryptor` (falls erforderlich) das verschlüsselte XML-Dokument generieren. Dieses verschlüsselte Dokument werden beibehalten, in langfristigen Speicher über die `IXmlRepository`. (Wenn kein `IXmlEncryptor` wird konfiguriert, wird die unverschlüsselte Dokument im beibehalten der `IXmlRepository`.)

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ![Abruf der](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ![Abruf der](key-management/_static/keyretrieval1.png)

---

   *Abrufen von Schlüssel / GetAllKeys*

In der Implementierung der `GetAllKeys`, die XML-Dokumente darstellen Schlüssel und Revocations werden gelesen, aus der zugrunde liegenden `IXmlRepository`. Wenn diese Dokumente verschlüsselt sind, wird das System diese automatisch entschlüsselt. `XmlKeyManager`erstellt das entsprechende `IAuthenticatedEncryptorDescriptorDeserializer` -Instanzen, die Dokumente zu deserialisierenden gestaffelte `IAuthenticatedEncryptorDescriptor` -Instanzen, die einzelnen umschlossen werden `IKey` Instanzen. Diese Auflistung von `IKey` Instanzen wird an den Aufrufer zurückgegeben.

Weitere Informationen zu bestimmten XML-Elemente finden Sie der [Schlüsselspeicher Format Dokument](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

Die `IXmlRepository` Schnittstelle darstellt, einen Typ, der dauerhaft zu XML und XML-Daten aus einem Sicherungsspeicher abrufen kann. Macht zwei APIs:

* GetAllElements() : IReadOnlyCollection<XElement>

* StoreElement ("XElement"-Element, FriendlyName Zeichenfolge)

Implementierungen von `IXmlRepository` müssen nicht den Umweg über diese XML-Code zu analysieren. Sie sollten die XML-Dokumente als nicht transparent behandelt und höhere Ebenen Gedanken machen, generieren und die Dokumente analysieren können.

Es gibt zwei integrierte konkrete Typen die implementieren `IXmlRepository`: `FileSystemXmlRepository` und `RegistryXmlRepository`. Finden Sie unter der [softwareschlüsselspeicher-Anbieter Dokument](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) für Weitere Informationen. Registrieren ein benutzerdefinierten `IXmlRepository` wäre die geeignete Weise verwenden Sie einen anderen Sicherungsspeicher, z. B. Azure Blob-Speicher.

Um das Standard-Repository anwendungsweite ändern möchten, registrieren Sie eine benutzerdefinierte `IXmlRepository` Instanz:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ```csharp
   services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
   ```
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ```csharp
   services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
   ```

---

<a name="data-protection-extensibility-key-management-ixmlencryptor"></a>

## <a name="ixmlencryptor"></a>IXmlEncryptor

Die `IXmlEncryptor` Schnittstelle darstellt, einen Typ, der eine nur-Text-XML-Element verschlüsseln kann. Es stellt eine einzelne API bereit:

* ("XElement" PlaintextElement) zu verschlüsseln: EncryptedXmlInfo

Wenn ein serialisiertes `IAuthenticatedEncryptorDescriptor` enthält alle Elemente, die als "erfordert Verschlüsselung," gekennzeichnet sind, klicken Sie dann `XmlKeyManager` führt diese Elemente über den konfigurierten `IXmlEncryptor`des `Encrypt` -Methode, und es bleiben enciphered Element statt über das Nur-Text-Element, um die `IXmlRepository`. Die Ausgabe der `Encrypt` Methode ist ein `EncryptedXmlInfo` Objekt. Dieses Objekt ist ein Wrapper enthält sowohl die resultierenden enciphered `XElement` und den Typ der darstellt ein `IXmlDecryptor` die verwendet werden können, um das entsprechende Element zu entschlüsseln.

Es gibt vier integrierte konkrete Typen die implementieren `IXmlEncryptor`:
* `CertificateXmlEncryptor`
* `DpapiNGXmlEncryptor`
* `DpapiXmlEncryptor`
* `NullXmlEncryptor`

Finden Sie unter der [Schlüsselverschlüsselung auf Rest Dokumentbibliotheks-](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) für Weitere Informationen.

Um den Schlüssel Verschlüsselung auf Rest Standardmechanismus anwendungsweite zu ändern, registrieren Sie ein benutzerdefiniertes `IXmlEncryptor` Instanz:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

   ```csharp
   services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
   ```
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

   ```csharp
   services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
   ```

---

## <a name="ixmldecryptor"></a>IXmlDecryptor

Die `IXmlDecryptor` Schnittstelle darstellt, einen Typ, der weiß, wie zum Entschlüsseln einer `XElement` , die über enciphered wurde ein `IXmlEncryptor`. Es stellt eine einzelne API bereit:

* Entschlüsseln ("XElement" EncryptedElement): "XElement"

Die `Decrypt` Methode macht die Verschlüsselung von `IXmlEncryptor.Encrypt`. Im Allgemeinen jede konkrete `IXmlEncryptor` Implementierung weist einen entsprechenden konkreten `IXmlDecryptor` Implementierung.

Typen der implementieren `IXmlDecryptor` sollte eines der folgenden zwei öffentlichen Konstruktoren aufweisen:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> Die `IServiceProvider` zum Übergeben des Konstruktors kann null sein.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

Die `IKeyEscrowSink` Schnittstelle darstellt, einen Typ, der hinterlegte vertraulicher Informationen ausführen kann. Beachten Sie, dass die serialisierte Deskriptoren enthalten möglicherweise vertrauliche Informationen (z. B. das kryptografische Material), und dies ist, was mit der Einführung des geführt hat die [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) ursprünglich geben. Allerdings Unfall auftreten, und Schlüssel Ringe können gelöscht oder beschädigt ist.

Die hinterlegte-Schnittstelle bietet eine Notfall Escape-Schraffur, ermöglicht den Zugriff auf die unformatierten serialisierten XML-Code vor dem Transformieren von keiner konfiguriert ist [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor). Die Schnittstelle macht eine einzige API verfügbar:

* Speicher (Guid KeyId, "XElement"-Element)

Es liegt die `IKeyEscrowSink` -Implementierung, die das bereitgestellte Element auf sichere Weise mit Geschäftsrichtlinie konsistent behandeln. Eine mögliche Implementierung konnte für die Senke werden zum Verschlüsseln von XML-Elements mit einem bekannten Unternehmens x. 509-Zertifikat sein, in dem der private Zertifikatschlüssel hinterlegt hat. die `CertificateXmlEncryptor` Typ dabei helfen kann. Die `IKeyEscrowSink` Implementierung ist auch für das bereitgestellte Element entsprechend beibehalten zuständig.

Keine hinterlegte-Mechanismus ist standardmäßig aktiviert, wenn Server-Administratoren können [konfigurieren Sie diese Option Global](xref:security/data-protection/configuration/machine-wide-policy). Sie können auch programmgesteuert programmiert werden über die `IDataProtectionBuilder.AddKeyEscrowSink` Methode, wie im folgenden Beispiel dargestellt. Die `AddKeyEscrowSink` Methode Überladungen spiegeln die `IServiceCollection.AddSingleton` und `IServiceCollection.AddInstance` Überladungen, als `IKeyEscrowSink` Instanzen sollten Singletons verwendet werden. Wenn mehrere `IKeyEscrowSink` -Instanzen registriert sind, jeweils wird während der Generierung, aufgerufen werden, damit der Schlüssel können gleichzeitig auf mehrere Mechanismen hinterlegt werden.

Es ist keine API zum Lesen von Materialien aus einer `IKeyEscrowSink` Instanz. Dies ist konsistent mit der Theorie zum Entwurf des hinterlegte angibt: beabsichtigt hat das Schlüsselmaterial einer vertrauenswürdigen Zertifizierungsstelle zu ermöglichen, und seit die Anwendung selbst keiner vertrauenswürdigen Zertifizierungsstelle ist, er keinen Zugriff haben sollten eigene escrowed Material.

Der folgende Beispielcode veranschaulicht das Erstellen und registrieren einen `IKeyEscrowSink` , in dem Schlüssel hinterlegt werden, dass nur Mitglieder der "CONTOSODomain-Admins" wiederhergestellt werden können.

> [!NOTE]
> Um dieses Beispiel ausführen zu können, muss auf eine Domäne eingebundenen Windows 8 / Windows Server 2012-Computer und dem Domänencontroller muss WindowsServer 2012 oder höher sein.

[!code-csharp[Main](key-management/samples/key-management-extensibility.cs)]
