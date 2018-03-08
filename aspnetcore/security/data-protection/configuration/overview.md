---
title: Konfigurieren des Datenschutzes in ASP.NET Core
author: rick-anderson
description: Informationen Sie zum Datenschutz in ASP.NET Core konfigurieren.
manager: wpickett
ms.author: riande
ms.date: 07/17/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/configuration/overview
ms.openlocfilehash: d35e0e806999ffd2e0f8f82e0adfc940ea2b503d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="configuring-data-protection-in-aspnet-core"></a>Konfigurieren des Datenschutzes in ASP.NET Core

<a name="data-protection-configuring"></a>

Wenn das System den Datenschutz initialisiert wird, gilt es [Standardeinstellungen](xref:security/data-protection/configuration/default-settings) basierend auf der betriebsumgebung. Diese Einstellungen sind im Allgemeinen geeignet für apps, die auf einem einzelnen Computer ausgeführt. Es gibt Fälle, in denen ein Entwickler möchten vielleicht die Standardeinstellungen geändert werden, da es sich bei ihrer app auf mehrere Computer oder aus Compliance-Gründen verteilt wird. Für die folgenden Szenarien bietet das Data Protection-System eine umfangreiche Konfigurations-API.

<a name="data-protection-configuration-callback"></a>

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Zum Speichern von Schlüsseln auf einer UNC-Freigabe statt an den *%LocalAppData%* Standardspeicherort, konfigurieren Sie das System mit [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Wenn Sie den Speicherort der schlüsselpersistenz ändern, verschlüsselt das System nicht mehr automatisch Schlüssel im Ruhezustand, da er nicht weiß, ob eine entsprechende Verschlüsselungsmechanismus bei DPAPI handelt es.

<a name="configuring-x509-certificate"></a>

Sie können konfigurieren, dass Schlüssel im Ruhezustand zu schützen, durch das Aufrufen einer der im System die [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) Konfigurations-APIs. Betrachten Sie das folgenden Beispiel wird die Tasten auf einer UNC-Freigabe gespeichert und verschlüsselt den Schlüssel im Ruhezustand mit einem bestimmten x. 509-Zertifikat aus:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

Finden Sie unter [Schlüssel Verschlüsselung ruhender](xref:security/data-protection/implementation/key-encryption-at-rest) Weitere Beispiele und detaillierte Informationen zu den integrierten Schlüsselverschlüsselung Mechanismen.

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Verwenden Sie zum Konfigurieren der im System eine Lebensdauer von 14 Tagen verwenden Sie anstelle des standardmäßigen 90 Tage [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

<a name="data-protection-code-sample-application-name"></a>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

<a name="data-protection-configuring-disable-automatic-key-generation"></a>

Sie möglicherweise ein Szenario, in dem Sie nicht möchten eine app an (zum Erstellen neuer Schlüssel) Schlüssel automatisch zurücksetzen, wie sie Ablaufdatum nähern. Ein Beispiel hierfür ist möglicherweise apps, die in einer Beziehung primär/Sekundär, in dem nur die primäre app ist verantwortlich für schlüsselverwaltung Bedenken und sekundären apps haben einfach eine schreibgeschützte Ansicht des Rings Schlüssel einrichten. Die sekundären apps konfiguriert werden können um der Schlüssel Ring als schreibgeschützt zu behandeln, indem Sie die Konfiguration des Systems mit [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

<a name="data-protection-configuration-per-app-isolation"></a>

## <a name="per-application-isolation"></a>Pro Anwendungsisolation

Wenn die Datenschutz-System von einem ASP.NET Core-Host bereitgestellt wird, isoliert es automatisch apps von einem anderen, selbst wenn diese apps mit der gleichen Arbeitsprozesskonto ausgeführt werden und die gleiche Schlüsselmaterial verwenden. Dies ist zumindest ähneln den IsolateApps-Modifizierer aus System.Web  **\<MachineKey >** Element.

Der Mechanismus für die Sicherheitsisolation Hinblick auf jede app auf dem lokalen Computer als einen eindeutigen Mandanten daher funktioniert die [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) Stamm eine bestimmte app automatisch als Diskriminator app-ID enthält. Eindeutige ID für die app stammt aus einer von zwei Stellen:

1. Wenn die app in IIS gehostet wird, ist der eindeutige Bezeichner der app-Konfigurationspfad an. Wenn eine app in einer webfarmumgebung bereitgestellt wird, sollte dieser Wert stabil sein, vorausgesetzt, dass die IIS-Umgebungen für alle Computer in der Webfarm auf ähnliche Weise konfiguriert werden.

2. Wenn die app in IIS gehostet wird nicht, ist der eindeutige Bezeichner der physische Pfad der app an.

Der eindeutige Bezeichner dient zum Zurücksetzen von Kennwörtern überstehen &mdash; sowohl der einzelnen app und der Computer.

Dieser Mechanismus für die Sicherheitsisolation wird davon ausgegangen, dass die apps nicht bösartig sind. Eine böswillige Anwendung beeinträchtigt immer jede andere app unter der gleichen Arbeitsprozesskonto ausgeführt wird. In einer freigegebenen Hostingumgebung nicht gegenseitig vertrauenswürdigen apps gegangenem geboten des hostenden Anbieters Schritte, um auf Betriebssystemebene Isolation zwischen apps, einschließlich trennen, die apps zugrunde liegenden Key Repositorys sicherzustellen.

<a name="data-protection-changing-algorithms"></a>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Algorithmen mit UseCryptographicAlgorithms ändern

Der Datenschutz-Stapel können Sie ändern, der durch den neu generierten Schlüssel verwendete Standardalgorithmus. Die einfachste Möglichkeit hierzu ist das Aufrufen [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) vom Rückruf Konfiguration:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

Der Standardwert "EncryptionAlgorithm" ist AES-256-CBC, und der Standardwert ValidationAlgorithm ist HMACSHA256. Die Standardrichtlinie kann festgelegt werden, von einem Systemadministrator über eine [computerweite Richtlinie](xref:security/data-protection/configuration/machine-wide-policy), aber ein expliziter Aufruf von `UseCryptographicAlgorithms` überschreibt der Standardrichtlinie.

Aufrufen von `UseCryptographicAlgorithms` können Sie den gewünschten Algorithmus aus einer vordefinierten integrierten Liste angeben. Sie müssen nicht die Implementierung des Algorithmus kümmern. Im obigen Szenario versucht Datenschutz, der CNG-Implementierung von AES zu verwenden, wenn unter Windows ausgeführt wird. Andernfalls es fragt die verwaltete [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) Klasse.

Sie können manuell angeben, um über einen Aufruf an eine Implementierung [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

>[!TIP]
> Ändern die Algorithmen wirkt sich nicht auf den vorhandenen Schlüssel im Schlüssel Ring aus. Er wirkt sich nur auf neu generierten Schlüssel.

<a name="data-protection-changing-algorithms-custom-managed"></a>

### <a name="specifying-custom-managed-algorithms"></a>Angeben von benutzerdefinierten verwalteten Algorithmen

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Um benutzerdefinierte Algorithmen, die verwaltete anzugeben, erstellen Sie eine [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) Instanz, die auf die Implementierungstypen verweist:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Um benutzerdefinierte Algorithmen, die verwaltete anzugeben, erstellen Sie eine [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) Instanz, die auf die Implementierungstypen verweist:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

---

Im Allgemeinen die \*Typeigenschaften müssen auf konkrete, zeigen instanziierbaren (über einen öffentlichen parameterlosen Ctor) Implementierungen von [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) und [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), obwohl die System spezielle-Fällen einige Werte wie `typeof(Aes)` der Einfachheit halber.

> [!NOTE]
> Die SymmetricAlgorithm muss eine Schlüssellänge von ≥ 128 Bits und eine Blockgröße von ≥ 64 Bits haben, und es muss CBC-Modus-Verschlüsselung mit PKCS #7-Auffüllung unterstützen. Der KeyedHashAlgorithm benötigen Nachrichtenhash Größe > = 128 Bits und müssen Schlüssel, der den Hashalgorithmus Digestlänge gleich Länge unterstützt. Die KeyedHashAlgorithm ist nicht zwingend erforderlich, HMAC sein.

<a name="data-protection-changing-algorithms-cng"></a>

### <a name="specifying-custom-windows-cng-algorithms"></a>Angeben von benutzerdefinierten Windows CNG-Algorithmen

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Um einen benutzerdefinierten Windows CNG-Algorithmus HMAC-Validierung mit Verschlüsselung im CBC-Modus anzugeben, erstellen eine [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) Instanz, die die algorithmische Informationen enthält:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Um einen benutzerdefinierten Windows CNG-Algorithmus HMAC-Validierung mit Verschlüsselung im CBC-Modus anzugeben, erstellen eine [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) Instanz, die die algorithmische Informationen enthält:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

---

> [!NOTE]
> Der Block symmetrischen Verschlüsselungsalgorithmus benötigen eine Schlüssellänge von > = 128 Bits und eine Blockgröße von > = 64 Bits und müssen entdeckt CBC-Modus-Verschlüsselung mit PKCS #7-Auffüllung. Der Hashalgorithmus benötigen Nachrichtenhash Größe von > = 128 Bits und müssen unterstützen geöffnet, die mit dem symmetrischen BCRYPT\_"alg"\_BEHANDELN\_HMAC\_FLAG-Flag. Die \*Anbietereigenschaften können werden auf null festgelegt, um den Standardanbieter für den angegebenen Algorithmus zu verwenden. Finden Sie unter der [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Dokumentation weitere Informationen.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Erstellen Sie zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus Überprüfung Galois/Counter-Modus-Verschlüsselung mit einem [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) Instanz, die die algorithmische Informationen enthält:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Erstellen Sie zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus Überprüfung Galois/Counter-Modus-Verschlüsselung mit einem [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) Instanz, die die algorithmische Informationen enthält:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

---

> [!NOTE]
> Der Block symmetrischen Verschlüsselungsalgorithmus benötigen eine Schlüssellänge von > = 128 Bits und eine Blockgröße von genau 128 Bits und müssen GCM-Verschlüsselung unterstützt. Sie können festlegen, die [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) -Eigenschaft auf null, um den Standardanbieter für den angegebenen Algorithmus zu verwenden. Finden Sie unter der [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Dokumentation weitere Informationen.

### <a name="specifying-other-custom-algorithms"></a>Andere benutzerdefinierten Algorithmen angeben

Obwohl nicht als eine erstklassige-API verfügbar gemacht, ist das Data Protection-System extensible genug, um zu ermöglichen, nahezu jede Art von Algorithmus angibt. Beispielsweise ist es möglich, alle Schlüssel in einem Hardwaresicherheitsmodul (HSM) enthaltenen zu behalten und eine benutzerdefinierte Implementierung des Kerns Ver- und Entschlüsselung Routinen bereitstellen. Finden Sie unter [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core kryptografieerweiterbarkeit](xref:security/data-protection/extensibility/core-crypto) für Weitere Informationen.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Beibehalten von Schlüsseln, wenn in einem Docker-Container gehostet wird.

Beim Hosten in einer [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) Container Schlüssel sollte entweder beibehalten werden:

* Einen Ordner, der ein Docker-Volume ist, die nach Ablauf des Containers Lebensdauer, z. B. ein freigegebenes Volume oder einem Host bereitgestellten Volume beibehält.
* Ein externer Anbieter, wie z. B. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [Redis](https://redis.io/).

## <a name="see-also"></a>Siehe auch

* [Szenarios, die die Abhängigkeitsinjektion nicht beachten](xref:security/data-protection/configuration/non-di-scenarios)
* [Computerübergreifende Richtlinie](xref:security/data-protection/configuration/machine-wide-policy)
