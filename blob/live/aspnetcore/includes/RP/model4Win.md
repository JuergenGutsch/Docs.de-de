<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="810a7-101">Erstellen des Filmmodells</span><span class="sxs-lookup"><span data-stu-id="810a7-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="810a7-102">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="810a7-102">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="810a7-103">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="810a7-103">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

<span data-ttu-id="810a7-104">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="810a7-104">If you get the error:</span></span>
  ```
  The process cannot access the file 
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll' 
  because it is being used by another process.
  ```

<span data-ttu-id="810a7-105">Beenden Sie Visual Studio, und führen Sie den Befehl erneut aus.</span><span class="sxs-lookup"><span data-stu-id="810a7-105">Exit Visual Studio and run the command again.</span></span>