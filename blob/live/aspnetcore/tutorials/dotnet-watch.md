---
title: Entwickeln von ASP.NET Core-Apps mit dotnet watch
author: rick-anderson
description: "Dieses Tutorial erläutert, wie Sie das Datei-Watcher-Tool (dotnet watch) der .NET Core-CLI in einer ASP.NET Core-Anwendung verwenden."
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: cadd4a6a78c29e2213c39a02729b5c32a2b93ebd
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a><span data-ttu-id="46ba0-103">Entwickeln von ASP.NET Core-Apps mit dotnet watch</span><span class="sxs-lookup"><span data-stu-id="46ba0-103">Developing ASP.NET Core apps using dotnet watch</span></span>

<span data-ttu-id="46ba0-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="46ba0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="46ba0-105">`dotnet watch` ist ein Tool, das einen [.NET Core-CLI](/dotnet/core/tools)-Befehl ausführt, wenn sich Quelldateien ändern.</span><span class="sxs-lookup"><span data-stu-id="46ba0-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="46ba0-106">Eine Dateiänderung kann beispielsweise eine Kompilierung, Testausführung oder Bereitstellung auslösen.</span><span class="sxs-lookup"><span data-stu-id="46ba0-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="46ba0-107">In diesem Tutorial verwenden wir eine vorhandene Web-API-App mit zwei Endpunkten: der eine gibt eine Summe, der andere ein Produkt zurück.</span><span class="sxs-lookup"><span data-stu-id="46ba0-107">In this tutorial, we use an existing Web API app with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="46ba0-108">Die Produktmethode enthält einen Fehler, den wir im Rahmen dieses Tutorials beheben müssen.</span><span class="sxs-lookup"><span data-stu-id="46ba0-108">The product method contains a bug that we'll fix as part of this tutorial.</span></span>

<span data-ttu-id="46ba0-109">Laden Sie die [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample) herunter.</span><span class="sxs-lookup"><span data-stu-id="46ba0-109">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="46ba0-110">Sie enthält zwei Projekte: *WebApp* (eine ASP.NET Core-Web-API) und *WebAppTests* (Komponententest für die Web-API).</span><span class="sxs-lookup"><span data-stu-id="46ba0-110">It contains two projects: *WebApp* (an ASP.NET Core Web API) and *WebAppTests* (unit tests for the Web API).</span></span>

<span data-ttu-id="46ba0-111">Navigieren Sie in einer Befehlsshell zum Ordner *WebApp*, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="46ba0-111">In a command shell, navigate to the *WebApp* folder and run the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="46ba0-112">Die Konsolenausgabe zeigt Meldungen ähnlich der folgenden (die angibt, dass die App ausgeführt wird und auf Anforderungen wartet):</span><span class="sxs-lookup"><span data-stu-id="46ba0-112">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="46ba0-113">Navigieren Sie in einem Webbrowser zu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="46ba0-113">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="46ba0-114">Es sollten die Ergebnisse von `9` angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="46ba0-114">You should see the result of `9`.</span></span>

<span data-ttu-id="46ba0-115">Navigieren Sie zur Produkt-API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="46ba0-115">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="46ba0-116">Es wird nicht wie erwartet `20` sondern `9` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="46ba0-116">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="46ba0-117">Dies korrigieren wir später im Tutorial.</span><span class="sxs-lookup"><span data-stu-id="46ba0-117">We'll fix that later in the tutorial.</span></span>

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="46ba0-118">Hinzufügen von `dotnet watch` zu einem Projekt</span><span class="sxs-lookup"><span data-stu-id="46ba0-118">Add `dotnet watch` to a project</span></span>

