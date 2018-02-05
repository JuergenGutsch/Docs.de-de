---
title: Razor-Pages mit EF-Kern - Migrationen - 4 von 8
author: rick-anderson
description: "In diesem Lernprogramm beginnen Sie mit der EF-Core-Migrationen-Funktion für die Verwaltung von datenmodelländerungen in einer ASP.NET-MVC-Anwendung Core."
manager: wpickett
ms.author: riande
ms.date: 10/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/migrations
ms.openlocfilehash: e89d95702cb94556bc6e5dc73253c51acaa11578
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="migrations---ef-core-with-razor-pages-tutorial-4-of-8"></a>Migrationen - EF-Core mit Razor-Pages Lernprogramm (4 von 8)

Durch [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[about the series](../../includes/RP-EF/intro.md)]

In diesem Lernprogramm wird das EF Hauptfeature-Migrationen für die Verwaltung von datenmodelländerungen verwendet.

Wenn Probleme können nicht zu lösen auftreten, laden Sie die [abgeschlossene Anwendung für diese Stufe](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).

Wenn eine neue app entwickelt wird, werden Änderungen in die Daten häufig modellieren. Jedes Mal Ruft das Modell ändert, das Modell nicht synchron mit der Datenbank. Dieses Lernprogramm gestartet, indem Sie die Konfiguration des Entity Framework zum Erstellen der Datenbank, wenn er nicht vorhanden ist. Jedes Mal, die das Datenmodell ändert:

* Die Datenbank wird gelöscht.
* EF erstellt ein neues Konto, das das Modell entspricht.
* Die app startet die Datenbank mit der Testdaten.

Dieser Ansatz zum Synchronisieren der Datenbank mit dem Datenmodell funktioniert gut, bis Sie die app in die Produktion bereitstellen. Wenn die app in der produktionsumgebung ausgeführt wird, wird es in der Regel Daten speichern, die beibehalten werden muss. Die app kann nicht in einem DB-Test starten jedes Mal eine Änderung vorgenommen wird, (z. B. das Hinzufügen einer neuen Spalte). Das Feature EF Core Migrationen löst dieses Problem durch Aktivieren von EF Core zum Aktualisieren des Schemas DB, anstatt eine neue Datenbank erstellen.

Anstatt löschen und Neuerstellen der Datenbank, wenn das Datenmodell ändert, werden Migrationen aktualisiert das Schema und behält die vorhandene Daten.

## <a name="entity-framework-core-nuget-packages-for-migrations"></a>NuGet-Pakete für Migrationen in Entity Framework Core

Verwenden Sie zum Arbeiten mit Migrationen der **Package Manager Console** (PMC) oder die Befehlszeilenschnittstelle (CLI). Diese Lernprogramme veranschaulichen, wie CLI-Befehlen. Informationen zu der Systemmonitor ist am [Ende dieses Lernprogramms](#pmc).

Der EF-Core-Tools für die Befehlszeilenschnittstelle (CLI) finden Sie unter [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet). Um dieses Paket zu installieren, fügen sie der `DotNetCliToolReference` Sammlung in der *csproj* Datei wie gezeigt. **Hinweis:** dieses Paket muss installiert werden, indem Sie bearbeiten die *csproj* Datei. Die`install-package` Befehl oder die Paket-Manager-GUI nicht zum Installieren dieses Pakets verwendet werden. Bearbeiten der *csproj* Datei mit der rechten Maustaste auf den Projektnamen im **Projektmappen-Explorer** auswählen und **ContosoUniversity.csproj bearbeiten**.

Das folgende Markup zeigt die aktualisierte *csproj* Datei mit hervorgehobenen EF Core CLI-Tools:

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?highlight=12)]
  
Die Versionsnummern im vorherigen Beispiel wurden beim Erstellen des Lernprogramms geschrieben wurde. Verwenden Sie die gleiche Version, für die EF Core CLI-Tools, die in die anderen Pakete gefunden.

