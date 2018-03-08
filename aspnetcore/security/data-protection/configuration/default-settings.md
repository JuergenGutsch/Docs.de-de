---
title: "Data Protection schlüsselverwaltung sowie seine Lebensdauer in ASP.NET Core"
author: rick-anderson
description: "Informationen Sie zu Data Protection schlüsselverwaltung und Lebensdauer in ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 5ac2d80e7d1cebcbc792e1247608e67991ec36f4
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Data Protection schlüsselverwaltung sowie seine Lebensdauer in ASP.NET Core

<a name="data-protection-default-settings"></a>

## <a name="key-management"></a>Schlüsselverwaltung

Die app versucht, seine betriebsumgebung erkennen und Behandeln von Schlüsselkonfiguration selbst.

1. Wenn die app, in gehostet wird [Azure Apps](https://azure.microsoft.com/services/app-service/), Schlüssel werden beibehalten, um die *%HOME%\ASP.NET\DataProtection-Keys* Ordner. Dieser Ordner wird vom Netzwerkspeicher gesichert und auf allen Computern, die die app hosten synchronisiert ist.
   * Schlüssel nicht im Ruhezustand geschützt.
   * Die *DataProtection Schlüssel* Ordner stellt den Schlüssel Ring für alle Instanzen einer App in einem einzelnen bereitstellungsslot bereit.
   * Separate bereitstellungsslots, z. B. Staging und Produktion freigeben keinen Schlüssel Ring. Wenn Sie zwischen bereitstellungsslots, z. B. Staging bis zur Produktion ausgetauscht oder mithilfe von austauschen / B-Tests, werden jede app mithilfe von Datenschutz kann nicht zum Entschlüsseln von gespeicherten Daten, die mit dem Schlüssel Ring innerhalb des vorherigen Slots. Dies führt zu Benutzern, die Abmeldung von einer app, die der standardmäßigen ASP.NET Core Cookieauthentifizierung verwendet protokolliert werden, da er Schutz von Daten verwendet, um die Cookies zu schützen. Wenn Slot unabhängig Schlüssel Ringe gewünscht Verwenden eines externen Schlüsselbund-Anbieters, z. B. Azure Blob-Speicher, Azure Key Vault, eine SQL-Speicher oder Redis-Cache.

1. Wenn das Benutzerprofil verfügbar ist, werden Schlüssel beibehalten, um die *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* Ordner. Wenn das Betriebssystem Windows ist, werden die Schlüssel im Ruhezustand mit DPAPI verschlüsselt.

1. Wenn die app in IIS gehostet wird, werden die Schlüssel in einen speziellen Registrierungsschlüssel der HKLM-Registrierung beibehalten, die nur für das Arbeitsprozesskonto ACLed ist. Schlüssel werden im Ruhezustand mit DPAPI verschlüsselt.

1. Wenn keine dieser Bedingungen übereinstimmen, werden nicht außerhalb des aktuellen Prozesses Schlüssel beibehalten. Wenn der Prozess alle generierten heruntergefahren wird, sind der Schlüssel verloren.

Der Entwickler ist immer vollständige Kontrolle und überschreiben kann, wie, und wo Schlüssel gespeichert werden. Die ersten drei oben aufgeführten Optionen sollten gute Standardwerte für die meisten apps, die ähnlich wie beim Bereitstellen der ASP.NET  **\<MachineKey >** automatische Generierung Routinen in der Vergangenheit funktioniert hat. Die endgültige, fallback-Option ist das einzige Szenario, die den Entwickler angeben erfordert [Konfiguration](xref:security/data-protection/configuration/overview) vorgelagerten, wenn sie schlüsselpersistenz, aber diese Vorgehensweise ist nur in seltenen Fällen Anwendung auftritt.

Wenn Sie in einem Docker-Container zu hosten, Schlüssel beibehalten werden soll in einen Ordner, ein Docker-Volume (ein freigegebenes Volume oder eines Host bereitgestellten Volumes, die nach Ablauf des Containers Lebensdauer beibehält) ist, oder in einem externen Anbieter, wie z. B. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [Redis](https://redis.io/). Ein externer Anbieter ist auch in der Webfarm-Szenarien nützlich, wenn apps ein freigegebener Datenträger zugreifen können (siehe [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) für Weitere Informationen).

> [!WARNING]
> Wenn der Entwickler die überschreibt oben beschriebenen Regeln und der Datenschutz-System an einem bestimmten Schlüssel Repository verweist, ist automatische Verschlüsselung der Schlüssel im Ruhezustand deaktiviert. Ruhender Schutz wieder aktiviert werden kann [Konfiguration](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Gültigkeitsdauer

Standardmäßig sind Schlüssel für eine 90-Tage-Lebensdauer. Wenn ein Schlüssel abläuft, wird die app automatisch generiert einen neuen Schlüssel, und legt den neuen Schlüssel als aktiver Schlüssel fest. Als veraltet Schlüssel auf dem System bleiben, kann Ihre app mit diesen geschützten Daten entschlüsseln. Finden Sie unter [schlüsselverwaltung](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) für Weitere Informationen.

## <a name="default-algorithms"></a>Standardalgorithmen

Die Nutzlast Schutz verwendete Standardalgorithmus ist AES-256-CBC für Vertraulichkeit und HMACSHA256 für erstellen. 512-Bit-Hauptschlüssel, alle 90 Tage geändert wird verwendet, die zwei untergeordneten Schlüssel für diese Algorithmen auf der Basis eines je Nutzlast verwendet abgeleitet werden. Finden Sie unter [Unterschlüssel Ableitung](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) für Weitere Informationen.

## <a name="see-also"></a>Siehe auch

* [Schlüsselverwaltungserweiterbarkeit](xref:security/data-protection/extensibility/key-management)
