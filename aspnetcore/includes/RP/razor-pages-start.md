Die Standardvorlage erstellt die Links und Seiten **RazorPagesMovie**, **Home**, **Info** und **Kontakt**. Je nach Größe Ihres Browserfensters müssen Sie möglicherweise auf das Navigationssymbol klicken, um die Links anzuzeigen.

![Seite „Home“ oder „Index“](../../tutorials/razor-pages/razor-pages-start/_static/home2.png)

Testen Sie die Links. Die Links **RazorPagesMovie** und **Home** führen zur Seite „Index“. Die Links **Info** und **Kontakt** führen jeweils zu den Seiten `About` und `Contact`.

## <a name="project-files-and-folders"></a>Projektdateien und -ordner

Die folgende Tabelle listet die Dateien und Ordner im Projekt auf. In diesem Tutorial ist es am wichtigsten, die Datei *Startup.cs* zu verstehen. Sie müssen nicht jeden der unten angegebenen Links ansehen. Die Links werden als Referenz bereitgestellt, wenn Sie weitere Informationen zu einer Datei oder einem Ordner im Projekt benötigen.

| Datei oder Ordner              | Zweck |
| ----------------- | ------------ | 
| wwwroot | Enthält statische Dateien. Weitere Informationen finden Sie unter [Arbeiten mit statischen Dateien](xref:fundamentals/static-files). |
| Seiten | Ordner für [Razor-Pages](xref:mvc/razor-pages/index). | 
| *appsettings.json* | [Konfiguration](xref:fundamentals/configuration/index) |
| *Program.cs* | [Hostet](xref:fundamentals/hosting) die ASP.NET Core-App.|
| *Startup.cs* | Konfiguriert Dienste und die Anforderungspipeline. Siehe [Startup](xref:fundamentals/startup).|

### <a name="the-pages-folder"></a>Der Seitenordner

Die Datei *_Layout.cshtml* enthält häufige HTML-Elemente (Skripts und Stylesheets) und legt das Layout für die Anwendung fest. Wenn Sie beispielsweise auf **RazorPagesMovie**, **Home**, **About** (Info) oder **Contact** (Kontakt) klicken, werden dieselben Elemente angezeigt. Die häufigen Elemente umfassen das Navigationsmenü am oberen Rand und den Header am unteren Rand des Fensters. Weitere Informationen finden Sie unter [Layout](xref:mvc/views/layout).

Die Datei *_ViewStart.cshtml* legt die Eigenschaft `Layout` der Razor-Pages auf die Verwendung der Datei *_Layout.cshtml* fest. Weitere Informationen finden Sie unter [Layout](xref:mvc/views/layout).

Die Datei *_ViewImports.cshtml* enthält Razor-Anweisungen, die in jede Razor-Page importiert werden. Weitere Informationen finden Sie unter [Importing Shared Directives (Importieren gemeinsamer Anweisungen)](xref:mvc/views/layout#importing-shared-directives).

Die Datei *_ValidationScriptsPartial.cshtml* enthält einen Verweis auf [jQuery](https://jquery.com/)-Validierungsskripts. Die Datei *_ValidationScriptsPartial.cshtml* wird später im Tutorial verwendet, wenn die Seiten `Create` und `Edit` hinzugefügt werden.

Die Seiten `About`, `Contact` und `Index` sind Standardseiten, die zum Verwenden einer App verwendet werden können. Die Seite `Error` (Fehler) wird verwendet, um Fehlerinformationen anzuzeigen.