## <a name="change-the-connection-string"></a>Ändern der Verbindungszeichenfolge

In der *appsettings.json* Datei, ändern Sie den Namen der Datenbank in der Verbindungszeichenfolge auf ContosoUniversity2.

[!code-json[Main](intro/samples/cu/appsettings2.json?range=1-4)]

Ändern der DB-Name in der Verbindungszeichenfolge bewirkt, dass die erste Migration zu eine neue Datenbank zu erstellen. Eine neue Datenbank wird erstellt, da eine mit diesem Namen vorhanden ist. Die Verbindungszeichenfolge ändern, ist nicht erforderlich für erste Schritte mit Migrationen.

Eine Alternative zum Ändern der DB-Name ist die Datenbank gelöscht. Verwendung **Objekt-Explorer von SQL Server** (SSOX) oder die `database drop` CLI-Befehl:

 ```console
 dotnet ef database drop
 ```

Im folgende Abschnitt wird erläutert, wie CLI-Befehle ausgeführt werden.

## <a name="create-an-initial-migration"></a>Erstellen einer anfänglichen Migrations

Erstellen Sie das Projekt.

Öffnen Sie ein Befehlsfenster, und navigieren Sie zum Projektordner. Enthält der Projektordner der *Startup.cs* Datei.

Geben Sie im Fenster Eingabeaufforderung Folgendes ein:

```console
dotnet ef migrations add InitialCreate
```

Das Befehlsfenster werden Informationen, die etwa wie folgt angezeigt:

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

Bei einem mit der Meldung Migrationsfehler "*... die Datei kann nicht zugegriffen werden. ContosoUniversity.dll, da sie von einem anderen Prozess verwendet wird.* " wird angezeigt:

* Beenden von IIS Express.

   * Beenden und starten Sie Visual Studio oder
   * Suchen Sie das Symbol "IIS Express" in der Windows-Taskleiste.
   * Maustaste auf das Symbol "IIS Express", und klicken Sie dann auf **ContosoUniversity > Standort beenden**.

Wenn die Fehlermeldung "Fehler beim Erstellen des." wird angezeigt, führen den Befehl erneut aus. Wenn Sie diesen Fehler erhalten, lassen Sie sich am Ende dieses Lernprogramms.

### <a name="examine-the-up-and-down-methods"></a>Überprüfen Sie die nach-oben und nach-unten Sie-Methoden

Der EF-Core-Befehl `migrations add` generiert Code zum Erstellen der Datenbank aus. Dieser Code Migrationen befindet sich in der *Migrationen\<Zeitstempel > _InitialCreate.cs* Datei. Die `Up` Methode der `InitialCreate` Klasse erstellt, die DB-Tabellen, die die Daten Modell Entitätenmengen entsprechen. Die `Down` Methode löscht, wie im folgenden Beispiel gezeigt:

[!code-csharp[Main](intro/samples/cu/Migrations/20171026010210_InitialCreate.cs?range=8-24,77-)]

Migrationen Aufrufe der `Up` Methode für die Implementierung des Datenmodells für eine Migration. Bei der Eingabe eines Befehls ein Rollback der Updates, die Migrationen Aufrufe der `Down` Methode.

Der vorhergehende Code ist der anfänglichen Migration. Dieser Code wurde erstellt, wenn die `migrations add InitialCreate` -Befehl ausgeführt wurde. Der Namensparameter der Migration (im Beispiel "InitialCreate") wird für den Dateinamen verwendet. Der migrationsname kann einen beliebigen gültigen Dateinamen. Es wird empfohlen, wählen ein Wort oder Ausdruck, die zusammengefasst, was bei der Migration durchgeführt wird. Beispielsweise könnte eine Migration, die Department-Tabelle hinzugefügt "AddDepartmentTable." aufgerufen werden

Wenn die anfängliche Migration erstellt wird und die Datenbank beendet wird:

