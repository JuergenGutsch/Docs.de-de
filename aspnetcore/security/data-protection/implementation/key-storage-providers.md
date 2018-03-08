---
title: "In softwareschlüsselspeicher-Anbieter"
author: rick-anderson
description: "In softwareschlüsselspeicher-Anbieter"
manager: wpickett
ms.author: riande
ms.date: 01/14/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 952fc99d2486b8e78d97356c333257e581a79b42
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="key-storage-providers"></a>In softwareschlüsselspeicher-Anbieter

<a name="data-protection-implementation-key-storage-providers"></a>

Standardmäßig die Datenschutzsystem [verwendet eine Heuristik](xref:security/data-protection/configuration/default-settings) um zu bestimmen, wo die kryptografischen schlüsselmaterialien beibehalten werden soll. Der Entwickler kann außer Kraft setzen die Heuristik und manuell Geben Sie den Speicherort.

> [!NOTE]
> Wenn Sie einen expliziten schlüsselpersistenz-Ort angeben, wird die Datenschutzsystem Aufheben der Registrierung der wichtigsten standardverschlüsselung zur Rest-Mechanismus, der die Heuristik zur Verfügung gestellt, sodass Schlüssel nicht mehr im Ruhezustand verschlüsselt werden. Es wird empfohlen, die Sie darüber hinaus [geben den Kommunikationsmechanismus für eine explizite Schlüsselverschlüsselung](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers) für Produktionsanwendungen.

Im Lieferumfang von der Datenschutzsystem sind mehrere integrierte-Schlüsselspeicher-Anbieter.

## <a name="file-system"></a>Dateisystem

Wir erwarten, dass viele apps Datei systembasierten Key-Repository verwendet. Um dies zu konfigurieren, rufen Sie die [PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) Konfiguration Routine, wie unten dargestellt. Geben Sie einen `DirectoryInfo` verweist auf das Repository, in dem Schlüssel gespeichert werden sollen.

```csharp
sc.AddDataProtection()
       // persist keys to a specific directory
       .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
   ```

Die `DirectoryInfo` kann auf ein Verzeichnis auf dem lokalen Computer verweisen, oder kann auf einen Ordner auf einer Netzwerkfreigabe zeigen. Wenn verweist auf ein Verzeichnis auf dem lokalen Computer (und das Szenario liegt vor, dass nur die Anwendungen auf dem lokalen Computer diesem Repository verwendet müssen), erwägen Sie [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) zum Verschlüsseln der Schlüssel im Ruhezustand. Erwägen Sie andernfalls mit einem [x. 509-Zertifikat](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) um ruhende Verschlüsselung.

## <a name="azure-and-redis"></a>Azure und Redis

Die `Microsoft.AspNetCore.DataProtection.AzureStorage` und `Microsoft.AspNetCore.DataProtection.Redis` Pakete zu ermöglichen, Ihre Data Protection-Schlüssel in Azure-Speicher oder eine Redis-Cache gespeichert. Schlüssel können über mehrere Instanzen von einer Web-app freigegeben werden. Ihre app ASP.NET Core kann Authentifizierungscookies oder CSRF-Schutz für mehrere Server freigeben. Um auf Azure zu konfigurieren, rufen Sie eine von der [PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs) überlädt, wie unten dargestellt.

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));

    services.AddMvc();
}
```

Siehe auch die [Azure Testcode](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs).

Um auf Redis konfigurieren, rufen Sie eine von der [PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs) überlädt, wie unten dargestellt.

```
public void ConfigureServices(IServiceCollection services)
{
    // Connect to Redis database.
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");

    services.AddMvc();
}
```

Über die folgenden Links erhalten Sie weitere Informationen:

- [StackExchange.Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
- [Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
- [Redis Testcode](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs).

## <a name="registry"></a>Registrierung

In einigen Fällen möglicherweise die app nicht Schreibzugriff auf das Dateisystem. Betrachten Sie ein Szenario, in denen eine app als virtuelle Dienstkonto (z. B. die app-Anwendungspoolidentität des W3wp.exe) ausgeführt wird. In diesen Fällen ist kann der Administrator einen Registrierungsschlüssel bereitgestellt haben, der entsprechenden ACLed für die Dienstidentität für das Konto ist. Rufen Sie die [PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) Konfiguration Routine, wie unten dargestellt. Geben Sie einen `RegistryKey` verweist auf den Speicherort, in dem kryptografischen Schlüssel/Werte gespeichert werden sollen.

```csharp
   sc.AddDataProtection()
       // persist keys to a specific location in the system registry
       .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
   ```

Wenn Sie die Registrierung des Systems als Mechanismus für die Persistenz verwenden, erwägen Sie [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) zum Verschlüsseln der Schlüssel im Ruhezustand.

## <a name="custom-key-repository"></a>Benutzerdefinierte Schlüssel-repository

Wenn die integrierten Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Mechanismus schlüsselpersistenz angeben, durch Bereitstellen eines benutzerdefinierten `IXmlRepository`.
