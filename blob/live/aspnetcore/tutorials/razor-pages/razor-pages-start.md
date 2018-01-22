---
title: Erste Schritte mit Razor-Seiten mit ASP.NET Core
author: rick-anderson
description: Erste Schritte mit Razor-Seiten mit ASP.NET Core
keywords: ASP.NET Core, Razor-Seiten, Razor, MVC
ms.author: riande
manager: wpickett
ms.date: 12/22/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4643665d48ca07ff43ce52064291fc106bd5c8ac
ms.sourcegitcommit: df2157ae9aeea0075772719c29784425c783e82a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="2b84b-104">Erste Schritte mit Razor-Seiten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b84b-104">Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="2b84b-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2b84b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2b84b-106">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor-Seiten.</span><span class="sxs-lookup"><span data-stu-id="2b84b-106">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="2b84b-107">Razor-Seiten sind der empfohlene Weg für die Erstellung von Benutzeroberflächen für Web-Apps in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2b84b-107">Razor Pages is the recommended way to build UI for web apps in ASP.NET Core.</span></span>

<span data-ttu-id="2b84b-108">Es gibt drei Versionen dieses Tutorials:</span><span class="sxs-lookup"><span data-stu-id="2b84b-108">There are three versions of this tutorial:</span></span>

* <span data-ttu-id="2b84b-109">Windows: Dieses Tutorial</span><span class="sxs-lookup"><span data-stu-id="2b84b-109">Windows: This tutorial</span></span>
* <span data-ttu-id="2b84b-110">macOS: [Erste Schritte mit Razor-Seiten mit Visual Studio für Mac](xref:tutorials/razor-pages-mac/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="2b84b-110">MacOS: [Getting started with Razor Pages with Visual Studio for Mac](xref:tutorials/razor-pages-mac/razor-pages-start)</span></span>
* <span data-ttu-id="2b84b-111">macOS, Linux und Windows: [Erste Schritte mit Razor-Seiten in ASP.NET Core mit Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="2b84b-111">macOS, Linux, and Windows: [Getting started with Razor Pages in ASP.NET Core with Visual Studio Code](xref:tutorials/razor-pages-vsc/razor-pages-start)</span></span>

<span data-ttu-id="2b84b-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2b84b-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2b84b-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="2b84b-113">Prerequisites</span></span>

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="2b84b-114">Erstellen einer Razor-Web-App</span><span class="sxs-lookup"><span data-stu-id="2b84b-114">Create a Razor web app</span></span>

* <span data-ttu-id="2b84b-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="2b84b-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2b84b-116">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="2b84b-116">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="2b84b-117">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="2b84b-117">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2b84b-118">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren/einfügen.</span><span class="sxs-lookup"><span data-stu-id="2b84b-118">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy/paste code.</span></span>
  <span data-ttu-id="2b84b-119">![neue ASP.NET Core-Webanwendung](../../mvc/razor-pages/index/_static/np.png)</span><span class="sxs-lookup"><span data-stu-id="2b84b-119">![new ASP.NET Core Web Application](../../mvc/razor-pages/index/_static/np.png)</span></span>
* <span data-ttu-id="2b84b-120">Wählen Sie in der Dropdownliste **ASP.NET Core 2.0** aus, und klicken Sie anschließend auf **Webanwendung**.</span><span class="sxs-lookup"><span data-stu-id="2b84b-120">Select **ASP.NET Core 2.0** in the dropdown, and then select **Web Application**.</span></span>

  [!INCLUDE[install 2.0](../../includes/dotnetcore-on-dotnetfx-vs.md)]

<span data-ttu-id="2b84b-121">Die Visual Studio-Vorlage erstellt ein Startprojekt:</span><span class="sxs-lookup"><span data-stu-id="2b84b-121">The Visual Studio template creates a starter project:</span></span>

![Projektmappen-Explorer](razor-pages-start/_static/se.png)

<span data-ttu-id="2b84b-123">Drücken Sie **F5**, um die App im Debugmodus auszuführen, oder **STRG+F5** zur Ausführung ohne Anfügen des Debuggers.</span><span class="sxs-lookup"><span data-stu-id="2b84b-123">Press **F5** to run the app in debug mode or **Ctrl-F5** to run without attaching the debugger</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home.png)

* <span data-ttu-id="2b84b-125">Visual Studio startet [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt Ihre App aus.</span><span class="sxs-lookup"><span data-stu-id="2b84b-125">Visual Studio starts [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="2b84b-126">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2b84b-126">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2b84b-127">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="2b84b-127">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2b84b-128">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="2b84b-128">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2b84b-129">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="2b84b-129">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2b84b-130">In der vorherigen Abbildung ist die Nummer des Ports 5000.</span><span class="sxs-lookup"><span data-stu-id="2b84b-130">In the preceding image, the port number is 5000.</span></span> <span data-ttu-id="2b84b-131">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2b84b-131">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2b84b-132">Das Starten der App mit **STRG+F5** (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="2b84b-132">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2b84b-133">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2b84b-133">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

>[!div class="step-by-step"]
[<span data-ttu-id="2b84b-134">Weiter: Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="2b84b-134">Next: Adding a model</span></span>](xref:tutorials/razor-pages/model)

>[!div class="step-by-step"]
[<span data-ttu-id="2b84b-135">Weiter: Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="2b84b-135">Next: Adding a model</span></span>](xref:tutorials/razor-pages/model)