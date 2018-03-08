---
title: "Sichere Speicherung von app-Kennwörter während der Entwicklung in ASP.NET Core"
author: rick-anderson
description: "Zeigt, wie sichere Speichern von geheimen Schlüsseln während der Entwicklung"
manager: wpickett
ms.author: riande
ms.date: 09/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/app-secrets
ms.openlocfilehash: 280819a6a0afb72311f0d50f7d3b83a942e9fcc3
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a>Sichere Speicherung von app-Kennwörter während der Entwicklung in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), und [Scott Addie](https://scottaddie.com) 

Dieses Dokument wird erläutert, wie das Schlüssel-Manager-Tool in der Entwicklung können aus dem Code geheime Schlüssel beibehalten. Der wichtigste Punkt ist, sollten Sie niemals Kennwörter oder andere vertraulichen Daten im Quellcode speichern und Produktion geheime Schlüssel verwenden, im Modus für Entwicklung und Tests keine. Stattdessen können Sie die [Konfiguration](xref:fundamentals/configuration/index) System lesen Sie diese Werte von Umgebungsvariablen oder tool aus Werten, die mit dem geheimen Schlüssel-Manager gespeichert. Das Schlüssel-Manager-Tool verhindert, dass sensible Daten in die quellcodeverwaltung überprüft wird. Die [Konfiguration](xref:fundamentals/configuration/index) System kann mit dem geheimen Schlüssel-Manager-Tool, das in diesem Artikel beschriebenen gespeicherten geheimen Schlüssel gelesen.

Der geheime Schlüssel-Manager-Tool wird nur in der Entwicklung verwendet. Sie können Azure geheime Schlüssel Test- und produktionsumgebungen mit Schützen der [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Konfigurationsanbieter. Finden Sie unter [Azure Key Vault-Konfigurationsanbieter](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) für Weitere Informationen.

## <a name="environment-variables"></a>Umgebungsvariablen

Um zu vermeiden, geheime app-Schlüssel im Code oder in lokalen Konfigurationsdateien gespeichert, geheime Schlüssel in Umgebungsvariablen gespeichert. Sie können das setup die [Konfiguration](xref:fundamentals/configuration/index) Framework Lesen von Werten durch Aufrufen von Umgebungsvariablen `AddEnvironmentVariables`. Umgebungsvariablen können dann die Konfigurationswerte für alle zuvor angegebenen Konfigurationsquellen außer Kraft setzen.

Z. B. Wenn Sie eine neue ASP.NET Core-Web-app mit einzelner Benutzerkonten erstellen, dabei werden hinzugefügt. eine Standard-Verbindungszeichenfolge für die *appsettings.json* Datei im Projekt mit dem Schlüssel `DefaultConnection`. Die Standardverbindungszeichenfolge bezieht Setup LocalDB, verwendet wird, die im Benutzermodus ausgeführt wird und ein Kennwort erfordert. Wenn Sie Ihre Anwendung auf einem Test- oder Produktionscomputer Server bereitstellen, können Sie Sie überschreiben die `DefaultConnection` -Schlüsselwert mit einer Einstellung der Umgebungsvariablen, die die Verbindungszeichenfolge (u. u. mit sensible Anmeldeinformationen) für eine Test- oder Produktionscomputer Datenbank enthält Server.

>[!WARNING]
> Umgebungsvariablen werden im Allgemeinen in Klartext gespeichert und werden nicht verschlüsselt. Wenn der Computer oder einen Prozess beschädigt ist, können von nicht vertrauenswürdigen Parteien Umgebungsvariablen zugegriffen werden. Zusätzliche Maßnahmen, um die Offenlegung von vertrauliche Benutzerdaten zu verhindern, dass möglicherweise weiterhin erforderlich.

## <a name="secret-manager"></a>Geheime Schlüssel-Manager

Das Schlüssel-Manager-Tool speichert die sensible Daten für andere Entwicklungen außerhalb der Projektstruktur. Das Schlüssel-Manager-Tool ist ein Verwaltungstool, das verwendet werden kann, Speichern von geheimen Schlüsseln für eine [.NET Core](https://www.microsoft.com/net/core) Projekt während der Entwicklung. Mit dem geheimen Schlüssel-Manager-Tool können Sie ein bestimmtes Projekt geheime app-Schlüssel zuordnen und diese projektübergreifend freigeben.

>[!WARNING]
> Der geheime Schlüssel-Manager-Tool nicht die gespeicherten geheimen Schlüssel zum Verschlüsseln und sollte nicht als vertrauenswürdigen Speicher behandelt werden. Es ist nur für Entwicklungszwecke. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Verzeichnis Benutzers-Profil gespeichert.

## <a name="installing-the-secret-manager-tool"></a>Installieren den geheimen Schlüssel-Manager

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Mit der rechten Maustaste des Projekts im Projektmappen-Explorer, und wählen Sie **bearbeiten \<Project_name\>csproj** aus dem Kontextmenü. Fügen Sie die hervorgehobene Zeile auf die *csproj* Datei, und speichern, um das zugehörige NuGet-Paket wiederherstellen:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

Mit der rechten Maustaste erneut auf des Projekts im Projektmappen-Explorer, und wählen Sie **verwalten Benutzer geheime Schlüssel** aus dem Kontextmenü. Diese Bewegung wird ein neues `UserSecretsId` Knoten innerhalb einer `PropertyGroup` von der *csproj* Datei, wie im folgenden Beispiel verdeutlicht:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

Speichern der geänderten *csproj* Datei auch öffnet eine `secrets.json` Datei im Text-Editor. Ersetzen Sie den Inhalt von der `secrets.json` Datei durch den folgenden Code:

```json
{
    "MySecret": "ValueOfMySecret"
}
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hinzufügen `Microsoft.Extensions.SecretManager.Tools` auf die *csproj* , und führen Sie `dotnet restore`. Die gleichen Schritte können Sie um das für die Befehlszeile mit Schlüssel-Manager-Tool zu installieren.

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

Testen Sie das Schlüssel-Manager-Tool, indem Sie den folgenden Befehl ausführen:

```console
dotnet user-secrets -h
```

Das Schlüssel-Manager-Tool werden Verwendung, Optionen und Befehl Hilfe angezeigt.

> [!NOTE]
> Müssen Sie sich im selben Verzeichnis wie die *csproj* Datei im definierten Tools ausführen, die *csproj* Datei `DotNetCliToolReference` Knoten.

Das Schlüssel-Manager-Tool verarbeitet projektspezifische Konfigurationseinstellungen, die in Ihrem Profil gespeichert sind. Um Benutzer geheime Schlüssel zu verwenden, das Projekt angegeben, muss ein `UserSecretsId` Wert in seine *csproj* Datei. Der Wert des `UserSecretsId` ist willkürlich, jedoch ist für das Projekt in der Regel eindeutig. Entwickler in der Regel generiert eine GUID für die `UserSecretsId`.

Hinzufügen einer `UserSecretsId` für das Projekt in der *csproj* Datei:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

Verwenden Sie den geheimen Schlüssel-Manager, um einen geheimen Schlüssel festzulegen. Geben Sie in einem Befehlsfenster aus dem Projektverzeichnis beispielsweise Folgendes ein:

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

Sie können das Schlüssel-Manager-Tool ausführen, aus anderen Verzeichnissen, aber Sie müssen die `--project` Option aus, um den Pfad zum Übergeben der *csproj* Datei:
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

Sie können auch das Tool Secret-Manager angezeigt wird, entfernen und app-Kennwörter zu löschen.

-----

## <a name="accessing-user-secrets-via-configuration"></a>Zugreifen auf vertrauliche Benutzerdaten über die Konfiguration

Sie können die geheimen Schlüssel-Manager über das Konfigurationssystem zugreifen. Hinzufügen der `Microsoft.Extensions.Configuration.UserSecrets` Verpacken und ausführen `dotnet restore`.

Hinzufügen der Benutzer geheime Schlüssel Konfigurationsquelle angibt, die `Startup` Methode:

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

Sie können Benutzer geheime Schlüssel über den Konfigurations-API zugreifen:

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a>Wie funktioniert das Schlüssel-Manager-tool

Das Schlüssel-Manager-Tool abstrahiert die Implementierungsdetails, z. B., wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails. In der aktuellen Version werden die Werte gespeichert, einem [JSON](http://json.org/) Konfigurationsdatei im Verzeichnis Benutzers-Profil:

* Windows: `%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`

* Linux: `~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

* Mac: `~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

Der Wert der `userSecretsId` stammt aus dem Wert im angegebenen *csproj* Datei.

Sie sollten nicht Code schreiben, der auf dem Speicherort oder das Format der Daten gespeichert werden, mit dem geheimen Schlüssel-Manager-Tool abhängig ist, da diese Implementierungsdetails ändern können. Beispielsweise sind die Werte für den geheimen derzeit *nicht* heute verschlüsselt, aber irgendwann werden konnte.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Konfiguration](xref:fundamentals/configuration/index)
