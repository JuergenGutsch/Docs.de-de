---
title: "Verschlüsselung ruhender"
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 5d0eb4036a3d491336cbe9357779c150b5cbb236
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="key-encryption-at-rest"></a>Verschlüsselung ruhender

<a name="data-protection-implementation-key-encryption-at-rest"></a>

Wird standardmäßig die Datenschutzsystem [verwendet eine Heuristik](xref:security/data-protection/configuration/default-settings) um zu bestimmen, wie kryptografischen schlüsselmaterialien ruhende verschlüsselt werden soll. Der Entwickler kann außer Kraft setzen die Heuristik und manuell angeben, wie die Schlüssel im Ruhezustand verschlüsselt werden soll.

> [!NOTE]
> Bei Angabe ein expliziter Schlüsselverschlüsselung zur Rest-Mechanismus wird die Datenschutzsystem den Standardmechanismus für die Speicherung Registrierung aufzuheben, den von die Heuristik zur Verfügung gestellt. Sie müssen [Geben Sie eine explizite schlüsselspeichermechanismus](key-storage-providers.md#data-protection-implementation-key-storage-providers), andernfalls das Data Protection-System nicht gestartet.

<a name="data-protection-implementation-key-encryption-at-rest-providers"></a>

Die Datenschutzsystem wird mit drei wichtige Verschlüsselungsmechanismen mitgelieferten geliefert.

## <a name="windows-dpapi"></a>Windows DPAPI

*Dieser Mechanismus ist nur unter Windows verfügbar.*

Wenn Windows DPAPI verwendet wird, werden Schlüsselmaterial über verschlüsselt [CryptProtectData](https://msdn.microsoft.com/library/windows/desktop/aa380261(v=vs.85).aspx) vor dem Speicher beibehalten wird. DPAPI ist eine entsprechende Verschlüsselungsmechanismus für Daten, die niemals außerhalb des aktuellen Computers gelesen werden (obwohl es ist möglich, diese Schlüssel bis zu Active Directory zu sichern; Siehe [DPAPI und servergespeicherte Profile](https://support.microsoft.com/kb/309408/#6)). Beispiel: DPAPI-Schlüssel-at-Rest-Verschlüsselung zu konfigurieren.

```csharp
sc.AddDataProtection()
    // only the local user account can decrypt the keys
    .ProtectKeysWithDpapi();
```

Wenn `ProtectKeysWithDpapi` ohne Parameter aufgerufen wird, nur das aktuelle Windows-Benutzerkonto der persistente Schlüsselmaterial entschlüsseln kann. Optional können Sie angeben, dass jedes Benutzerkonto auf dem Computer (nicht nur das aktuelle Benutzerkonto) an das Schlüsselmaterial entschlüsseln könnte entsprechend dem folgenden Beispiel.

```csharp
sc.AddDataProtection()
    // all user accounts on the machine can decrypt the keys
    .ProtectKeysWithDpapi(protectToLocalMachine: true);
```

## <a name="x509-certificate"></a>X. 509-Zertifikat

*Dieser Mechanismus ist nicht verfügbar auf `.NET Core 1.0` oder `1.1`.*

Wenn Ihre Anwendung über mehrere Computer verteilt wird, kann es einfacher, eine gemeinsam genutzte x. 509-Zertifikat auf den Computern zu verteilen und Konfigurieren von Anwendungen für die Verwendung dieses Zertifikats für die Verschlüsselung der Schlüssel im Ruhezustand wird. Ein Beispiel finden Sie weiter unten.

```csharp
sc.AddDataProtection()
    // searches the cert store for the cert with this thumbprint
    .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
```

Aufgrund der .NET Framework-Einschränkungen werden nur Zertifikate mit privaten Schlüsseln CAPI unterstützt. Finden Sie unter [zertifikatbasierte Verschlüsselung mit Windows DPAPI-NG](#data-protection-implementation-key-encryption-at-rest-dpapi-ng) unten mögliche problemumgehungen für diese Einschränkungen.

<a name="data-protection-implementation-key-encryption-at-rest-dpapi-ng"></a>

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

*Dieser Mechanismus steht nur für Windows 8 / WindowsServer 2012 und höher.*

Ab Windows 8 unterstützt das Betriebssystem DPAPI-NG (so genannte CNG DPAPI). Microsoft legt seine Verwendungsszenario wie folgt.

   Cloud computing, erfordert jedoch häufig, dass dieser Inhalt verschlüsselte auf einem Computer auf einem anderen entschlüsselt werden. Aus diesem Grund erweitert Microsoft beginnen mit dem Windows 8, des Konzepts der eine relativ einfache API Cloud-Szenarien umfassen. Diese neue API, DPAPI-NG namens können Sie sicher freigeben geheimen Schlüssel (Schlüssel, Kennwörter, Schlüssel) und Nachrichten von Schutz auf einen Satz von Prinzipalen, die sie nach dem richtigen Authentifizierung und Autorisierung auf verschiedenen Computern den Schutz verwendet werden kann.

   Von [über DPAPI CNG](https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx)

Der Prinzipal wird als ein Deskriptor Schutzregel codiert. Betrachten Sie das folgende Beispiel, das Schlüsselmaterial verschlüsselt, so, dass nur der Domäne-Benutzer mit der angegebenen SID das Schlüsselmaterial entschlüsseln kann.

```csharp
sc.AddDataProtection()
    // uses the descriptor rule "SID=S-1-5-21-..."
    .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
    flags: DpapiNGProtectionDescriptorFlags.None);
```

Es gibt auch eine parameterlose Überladung der `ProtectKeysWithDpapiNG`. Dies ist eine bequeme Methode zum Angeben der Regelnamens "SID = extrahieren", wobei extrahieren die SID des aktuellen Windows-Benutzerkonto ist.

```csharp
sc.AddDataProtection()
    // uses the descriptor rule "SID={current account SID}"
    .ProtectKeysWithDpapiNG();
```

In diesem Szenario wird die AD-Domänencontroller für die Verteilung der Verschlüsselungsschlüssel, die von der DPAPI NG-Vorgängen verwendeten zuständig. Der Zielbenutzer sind Lage die verschlüsselte Nutzlast über eine beliebige Domäne eingebundenen Computer entschlüsseln (vorausgesetzt, dass der Prozess unter ihrer Identität ausgeführt wird).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Zertifikatbasierte Verschlüsselung mit Windows DPAPI-NG

Wenn Sie auf Windows 8.1 ausführen / Windows Server 2012 R2 oder höher, können Sie verwenden Windows DPAPI-NG zertifikatbasierte Verschlüsselung ausführen, auch wenn die Anwendung ausgeführt wird, auf [.NET Core](https://www.microsoft.com/net/core). Um diesen Vorteil zu erstellen, verwenden Sie die Regel sicherheitsbeschreibungs-Zeichenfolge "Zertifikat HashId:thumbprint =", wobei Fingerabdruck den Hexadezimal codierten SHA1-Fingerabdruck des Zertifikats zu verwenden ist. Ein Beispiel finden Sie weiter unten.

```csharp
sc.AddDataProtection()
    // searches the cert store for the cert with this thumbprint
    .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0",
        flags: DpapiNGProtectionDescriptorFlags.None);
```

Jede Anwendung, die an diesem Repository gezeigt wird, muss auf Windows 8.1 ausgeführt werden / Windows Server 2012 R2 oder höher in der Lage, diese Schlüssel zu entschlüsseln.

## <a name="custom-key-encryption"></a>Benutzerdefinierte Schlüsselverschlüsselung

Wenn die integrierten Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Mechanismus für die Schlüsselverschlüsselung angeben, durch Bereitstellen eines benutzerdefinierten `IXmlEncryptor`.
