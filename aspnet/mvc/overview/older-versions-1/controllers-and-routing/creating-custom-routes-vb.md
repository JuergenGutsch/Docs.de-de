---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-vb
title: Erstellen von benutzerdefinierten Routen (VB) | Microsoft Docs
author: microsoft
description: "Weitere Informationen Sie zum Hinzufügen von benutzerdefinierter Routen zu einer ASP.NET MVC-Anwendung. In diesem Lernprogramm erfahren Sie, wie die Tabelle mit den standardmäßigen Route in der Datei \"Global.asax\" ändern."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2009
ms.topic: article
ms.assetid: 6ac5758b-6199-42af-adcb-21954b864951
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-vb
msc.type: authoredcontent
ms.openlocfilehash: 3d3161bd1bf74df425d3c53873875a1abcfbfa05
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2017
---
<a name="creating-custom-routes-vb"></a><span data-ttu-id="15061-104">Erstellen von benutzerdefinierten Routen (VB)</span><span class="sxs-lookup"><span data-stu-id="15061-104">Creating Custom Routes (VB)</span></span>
====================
<span data-ttu-id="15061-105">durch [Microsoft](https://github.com/microsoft)</span><span class="sxs-lookup"><span data-stu-id="15061-105">by [Microsoft](https://github.com/microsoft)</span></span>

> <span data-ttu-id="15061-106">Weitere Informationen Sie zum Hinzufügen von benutzerdefinierter Routen zu einer ASP.NET MVC-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="15061-106">Learn how to add custom routes to an ASP.NET MVC application.</span></span> <span data-ttu-id="15061-107">In diesem Lernprogramm erfahren Sie, wie die Tabelle mit den standardmäßigen Route in der Datei "Global.asax" ändern.</span><span class="sxs-lookup"><span data-stu-id="15061-107">In this tutorial, you learn how to modify the default route table in the Global.asax file.</span></span>


<span data-ttu-id="15061-108">In diesem Lernprogramm erfahren Sie, wie eine benutzerdefinierte Route zu einer ASP.NET MVC-Anwendung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="15061-108">In this tutorial, you learn how to add a custom route to an ASP.NET MVC application.</span></span> <span data-ttu-id="15061-109">Erfahren Sie, wie die Tabelle mit den standardmäßigen Route in der Datei "Global.asax" mit einer benutzerdefinierten Route zu ändern.</span><span class="sxs-lookup"><span data-stu-id="15061-109">You learn how to modify the default route table in the Global.asax file with a custom route.</span></span>

<span data-ttu-id="15061-110">In ASP.NET MVC-Anwendungen funktioniert die Standard-Routingtabelle gut.</span><span class="sxs-lookup"><span data-stu-id="15061-110">In ASP.NET MVC applications, the default route table will work just fine.</span></span> <span data-ttu-id="15061-111">Jedoch könnten Sie feststellen, dass Sie routing benötigt spezielle.</span><span class="sxs-lookup"><span data-stu-id="15061-111">However, you might discover that you have specialized routing needs.</span></span> <span data-ttu-id="15061-112">In diesem Fall können Sie eine benutzerdefinierte Route erstellen.</span><span class="sxs-lookup"><span data-stu-id="15061-112">In that case, you can create a custom route.</span></span>

<span data-ttu-id="15061-113">Stellen Sie sich vor, z. B., dass Sie eine Bloganwendung erstellen.</span><span class="sxs-lookup"><span data-stu-id="15061-113">Imagine, for example, that you are building a blog application.</span></span> <span data-ttu-id="15061-114">Möglicherweise möchten die eingehende Anforderungen zu verarbeiten, die wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="15061-114">You might want to handle incoming requests that look like this:</span></span>

<span data-ttu-id="15061-115">/ Archiv/12-25-2009</span><span class="sxs-lookup"><span data-stu-id="15061-115">/Archive/12-25-2009</span></span>

<span data-ttu-id="15061-116">Wenn ein Benutzer diese Anforderung eingibt, zurückgegeben werden soll den Blogeintrag, entspricht dem Datum 12/25/2009.</span><span class="sxs-lookup"><span data-stu-id="15061-116">When a user enters this request, you want to return the blog entry that corresponds to the date 12/25/2009.</span></span> <span data-ttu-id="15061-117">Um diese Art der Anforderung zu verarbeiten, müssen Sie eine benutzerdefinierte Route erstellen.</span><span class="sxs-lookup"><span data-stu-id="15061-117">In order to handle this type of request, you need to create a custom route.</span></span>

<span data-ttu-id="15061-118">Die Datei "Global.asax" im Codebeispiel 1 enthält eine neue benutzerdefinierte Route, die mit dem Namen Blog verwenden, welche Handles-Anforderungen, die wie /Archive/ aussehen*Eingabedatum*.</span><span class="sxs-lookup"><span data-stu-id="15061-118">The Global.asax file in Listing 1 contains a new custom route, named Blog, which handles requests that look like /Archive/*entry date*.</span></span>

<span data-ttu-id="15061-119">**Auflisten von 1 – "Global.asax" (mit benutzerdefinierten Route)**</span><span class="sxs-lookup"><span data-stu-id="15061-119">**Listing 1 - Global.asax (with custom route)**</span></span>

[!code-vb[Main](creating-custom-routes-vb/samples/sample1.vb)]

<span data-ttu-id="15061-120">Die Reihenfolge der Routen, die Sie, um die Routentabelle hinzufügen ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="15061-120">The order of the routes that you add to the route table is important.</span></span> <span data-ttu-id="15061-121">Bevor Sie die vorhandene Standardroute wird unsere neue benutzerdefinierte Blog-Route hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="15061-121">Our new custom Blog route is added before the existing Default route.</span></span> <span data-ttu-id="15061-122">Wenn Sie die Reihenfolge umgekehrt, und klicken Sie dann die Standardroute anstelle der benutzerdefinierten Route immer aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="15061-122">If you reversed the order, then the Default route always will get called instead of the custom route.</span></span>

<span data-ttu-id="15061-123">Die benutzerdefinierte Blog Route entspricht jede Anforderung, die mit/Archiv/beginnt.</span><span class="sxs-lookup"><span data-stu-id="15061-123">The custom Blog route matches any request that starts with /Archive/.</span></span> <span data-ttu-id="15061-124">Deshalb liegt eine Übereinstimmung mit allen folgenden URLs:</span><span class="sxs-lookup"><span data-stu-id="15061-124">So, it matches all of the following URLs:</span></span>

- <span data-ttu-id="15061-125">/ Archiv/12-25-2009</span><span class="sxs-lookup"><span data-stu-id="15061-125">/Archive/12-25-2009</span></span>

- <span data-ttu-id="15061-126">/ Archiv/10-6-2004</span><span class="sxs-lookup"><span data-stu-id="15061-126">/Archive/10-6-2004</span></span>

- <span data-ttu-id="15061-127">/ Archiv/apple</span><span class="sxs-lookup"><span data-stu-id="15061-127">/Archive/apple</span></span>

<span data-ttu-id="15061-128">Die benutzerdefinierte Route ordnet die eingehende Anforderung an einen Controller mit dem Namen Archiv und ruft die Entry()-Aktion.</span><span class="sxs-lookup"><span data-stu-id="15061-128">The custom route maps the incoming request to a controller named Archive and invokes the Entry() action.</span></span> <span data-ttu-id="15061-129">Wenn die Entry()-Methode aufgerufen wird, wird das Datum des Eintrags als einen Parameter namens EntryDate übergeben.</span><span class="sxs-lookup"><span data-stu-id="15061-129">When the Entry() method is called, the entry date is passed as a parameter named entryDate.</span></span>

<span data-ttu-id="15061-130">Sie können die benutzerdefinierte Blog-Route mit dem Codebeispiel 2-Controller verwenden.</span><span class="sxs-lookup"><span data-stu-id="15061-130">You can use the Blog custom route with the controller in Listing 2.</span></span>

<span data-ttu-id="15061-131">**Auflisten von 2 – ArchiveController.vb**</span><span class="sxs-lookup"><span data-stu-id="15061-131">**Listing 2 - ArchiveController.vb**</span></span>

[!code-vb[Main](creating-custom-routes-vb/samples/sample2.vb)]

<span data-ttu-id="15061-132">Beachten Sie, dass die Entry()-Methode im Codebeispiel 2 einen Parameter vom Typ "DateTime" akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="15061-132">Notice that the Entry() method in Listing 2 accepts a parameter of type DateTime.</span></span> <span data-ttu-id="15061-133">Das MVC-Framework ist intelligent genug, um das Eingabedatum aus der URL automatisch in einen DateTime-Wert konvertieren.</span><span class="sxs-lookup"><span data-stu-id="15061-133">The MVC framework is smart enough to convert the entry date from the URL into a DateTime value automatically.</span></span> <span data-ttu-id="15061-134">Wenn der Eintrag Date-Parameter von der URL in einen datetime-Wert konvertiert werden kann, wird ein Fehler ausgelöst (siehe Abbildung 1).</span><span class="sxs-lookup"><span data-stu-id="15061-134">If the entry date parameter from the URL cannot be converted to a DateTime, an error is raised (see Figure 1).</span></span>

<span data-ttu-id="15061-135">**Abbildung 1: Fehler aus der Konvertierung der parameter**</span><span class="sxs-lookup"><span data-stu-id="15061-135">**Figure 1 - Error from converting parameter**</span></span>


<span data-ttu-id="15061-136">[![Das Dialogfeld "Neues Projekt"](creating-custom-routes-vb/_static/image1.jpg)](creating-custom-routes-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="15061-136">[![The New Project dialog box](creating-custom-routes-vb/_static/image1.jpg)](creating-custom-routes-vb/_static/image1.png)</span></span>

<span data-ttu-id="15061-137">**Abbildung 01**: Fehler aus der Konvertierung der Parameter ([klicken Sie hier, um das Bild in voller Größe angezeigt](creating-custom-routes-vb/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="15061-137">**Figure 01**: Error from converting parameter ([Click to view full-size image](creating-custom-routes-vb/_static/image2.png))</span></span>


## <a name="summary"></a><span data-ttu-id="15061-138">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="15061-138">Summary</span></span>

<span data-ttu-id="15061-139">Das Ziel dieses Lernprogramms wurde veranschaulicht, wie Sie eine benutzerdefinierte Route erstellen können.</span><span class="sxs-lookup"><span data-stu-id="15061-139">The goal of this tutorial was to demonstrate how you can create a custom route.</span></span> <span data-ttu-id="15061-140">Sie haben gelernt, wie eine benutzerdefinierte Route die Routingtabelle in der Datei "Global.asax" hinzugefügt, die Blogeinträgen darstellt.</span><span class="sxs-lookup"><span data-stu-id="15061-140">You learned how to add a custom route to the route table in the Global.asax file that represents blog entries.</span></span> <span data-ttu-id="15061-141">Zuordnen von Anforderungen für Blogeinträgen zu einem Controller namens ArchiveController und eine Controlleraktion mit dem Namen Entry() besprochen.</span><span class="sxs-lookup"><span data-stu-id="15061-141">We discussed how to map requests for blog entries to a controller named ArchiveController and a controller action named Entry().</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="15061-142">[Zurück](asp-net-mvc-controller-overview-vb.md)
[Weiter](creating-a-route-constraint-vb.md)</span><span class="sxs-lookup"><span data-stu-id="15061-142">[Previous](asp-net-mvc-controller-overview-vb.md)
[Next](creating-a-route-constraint-vb.md)</span></span>