* Der DB-Erstellung-Code wird generiert.
* Der DB-Erstellung Code muss nicht ausgeführt werden, da die Datenbank bereits das Datenmodell übereinstimmt. Wenn der DB-Erstellung Code ausgeführt wird, nicht der Änderungen vorgenommen werden, da die Datenbank bereits das Datenmodell übereinstimmt.

Wenn die app zu einer neuen Umgebung bereitgestellt wird, muss die DB-Erstellung Code ausgeführt werden, um die Datenbank zu erstellen.

Zuvor wurde die Verbindungszeichenfolge geändert, um einen neuen Namen für die Datenbank zu verwenden. Die angegebene Datenbank ist nicht vorhanden, damit Migrationen erstellt die Datenbank.

### <a name="examine-the-data-model-snapshot"></a>Überprüfen Sie die datenmomentaufnahme für das Modell

Migrationen erstellt eine *Momentaufnahme* des aktuellen DB-Schemas in *Migrations/SchoolContextModelSnapshot.cs*:

[!code-csharp[Main](intro/samples/cu/Migrations/SchoolContextModelSnapshot1.cs?name=snippet_Truncate)]

Da das Schema der aktuellen im Code dargestellt wird, verwendet nicht EF Kern, für die Interaktion mit der Datenbank, um Migrationen zu erstellen. Wenn Sie eine Migration hinzufügen, bestimmt EF Core Änderungen durch das Datenmodell auf der Snapshot-Datei vergleichen. EF Core interagiert mit der Datenbank, nur bei die Datenbank zu aktualisieren.

Der Datenbankmomentaufnahme-Datei muss mit die Migrationen synchron sein, der Sie erstellt. Eine Migration kann nicht entfernt werden, durch Löschen der Datei mit dem Namen  *\<Zeitstempel > _\<Migrationname > .cs*. Wenn diese Datei gelöscht wird, werden die übrigen Migrationen werden nicht synchron mit der Snapshot-Datenbankdatei. Verwenden Sie zum Löschen der letzten Migrations hinzugefügt der [Dotnet Ef Migrationen entfernen](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) Befehl.

## <a name="remove-ensurecreated"></a>EnsureCreated entfernen

Bei frühen-Bereitstellung müssen die `EnsureCreated` -Befehl wurde verwendet. In diesem Lernprogramm wird die Migrationen verwendet. `EnsureCreated`verfügt über die folgenden Einschränkungen:

* Migrationen umgangen wird und die DB- und das Schema erstellt.
* Eine Tabelle Migrationen nicht erstellt werden.
* Können *nicht* mit Migrationen verwendet werden.
* Dient zum Testen oder schnellen Prototyping, in dem die Datenbank gelöscht und neu erstellt häufig.

Entfernen Sie die folgende Zeile aus `DbInitializer`:

```csharp
context.Database.EnsureCreated();
```

## <a name="apply-the-migration-to-the-db-in-development"></a>Übernehmen Sie die Migration mit der Datenbank in der Entwicklung

Geben Sie Folgendes ein, um die DB- und Tabellen zu erstellen, in das Befehlsfenster.

```console
dotnet ef database update
```

Hinweis: Wenn die `update` Befehl gibt dem Fehler "Fehler beim Buildvorgang.":

* Führen Sie den Befehl erneut aus.
* Wenn es wieder fehlschlägt, beenden Sie Visual Studio, und führen Sie dann die `update` Befehl.
* Lassen Sie eine Nachricht am unteren Rand der Seite.

Die Ausgabe des Befehls ähnelt der `migrations add` Befehl Ausgabe. In den vorherigen Befehl werden die Protokolle für die SQL-Befehle, mit dem die Datenbank eingerichtet angezeigt. Die meisten Protokolle werden in der folgenden Beispielausgabe ausgelassen:

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

