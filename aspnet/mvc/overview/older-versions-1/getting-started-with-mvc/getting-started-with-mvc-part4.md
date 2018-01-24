---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part4
title: Erstellen einer Datenbank | Microsoft Docs
author: shanselman
description: "Dies ist ein Anfänger-Lernprogramm, das die Grundlagen von ASP.NET MVC. Erstellen Sie eine einfache Web-Anwendung, die Lese- und Schreibvorgänge aus einer Datenbank."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: 742df67f-484d-4ef3-af6b-8c791e556b43
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part4
msc.type: authoredcontent
ms.openlocfilehash: 6a9617b8056f883d6be2547588b13063a58abd0e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-database"></a><span data-ttu-id="c7a48-104">Erstellen einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="c7a48-104">Creating a Database</span></span>
====================
<span data-ttu-id="c7a48-105">durch [Scott Hanselman](https://github.com/shanselman)</span><span class="sxs-lookup"><span data-stu-id="c7a48-105">by [Scott Hanselman](https://github.com/shanselman)</span></span>

> <span data-ttu-id="c7a48-106">Dies ist ein Anfänger-Lernprogramm, das die Grundlagen von ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c7a48-106">This is a beginner tutorial that introduces the basics of ASP.NET MVC.</span></span> <span data-ttu-id="c7a48-107">Erstellen Sie eine einfache Web-Anwendung, die Lese- und Schreibvorgänge aus einer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c7a48-107">You'll create a simple web application that reads and writes from a database.</span></span> <span data-ttu-id="c7a48-108">Besuchen Sie die [ASP.NET MVC-Trainingscenter](../../../index.md) andere ASP.NET-MVC Lernprogramme und Beispiele finden.</span><span class="sxs-lookup"><span data-stu-id="c7a48-108">Visit the [ASP.NET MVC learning center](../../../index.md) to find other ASP.NET MVC tutorials and samples.</span></span>


<span data-ttu-id="c7a48-109">In diesem Abschnitt werden wir eine neue SQL Express-Datenbank zu erstellen, die zum Speichern und Abrufen von unseren Filmdaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c7a48-109">In this section we are going to create a new SQL Express database that we'll use to store and retrieve our movie data.</span></span> <span data-ttu-id="c7a48-110">Wählen Sie aus der Visual Web Developer-IDE auf die Registerkarte Ansicht | Server-Explorer.</span><span class="sxs-lookup"><span data-stu-id="c7a48-110">From within the Visual Web Developer IDE, select View | Server Explorer.</span></span> <span data-ttu-id="c7a48-111">Klicken Sie mit der rechten Maustaste auf Datenverbindungen, und klicken Sie auf die Verbindung hinzufügen...</span><span class="sxs-lookup"><span data-stu-id="c7a48-111">Right click on Data Connections and click Add Connection...</span></span>

![AddConnection](getting-started-with-mvc-part4/_static/image1.png)

<span data-ttu-id="c7a48-113">Klicken Sie im Dialogfeld "Datenquelle auswählen" Wählen Sie Microsoft SQL Server, und wählen Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="c7a48-113">In the Choose Data Source dialog, select Microsoft SQL Server and select Continue.</span></span>

![](getting-started-with-mvc-part4/_static/image2.png)

<span data-ttu-id="c7a48-114">Klicken Sie im Dialogfeld "Verbindung hinzufügen" Geben Sie ". \SQLEXPRESS" für den Servernamen, und geben Sie "Filme" als Namen für die neue Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c7a48-114">In the Add Connection dialog, enter ".\SQLEXPRESS" for your Server Name, and enter "Movies" as the name for your new database.</span></span>

<span data-ttu-id="c7a48-115">[![Dialogfeld "Verbindung" hinzufügen](getting-started-with-mvc-part4/_static/image4.png)](getting-started-with-mvc-part4/_static/image3.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-115">[![Add Connection dialog](getting-started-with-mvc-part4/_static/image4.png)](getting-started-with-mvc-part4/_static/image3.png)</span></span>

<span data-ttu-id="c7a48-116">Klicken Sie auf OK, und Sie werden gefragt, ob die Datenbank erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="c7a48-116">Click OK and you'll be asked if you want to create that database.</span></span> <span data-ttu-id="c7a48-117">Wählen Sie Ja.</span><span class="sxs-lookup"><span data-stu-id="c7a48-117">Select yes.</span></span>

<span data-ttu-id="c7a48-118">[![Erstellen Filme?](getting-started-with-mvc-part4/_static/image6.png)](getting-started-with-mvc-part4/_static/image5.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-118">[![Create Movies?](getting-started-with-mvc-part4/_static/image6.png)](getting-started-with-mvc-part4/_static/image5.png)</span></span>

<span data-ttu-id="c7a48-119">Jetzt haben Sie eine leere Datenbank im Server-Explorer.</span><span class="sxs-lookup"><span data-stu-id="c7a48-119">Now you've got an empty database in Server Explorer.</span></span>

![Fügen Sie neue Tabelle hinzu](getting-started-with-mvc-part4/_static/image7.png)

<span data-ttu-id="c7a48-121">Klicken Sie mit der rechten Maustaste auf Tabellen, und klicken Sie auf die Tabelle hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="c7a48-121">Right click on Tables and click Add Table.</span></span> <span data-ttu-id="c7a48-122">Tabellen-Designer wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c7a48-122">The Table Designer will appear.</span></span> <span data-ttu-id="c7a48-123">Hinzufügen von Spalten für Id, Titel, ReleaseDate, "Genre" und Preis.</span><span class="sxs-lookup"><span data-stu-id="c7a48-123">Add columns for Id, Title, ReleaseDate, Genre, and Price.</span></span> <span data-ttu-id="c7a48-124">Klicken Sie mit der rechten Maustaste auf die ID-Spalte, und klicken Sie auf den Primärschlüssel festlegen.</span><span class="sxs-lookup"><span data-stu-id="c7a48-124">Right click on the ID column and click set Primary Key.</span></span> <span data-ttu-id="c7a48-125">Hier ist welche Meine Entwurf Bereiche sieht wie ein.</span><span class="sxs-lookup"><span data-stu-id="c7a48-125">Here's what my design areas looks like.</span></span>

<span data-ttu-id="c7a48-126">[![Datenbank-Tabellen-Editor](getting-started-with-mvc-part4/_static/image9.png)](getting-started-with-mvc-part4/_static/image8.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-126">[![Database Table Editor](getting-started-with-mvc-part4/_static/image9.png)](getting-started-with-mvc-part4/_static/image8.png)</span></span>

<span data-ttu-id="c7a48-127">Wählen Sie die Id-Spalte, und ändern Sie unter folgenden Spalteneigenschaften "Identitätsspezifikation" auf "Yes".</span><span class="sxs-lookup"><span data-stu-id="c7a48-127">Also, select the Id column and under Column Properties below change "Identity Specification" to "Yes."</span></span>

<span data-ttu-id="c7a48-128">[![IsIdentity - Spalteneigenschaften](getting-started-with-mvc-part4/_static/image11.png)](getting-started-with-mvc-part4/_static/image10.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-128">[![IsIdentity - Column Properties](getting-started-with-mvc-part4/_static/image11.png)](getting-started-with-mvc-part4/_static/image10.png)</span></span>

<span data-ttu-id="c7a48-129">Wenn Sie vorgenommen haben, klicken Sie auf das Symbol "Speichern" in der Symbolleiste, oder wählen Sie Datei | Speichern Sie aus dem Menü aus, und nennen Sie die Tabelle "**Film**" (im singular).</span><span class="sxs-lookup"><span data-stu-id="c7a48-129">When you've got it done, click the Save icon in the toolbar or select File | Save from the menu, and name your table "**Movie**" (singular).</span></span> <span data-ttu-id="c7a48-130">Wir haben eine Datenbank und eine Tabelle!</span><span class="sxs-lookup"><span data-stu-id="c7a48-130">We've got a database and a table!</span></span>

<span data-ttu-id="c7a48-131">[![Namen auswählen](getting-started-with-mvc-part4/_static/image13.png)](getting-started-with-mvc-part4/_static/image12.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-131">[![Choose Name](getting-started-with-mvc-part4/_static/image13.png)](getting-started-with-mvc-part4/_static/image12.png)</span></span>

<span data-ttu-id="c7a48-132">Wechseln Sie zurück zum Server-Explorer mit der rechten Maustaste, klicken Sie auf die Film-Tabelle, und wählen Sie "Tabellendaten anzeigen".</span><span class="sxs-lookup"><span data-stu-id="c7a48-132">Go back to Server Explorer and right click the Movie table, then select "Show Table Data."</span></span> <span data-ttu-id="c7a48-133">Geben Sie ein paar Filme aus, damit unsere Datenbank einige Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="c7a48-133">Enter a few movies so our database has some data.</span></span>

<span data-ttu-id="c7a48-134">[![Bearbeiten von Datenbank-Tabelle](getting-started-with-mvc-part4/_static/image15.png)](getting-started-with-mvc-part4/_static/image14.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-134">[![Database Table Editing](getting-started-with-mvc-part4/_static/image15.png)](getting-started-with-mvc-part4/_static/image14.png)</span></span>

## <a name="creating-a-model"></a><span data-ttu-id="c7a48-135">Erstellen eines Modells</span><span class="sxs-lookup"><span data-stu-id="c7a48-135">Creating a Model</span></span>

<span data-ttu-id="c7a48-136">Jetzt, wechseln Sie zurück zum Projektmappen-Explorer auf der rechten Seite der IDE und mit der rechten Maustaste auf den Ordner Models und wählen Sie hinzufügen | Neues Element.</span><span class="sxs-lookup"><span data-stu-id="c7a48-136">Now, switch back to the Solution Explorer on the right hand side of the IDE and right-click on the Models folder and select Add | New Item.</span></span>

<span data-ttu-id="c7a48-137">[![addnewmodelitem](getting-started-with-mvc-part4/_static/image17.png)](getting-started-with-mvc-part4/_static/image16.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-137">[![addnewmodelitem](getting-started-with-mvc-part4/_static/image17.png)](getting-started-with-mvc-part4/_static/image16.png)</span></span>

<span data-ttu-id="c7a48-138">Wir werden ein Entitätenmodell aus unserem neuen Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c7a48-138">We're going to create an Entity Model from our new database.</span></span> <span data-ttu-id="c7a48-139">Dadurch wird eine Reihe von Klassen zum Projekt hinzugefügt, die erleichtert es uns abgefragt und bearbeitet die Daten innerhalb der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c7a48-139">This will add a set of classes to our project that makes it easy for us to query and manipulate the data within our database.</span></span> <span data-ttu-id="c7a48-140">Wählen Sie den Knoten Daten auf der linken Seite des Dialogfelds, und wählen Sie dann das ADO.NET Entity Data Model-Elementvorlage.</span><span class="sxs-lookup"><span data-stu-id="c7a48-140">Select the Data node on the left-hand side of the dialog, and then select the ADO.NET Entity Data Model item template.</span></span> <span data-ttu-id="c7a48-141">Nennen sie Movies.edmx.</span><span class="sxs-lookup"><span data-stu-id="c7a48-141">Name it Movies.edmx.</span></span>

<span data-ttu-id="c7a48-142">[![AddNewDataModel](getting-started-with-mvc-part4/_static/image19.png)](getting-started-with-mvc-part4/_static/image18.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-142">[![AddNewDataModel](getting-started-with-mvc-part4/_static/image19.png)](getting-started-with-mvc-part4/_static/image18.png)</span></span>

<span data-ttu-id="c7a48-143">Klicken Sie auf die Schaltfläche "Hinzufügen".</span><span class="sxs-lookup"><span data-stu-id="c7a48-143">Click the "Add" button.</span></span> <span data-ttu-id="c7a48-144">Klicken Sie dann wird "Entity Data Model-Assistenten" gestartet.</span><span class="sxs-lookup"><span data-stu-id="c7a48-144">This will then launch the "Entity Data Model Wizard".</span></span>

<span data-ttu-id="c7a48-145">Wählen Sie in der angezeigten Dialogfeld neue generieren aus Datenbank ein.</span><span class="sxs-lookup"><span data-stu-id="c7a48-145">In the new dialog that pops up, select Generate from Database.</span></span> <span data-ttu-id="c7a48-146">Da wir nur eine Datenbank vorgenommen haben, müssen wir nur die Entity Framework über die neue Datenbank und die Tabelle mitteilen.</span><span class="sxs-lookup"><span data-stu-id="c7a48-146">Since we've just made a database, we'll only need to tell the Entity Framework about our new database and its table.</span></span> <span data-ttu-id="c7a48-147">Klicken Sie auf neben Speichern unserer datenbankverbindung in unserem Webanwendung-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c7a48-147">Click Next to save our database connection in our web application's configuration.</span></span> <span data-ttu-id="c7a48-148">Prüfen Sie jetzt die Tabellen und Film Kontrollkästchen und klicken Sie auf Fertig stellen.</span><span class="sxs-lookup"><span data-stu-id="c7a48-148">Now, check the Tables and Movie checkbox and click Finish.</span></span>

<span data-ttu-id="c7a48-149">[![Assistent für Entity Data Model](getting-started-with-mvc-part4/_static/image21.png)](getting-started-with-mvc-part4/_static/image20.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-149">[![Entity Data Model Wizard](getting-started-with-mvc-part4/_static/image21.png)](getting-started-with-mvc-part4/_static/image20.png)</span></span>

<span data-ttu-id="c7a48-150">Jetzt können wir finden Sie in unserer neuen Film-Tabelle im Entity Framework-Designer und Code zugreifen.</span><span class="sxs-lookup"><span data-stu-id="c7a48-150">Now we can see our new Movie table in the Entity Framework Designer and access it from code.</span></span>

<span data-ttu-id="c7a48-151">[![Filme – Microsoft Visual Web Developer 2010 Express](getting-started-with-mvc-part4/_static/image23.png)](getting-started-with-mvc-part4/_static/image22.png)</span><span class="sxs-lookup"><span data-stu-id="c7a48-151">[![Movies - Microsoft Visual Web Developer 2010 Express](getting-started-with-mvc-part4/_static/image23.png)](getting-started-with-mvc-part4/_static/image22.png)</span></span>

<span data-ttu-id="c7a48-152">Auf der Entwurfsoberfläche sehen Sie eine "Movie"-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c7a48-152">On the design surface you can see a "Movie" class.</span></span> <span data-ttu-id="c7a48-153">Diese Klasse ist der "Movie" Tabelle in der Datenbank zugeordnet, und jede Eigenschaft darin ist eine Spalte mit der Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="c7a48-153">This class maps to the "Movie" table in our database, and each property within it maps to a column with the table.</span></span> <span data-ttu-id="c7a48-154">Jede Instanz einer Klasse "Movie" wird eine Zeile in der Tabelle "Movie" entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c7a48-154">Each instance of a "Movie" class will correspond to a row within the "Movie" table.</span></span>

<span data-ttu-id="c7a48-155">Wenn Sie die Standardnamen und Zuordnen von Entity Framework verwendete Konventionen nicht gefällt, können Sie die Entity Framework-Designer ändern, oder passen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="c7a48-155">If you don't like the default naming and mapping conventions used by the Entity Framework, you can use the Entity Framework designer to change or customize them.</span></span> <span data-ttu-id="c7a48-156">Für diese Anwendung wir die Standardeinstellungen verwenden und speichern Sie die Datei als nur-ist.</span><span class="sxs-lookup"><span data-stu-id="c7a48-156">For this application we'll use the defaults and just save the file as-is.</span></span>

<span data-ttu-id="c7a48-157">Jetzt sehen wir mit ein paar echten Daten funktioniert!</span><span class="sxs-lookup"><span data-stu-id="c7a48-157">Now, let's work with some real data!</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="c7a48-158">[Zurück](getting-started-with-mvc-part3.md)
[Weiter](getting-started-with-mvc-part5.md)</span><span class="sxs-lookup"><span data-stu-id="c7a48-158">[Previous](getting-started-with-mvc-part3.md)
[Next](getting-started-with-mvc-part5.md)</span></span>