1. <span data-ttu-id="46ba0-119">Fügen Sie der *CSPROJ*-Datei einen `Microsoft.DotNet.Watcher.Tools`-Paketverweis hinzu:</span><span class="sxs-lookup"><span data-stu-id="46ba0-119">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup> 
    ```

1. <span data-ttu-id="46ba0-120">Installieren Sie das `Microsoft.DotNet.Watcher.Tools`-Paket, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="46ba0-120">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>
    
    ```console
    dotnet restore
    ```

## <a name="running-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="46ba0-121">Ausführen des .NET Core-CLI-Befehls mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="46ba0-121">Running .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="46ba0-122">Jeder [.NET Core-CLI-Befehl](/dotnet/core/tools#cli-commands) kann mit `dotnet watch` ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="46ba0-122">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="46ba0-123">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="46ba0-123">For example:</span></span>

| <span data-ttu-id="46ba0-124">Befehl</span><span class="sxs-lookup"><span data-stu-id="46ba0-124">Command</span></span> | <span data-ttu-id="46ba0-125">Befehl mit watch</span><span class="sxs-lookup"><span data-stu-id="46ba0-125">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="46ba0-126">dotnet run</span><span class="sxs-lookup"><span data-stu-id="46ba0-126">dotnet run</span></span> | <span data-ttu-id="46ba0-127">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="46ba0-127">dotnet watch run</span></span> |
| <span data-ttu-id="46ba0-128">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="46ba0-128">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="46ba0-129">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="46ba0-129">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="46ba0-130">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="46ba0-130">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="46ba0-131">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="46ba0-131">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="46ba0-132">dotnet test</span><span class="sxs-lookup"><span data-stu-id="46ba0-132">dotnet test</span></span> | <span data-ttu-id="46ba0-133">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="46ba0-133">dotnet watch test</span></span> |

<span data-ttu-id="46ba0-134">Führen Sie `dotnet watch run` im Ordner *WebApp* aus.</span><span class="sxs-lookup"><span data-stu-id="46ba0-134">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="46ba0-135">Die Konsolenausgabe gibt an, dass `watch` gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="46ba0-135">The console output indicates `watch` has started.</span></span>

## <a name="making-changes-with-dotnet-watch"></a><span data-ttu-id="46ba0-136">Vornehmen von Änderungen mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="46ba0-136">Making changes with `dotnet watch`</span></span>

<span data-ttu-id="46ba0-137">Stellen Sie sicher, dass `dotnet watch` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="46ba0-137">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="46ba0-138">Korrigieren Sie den Fehler in der `Product`-Methode von *MathController.cs* so, dass das Produkt und nicht die Summe zurückgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="46ba0-138">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

<span data-ttu-id="46ba0-139">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="46ba0-139">Save the file.</span></span> <span data-ttu-id="46ba0-140">Die Konsolenausgabe zeigt an, dass `dotnet watch` eine Dateiänderung erkannt und die App neu gestartet hat.</span><span class="sxs-lookup"><span data-stu-id="46ba0-140">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="46ba0-141">Vergewissern Sie sich, dass `http://localhost:<port number>/api/math/product?a=4&b=5` das richtige Ergebnis zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="46ba0-141">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="running-tests-using-dotnet-watch"></a><span data-ttu-id="46ba0-142">Ausführen von Tests mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="46ba0-142">Running tests using `dotnet watch`</span></span>

1. <span data-ttu-id="46ba0-143">Ändern Sie `Product`-Methode von *MathController.cs* so, dass wieder die Summe zurückgegeben wird, und speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="46ba0-143">Change the `Product` method of *MathController.cs* back to returning the sum and save the file.</span></span>
1. <span data-ttu-id="46ba0-144">Navigieren Sie in einer Befehlsshell zum Ordner *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="46ba0-144">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="46ba0-145">Führen Sie aus `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="46ba0-145">Run `dotnet restore`.</span></span>
1. <span data-ttu-id="46ba0-146">Führen Sie aus `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="46ba0-146">Run `dotnet watch test`.</span></span> <span data-ttu-id="46ba0-147">In der Ausgabe wird angezeigt, dass ein Test fehlgeschlagen ist und Watcher auf Dateiänderungen wartet:</span><span class="sxs-lookup"><span data-stu-id="46ba0-147">Its output indicates that a test failed and that watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="46ba0-148">Ändern Sie den Code der `Product`-Methode so, dass das Produkt zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="46ba0-148">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="46ba0-149">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="46ba0-149">Save the file.</span></span>

<span data-ttu-id="46ba0-150">`dotnet watch` erkennt die Dateiänderung und führt die Tests erneut aus.</span><span class="sxs-lookup"><span data-stu-id="46ba0-150">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="46ba0-151">Die Konsolenausgabe zeigt, dass die Tests bestanden wurden.</span><span class="sxs-lookup"><span data-stu-id="46ba0-151">The console output indicates the tests passed.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="46ba0-152">dotnet-watch in GitHub</span><span class="sxs-lookup"><span data-stu-id="46ba0-152">dotnet-watch in GitHub</span></span>

<span data-ttu-id="46ba0-153">dotnet-watch ist Teil des GitHub-Repositorys [DotNetTools](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="46ba0-153">dotnet-watch is part of the GitHub [DotNetTools repository](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch).</span></span>

<span data-ttu-id="46ba0-154">Der Abschnitt [MSBuild](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch#msbuild) in der [Infodatei zu dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) erläutert, wie dotnet-watch in der überwachten MSBuild-Projektdatei konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="46ba0-154">The [MSBuild section](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch#msbuild) of the [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) explains how dotnet-watch can be configured from the MSBuild project file being watched.</span></span> <span data-ttu-id="46ba0-155">Die [Infodatei zu dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) enthält Informationen zu dotnet-watch, die in diesem Tutorial nicht behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="46ba0-155">The [dotnet-watch ReadMe](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) contains information on dotnet-watch not covered in this tutorial.</span></span>