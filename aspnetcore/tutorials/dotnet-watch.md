---
title: Entwickeln von ASP.NET Core-Apps mit dotnet watch
author: rick-anderson
description: "Dieses Tutorial erläutert, wie Sie das Datei-Watcher-Tool (dotnet watch) der .NET Core-CLI in einer ASP.NET Core-Anwendung verwenden."
keywords: ASP.NET Core, Verwenden von dotnet watch
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: tutorials/dotnet-watch
ms.openlocfilehash: 9baf2ce2a1270a728616a8a2ab45deca9a9cde6f
ms.sourcegitcommit: e7f01a649f240b6b57118c53314ab82f7f36f2eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a>Entwickeln von ASP.NET Core-Apps mit dotnet watch

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` ist ein Tool, das einen [.NET Core-CLI](/dotnet/core/tools)-Befehl ausführt, wenn sich Quelldateien ändern. Eine Dateiänderung kann beispielsweise eine Kompilierung, Testausführung oder Bereitstellung auslösen.

In diesem Tutorial verwenden wir eine vorhandene Web-API-App mit zwei Endpunkten: der eine gibt eine Summe, der andere ein Produkt zurück. Die Produktmethode enthält einen Fehler, den wir im Rahmen dieses Tutorials beheben müssen.

Laden Sie die [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample) herunter. Sie enthält zwei Projekte: *WebApp* (eine ASP.NET Core-Web-API) und *WebAppTests* (Komponententest für die Web-API).

Navigieren Sie in einer Befehlsshell zum Ordner *WebApp*, und führen Sie den folgenden Befehl aus:

```console
dotnet run
```

Die Konsolenausgabe zeigt Meldungen ähnlich der folgenden (die angibt, dass die App ausgeführt wird und auf Anforderungen wartet):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Navigieren Sie in einem Webbrowser zu `http://localhost:<port number>/api/math/sum?a=4&b=5`. Es sollten die Ergebnisse von `9` angezeigt werden.

Navigieren Sie zur Produkt-API (`http://localhost:<port number>/api/math/product?a=4&b=5`). Es wird nicht wie erwartet `20` sondern `9` zurückgegeben. Dies korrigieren wir später im Tutorial.

## <a name="add-dotnet-watch-to-a-project"></a>Hinzufügen von `dotnet watch` zu einem Projekt

1. Fügen Sie der *CSPROJ*-Datei einen `Microsoft.DotNet.Watcher.Tools`-Paketverweis hinzu:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup> 
    ```

1. Installieren Sie das `Microsoft.DotNet.Watcher.Tools`-Paket, indem Sie den folgenden Befehl ausführen:
    
    ```console
    dotnet restore
    ```

## <a name="running-net-core-cli-commands-using-dotnet-watch"></a>Ausführen des .NET Core-CLI-Befehls mit `dotnet watch`

Jeder [.NET Core-CLI-Befehl](/dotnet/core/tools#cli-commands) kann mit `dotnet watch` ausgeführt werden. Zum Beispiel:

| Befehl | Befehl mit watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

Führen Sie `dotnet watch run` im Ordner *WebApp* aus. Die Konsolenausgabe gibt an, dass `watch` gestartet wurde.

## <a name="making-changes-with-dotnet-watch"></a>Vornehmen von Änderungen mit `dotnet watch`

Stellen Sie sicher, dass `dotnet watch` ausgeführt wird.

Korrigieren Sie den Fehler in der `Product`-Methode von *MathController.cs* so, dass das Produkt und nicht die Summe zurückgegeben wird:

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

Speichern Sie die Datei. Die Konsolenausgabe zeigt an, dass `dotnet watch` eine Dateiänderung erkannt und die App neu gestartet hat.

Vergewissern Sie sich, dass `http://localhost:<port number>/api/math/product?a=4&b=5` das richtige Ergebnis zurückgibt.

## <a name="running-tests-using-dotnet-watch"></a>Ausführen von Tests mit `dotnet watch`

1. Ändern Sie `Product`-Methode von *MathController.cs* so, dass wieder die Summe zurückgegeben wird, und speichern Sie die Datei.
1. Navigieren Sie in einer Befehlsshell zum Ordner *WebAppTests*.
1. Führen Sie `dotnet restore` aus.
1. Führen Sie `dotnet watch test` aus. In der Ausgabe wird angezeigt, dass ein Test fehlgeschlagen ist und Watcher auf Dateiänderungen wartet:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Ändern Sie den Code der `Product`-Methode so, dass das Produkt zurückgegeben wird. Speichern Sie die Datei.

`dotnet watch` erkennt die Dateiänderung und führt die Tests erneut aus. Die Konsolenausgabe zeigt, dass die Tests bestanden wurden.

## <a name="dotnet-watch-in-github"></a>dotnet-watch in GitHub

dotnet-watch ist Teil des GitHub-Repositorys [DotNetTools](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch).

Der Abschnitt [MSBuild](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch#msbuild) in der [Infodatei zu dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) erläutert, wie dotnet-watch in der überwachten MSBuild-Projektdatei konfiguriert werden kann. Die [Infodatei zu dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) enthält Informationen zu dotnet-watch, die in diesem Tutorial nicht behandelt werden.
