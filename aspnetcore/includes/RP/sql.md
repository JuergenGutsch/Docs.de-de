# <a name="working-with-sqlite-in-and-razor-pages"></a>Arbeiten mit SQLite und Razor-Seiten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Das `MovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank. Der Datenbankkontext wird mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der Methode `ConfigureServices` in der Datei *Startup.cs* registriert:

[!code-csharp[Main](code/Startup.cs?name=snippet2&highlight=6-8)]

## <a name="sqlite"></a>SQLite

Auf der [SQLite](https://www.sqlite.org/)-Website ist zu lesen:

> SQLite ist ein eigenständiges, sehr zuverlässiges, eingebettetes, genehmigungsfreies SQL-Datenbankmodul mit vollen Funktionsumfang. SQLite ist das weltweit am häufigsten verwendete Datenbankmodul.

Es gibt viele Tools von Drittanbietern, die Sie herunterladen können, um eine SQLite-Datenbank zu verwalten und anzuzeigen. Die folgende Abbildung stammt aus [DB Browser for SQLite](http://sqlitebrowser.org/). Wenn Sie ein bestimmtes SQLite-Tool bevorzugen, geben Sie bitte in einem Kommentar dessen Vorteile an.

![DB Browser for SQLite mit der Filmdatenbank](../../tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Erstellen Sie im Ordner *Models* die neue Klasse `SeedData`. Ersetzen Sie den generierten Code durch den folgenden:

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a>Hinzufügen des Initialisierers des Seedings

Fügen Sie den Initialisierer des Seedings in der Datei *Program.cs* zur `Main`-Methode hinzu:

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]

### <a name="test-the-app"></a>Testen der App

Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird). Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.
   
Die App zeigt die per Seeding hinzugefügten Daten.