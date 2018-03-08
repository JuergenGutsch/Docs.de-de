---
title: Arbeiten mit SQL Server LocalDB und ASP.NET Core
author: rick-anderson
description: Informationen zum Arbeiten mit SQL Server LocalDB und ASP.NET Core.
keywords: ASP.NET Core, Razor-Seiten, Razor, MVC, SQL, LocalDB
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 1e35ce49980bf026de35359cdf413961051e3bee
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="working-with-sql-server-localdb-and-aspnet-core"></a>Arbeiten mit SQL Server LocalDB und ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette) 

Das `MovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank. Der Datenbankkontext wird mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der Methode `ConfigureServices` in der Datei *Startup.cs* registriert:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=7-8)]

Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`. Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:

[!code-json[Main](razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=2&range=8-10)]

Wenn Sie die App auf einem Test- oder Produktionsserver bereitstellen, können Sie eine Umgebungsvariable oder eine andere Vorgehensweise zum Festlegen der Verbindungszeichenfolge für einen tatsächlichen SQL Server-Computer verwenden. Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB ist eine Lightweightversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung geeignet ist. LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt. Die LocalDB-Datenbank erstellt standardmäßig \*MDF-Dateien im Verzeichnis *C:/Benutzer/\<Benutzer\>*.

<a name="ssox"></a>
* Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).

  ![Menü Ansicht](sql/_static/ssox.png)

* Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](sql/_static/dv.png)

Beachten Sie das Schlüsselsymbol neben `ID`. EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.

* Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

## <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Erstellen Sie im Ordner *Models* die neue Klasse `SeedData`. Ersetzen Sie den generierten Code durch den folgenden:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedData.cs?name=snippet_1)]

Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a>Hinzufügen des Initialisierers des Seedings

Fügen Sie den Initialisierers des Seedings in der Datei *Program.cs* am Ende der `Main`-Methode hinzu:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Program.cs)]

Testen der App

* Löschen Sie alle Datensätze in der Datenbank. Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.
* Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seedmethode ausgeführt wird. Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden. Hierzu können Sie einen der folgenden Ansätze verwenden:

  * Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

   * Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.
   * Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.
   
Die App zeigt die per Seeding hinzugefügten Daten:

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55.png)

Im nächsten Tutorial wird die Präsentation der Daten bereinigt.

>[!div class="step-by-step"]
[Zurück: Gerüstbau mit Razor-Pages](xref:tutorials/razor-pages/page)
[Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)
