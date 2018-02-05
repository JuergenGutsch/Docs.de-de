---
title: "Hinzufügen eines Modells zu einer App mit Razor-Pages in ASP.NET Core"
author: rick-anderson
description: "Hinzufügen eines Modells zu einer App mit Razor-Pages in ASP.NET Core"
manager: wpickett
ms.author: riande
ms.date: 07/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0ce7693bfdc37d930488304b329dbcd533a5ec1d
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="adding-a-model-to-a-razor-pages-app"></a>Hinzufügen eines Modells zu einer App mit Razor-Pages

[!INCLUDE[model1](../../includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Hinzufügen eines Datenmodells

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **RazorPagesMovie** > **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*. Wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse **Movie**, und fügen Sie Ihr die folgenden Eigenschaften hinzu:

[!INCLUDE[model 2](../../includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu.

[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

Registrieren Sie den Datenbankkontext mit dem [Abhängigkeitsinjektionscontainer](xref:fundamentals/dependency-injection) in der Datei *Startup.cs*.

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-5,7-9)]

Erstellen Sie das Projekt, um sicherzustellen, dass keine Fehler vorliegen.

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a>Hinzufügen von Tools für den Gerüstbau und Ausführen der anfänglichen Migration

In diesem Abschnitt führen Sie folgende Aktionen mit der Paket-Manager-Konsole (Package Manager Console, PMC) aus:

* Fügen Sie das Visual Studio-Paket für die Webcodegenerierung hinzu. Dieses Paket ist für die Ausführung des Gerüstbaumoduls erforderlich.
* Fügen Sie eine anfängliche Migration hinzu.
* Aktualisieren Sie die Datenbank mit der anfänglichen Migration.

Öffnen Sie das Menü **Extras**, und wählen Sie **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Add-Migration Initial
Update-Database
```

Mit dem Befehl `Install-Package` werden die für das Gerüstbaumodul benötigten Tools installiert.

Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen. Das Schema basiert auf dem Modell, das in der Datei *Modelle/MovieContext.cs* für `DbContext` angegeben ist. Das Argument `Initial` wird verwendet, um die Migrationen zu benennen. Sie können einen beliebigen Namen auswählen, sollten aber der Konvention zufolge einen Namen verwenden, der die Migration beschreibt. Weitere Informationen finden Sie unter [Introduction to migrations (Einführung in Migrationen)](xref:data/ef-mvc/migrations#introduction-to-migrations).

Mit dem Befehl `Update-Database` führen Sie in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* die Methode `Up` aus, mit der die Datenbank erstellt wird.

[!INCLUDE[model 4windows](../../includes/RP/model4Win.md)]

[!INCLUDE[model 4](../../includes/RP/model4tbl.md)]

<a name="test"></a>
### <a name="test-the-app"></a>Testen der App

* Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).
* Testen Sie den Link **Create** (Erstellen).

 ![Seite „Create“](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).

Wenn eine SQL-Ausnahme ausgelöst wird, stellen Sie sicher, dass Migrationen ausgeführt wurden und die Datenbank aktualisiert wurde:

Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.

>[!div class="step-by-step"]
[Vorheriges Tutorial: Getting Started (Erste Schritte)](xref:tutorials/razor-pages/razor-pages-start)
[Nächstes Tutorial: Scaffolded Razor Pages (Razor-Pages mit erfolgtem Gerüstbau)](xref:tutorials/razor-pages/page)    