Zum Reduzieren der Detailebene in Protokollnachrichten enthalten, kann die Protokollierungsstufen im Ändern der *"appSettings". Development.JSON* Datei. Weitere Informationen finden Sie unter [Einführung in die Protokollierung](xref:fundamentals/logging/index).

Verwendung **Objekt-Explorer von SQL Server** , die Datenbank zu überprüfen. Beachten Sie das Hinzufügen einer `__EFMigrationsHistory` Tabelle. Die `__EFMigrationsHistory` Tabelle der nachverfolgt welche Migrationen auf die Datenbank angewendet wurden. Zeigen Sie die Daten in der `__EFMigrationsHistory` Tabelle wird eine Zeile für die erste Migration gezeigt. Das letzte Protokoll im vorherigen Beispiel der CLI-Ausgabe zeigt die INSERT-Anweisung, die diese Zeile erstellt.

Führen Sie die app, und stellen Sie sicher, dass alles funktioniert.

## <a name="appling-migrations-in-production"></a>Appling Migrationen in der Produktion

Es wird empfohlen, sollte der Produktion apps **nicht** Aufrufen [Database.Migrate](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) beim Anwendungsstart. `Migrate`darf nicht von einer app in der Serverfarm aufgerufen werden. Beispielsweise, wenn die app wurde Cloud mit horizontaler Skalierung (mehrere Instanzen der app ausgeführt werden) bereitgestellt.

Datenbankmigration sollte im Rahmen der Bereitstellung, und klicken Sie auf kontrollierte Weise erfolgen. Produktion Datenbank Migrationsansätze gehören:

* Migrationen zum SQL-Skripts erstellen und verwenden die SQL-Skripts in der Bereitstellung.
* Ausführen `dotnet ef database update` aus einer kontrollierten Umgebung.

EF Kern verwendet die `__MigrationsHistory` Tabelle, um festzustellen, ob alle Migrationen ausführen müssen. Wenn die Datenbank auf dem neuesten Stand ist, wird keine Migration ausgeführt.

<a id="pmc"></a>
## <a name="command-line-interface-cli-vs-package-manager-console-pmc"></a>Im Vergleich zur Befehlszeilenschnittstelle (CLI) Paket-Manager-Konsole (PMC)

Die Tools zum Verwalten von Migrationen EF Core steht aus:

* .NET Core-CLI-Befehlen.
* Die PowerShell-Cmdlets in der Visual Studio **Package Manager Console** (PMC) Fenster.

Dieses Lernprogramm zeigt, wie die CLI, einige Entwickler bevorzugen, mit der Systemmonitor.

Die EF Kernbefehle für die PMC befinden sich in der [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) Paket. Dieses Paket ist in enthalten die [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) Metapackage, daher ist es nicht, es zu installieren.

**Wichtig:** dieses Element wird nicht das gleiche Paket mit dem Sie für die CLI, indem Sie die Bearbeitung Installieren der *csproj* Datei. Der Name dieser endet `Tools`, im Gegensatz zu den CLI-Paketnamen die endet in `Tools.DotNet`.

Weitere Informationen zu CLI-Befehlen finden Sie unter [.NET Core CLI](https://docs.microsoft.com/ef/core/miscellaneous/cli/dotnet).

Weitere Informationen zu den PMC-Befehlen finden Sie unter [Package Manager Console (Visual Studio)](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).

## <a name="troubleshooting"></a>Problembehandlung

Herunterladen der [abgeschlossene Anwendung für diese Stufe](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).

Die app wird die folgende Ausnahme generiert:

```text
`SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Lösung: Ausführen`dotnet ef database update`

Wenn die `update` Befehl gibt dem Fehler "Fehler beim Buildvorgang.":

* Führen Sie den Befehl erneut aus.
* Lassen Sie eine Nachricht am unteren Rand der Seite.

>[!div class="step-by-step"]
[Zurück](xref:data/ef-rp/sort-filter-page)
[Weiter](xref:data/ef-rp/complex-data-model)
