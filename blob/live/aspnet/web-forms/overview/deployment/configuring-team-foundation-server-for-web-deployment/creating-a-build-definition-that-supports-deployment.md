---
uid: web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment
title: "Erstellen einer Builddefinition, unterstützt die Bereitstellung | Microsoft Docs"
author: jrjlee
description: "Wenn Sie alle Arten von Builds in Team Foundation Server (TFS) 2010 ausführen möchten, müssen Sie eine Builddefinition innerhalb des Teamprojekts zu erstellen. Dieses Thema des..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: fe47a018-f6d0-4979-80e7-5b1fa75a5865
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-team-foundation-server-for-web-deployment/creating-a-build-definition-that-supports-deployment
msc.type: authoredcontent
ms.openlocfilehash: c2e7a768c2cf9900731b822ec187093a4b250ead
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-build-definition-that-supports-deployment"></a><span data-ttu-id="0deb2-104">Erstellen einer Builddefinition, unterstützt die Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="0deb2-104">Creating a Build Definition That Supports Deployment</span></span>
====================
<span data-ttu-id="0deb2-105">durch [Jason Lee](https://github.com/jrjlee)</span><span class="sxs-lookup"><span data-stu-id="0deb2-105">by [Jason Lee](https://github.com/jrjlee)</span></span>

[<span data-ttu-id="0deb2-106">PDF herunterladen</span><span class="sxs-lookup"><span data-stu-id="0deb2-106">Download PDF</span></span>](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> <span data-ttu-id="0deb2-107">Wenn Sie alle Arten von Builds in Team Foundation Server (TFS) 2010 ausführen möchten, müssen Sie eine Builddefinition innerhalb des Teamprojekts zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-107">If you want to perform any kind of build in Team Foundation Server (TFS) 2010, you need to create a build definition within your team project.</span></span> <span data-ttu-id="0deb2-108">Dieses Thema beschreibt, wie eine neue Builddefinition in TFS erstellt und zur Bereitstellung im Rahmen des Buildprozesses in Team Build steuern.</span><span class="sxs-lookup"><span data-stu-id="0deb2-108">This topic describes how to create a new build definition in TFS and how to control web deployment as part of the build process in Team Build.</span></span>


<span data-ttu-id="0deb2-109">Dieses Thema ist Teil einer Reihe von Lernprogrammen, die auf der Basis der Enterprise-bereitstellungsanforderungen eines fiktiven Unternehmens mit dem Namen Fabrikam, Inc. Diese Reihe von Lernprogrammen verwendet eine Beispielprojektmappe & #x 2014; die [Kontakt-Manager-Lösung](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)& #x 2014; zum Darstellen einer Webanwendung mit einer realistischen Maß an Komplexität, einschließlich einer ASP.NET MVC 3-Anwendung, eine Windows Communication Foundation (WCF)-Dienst, und ein Datenbankprojekt.</span><span class="sxs-lookup"><span data-stu-id="0deb2-109">This topic forms part of a series of tutorials based around the enterprise deployment requirements of a fictional company named Fabrikam, Inc. This tutorial series uses a sample solution&#x2014;the [Contact Manager solution](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)&#x2014;to represent a web application with a realistic level of complexity, including an ASP.NET MVC 3 application, a Windows Communication Foundation (WCF) service, and a database project.</span></span>

<span data-ttu-id="0deb2-110">Die Bereitstellungsmethode das Herzstück mit diesen Lernprogrammen basiert auf in beschriebene Ansatz der Teilung Projekt Datei [verstehen die Projektdatei](../web-deployment-in-the-enterprise/understanding-the-project-file.md)in die erstellungs-und Bereitstellung von zwei Projektdateien & #x 2014 kontrolliert wird; o Ne mit Buildanweisungen, die für jede zielumgebung und enthält umgebungsspezifische Einstellungen für Build- und Bereitstellungsprozess gelten.</span><span class="sxs-lookup"><span data-stu-id="0deb2-110">The deployment method at the heart of these tutorials is based on the split project file approach described in [Understanding the Project File](../web-deployment-in-the-enterprise/understanding-the-project-file.md), in which the build and deployment process is controlled by two project files&#x2014;one containing build instructions that apply to every destination environment, and one containing environment-specific build and deployment settings.</span></span> <span data-ttu-id="0deb2-111">Zur Buildzeit ist die Unabhängigkeit von der Umgebung-Projektdatei, einen vollständigen Satz von Buildanweisungen bilden die Projektdatei umgebungsspezifische zusammengeführt.</span><span class="sxs-lookup"><span data-stu-id="0deb2-111">At build time, the environment-specific project file is merged into the environment-agnostic project file to form a complete set of build instructions.</span></span>

## <a name="task-overview"></a><span data-ttu-id="0deb2-112">Übersicht über den Task</span><span class="sxs-lookup"><span data-stu-id="0deb2-112">Task Overview</span></span>

<span data-ttu-id="0deb2-113">Eine Builddefinition ist ein Mechanismus, der steuert, wie und wann Builds Teamprojekten in TFS auftreten.</span><span class="sxs-lookup"><span data-stu-id="0deb2-113">A build definition is the mechanism that controls how and when builds occur for team projects in TFS.</span></span> <span data-ttu-id="0deb2-114">Jede Builddefinition angibt:</span><span class="sxs-lookup"><span data-stu-id="0deb2-114">Each build definition specifies:</span></span>

- <span data-ttu-id="0deb2-115">Die Dinge, die Sie erstellen z. B. Visual Studio-Projektmappendateien oder benutzerdefinierte Microsoft Build Engine (MSBuild)-Projektdateien, möchten.</span><span class="sxs-lookup"><span data-stu-id="0deb2-115">The things you want to build, like Visual Studio solution files or custom Microsoft Build Engine (MSBuild) project files.</span></span>
- <span data-ttu-id="0deb2-116">Die Kriterien, die bestimmen, wann ein Build ausgeführt werden soll, platzieren, wie manuelle Trigger, fortlaufende Integration (CI), oder gated-Check-ins.</span><span class="sxs-lookup"><span data-stu-id="0deb2-116">The criteria that determine when a build should take place, like manual triggers, continuous integration (CI), or gated check-ins.</span></span>
- <span data-ttu-id="0deb2-117">Der Speicherort, an den Team Build Buildausgaben, einschließlich bereitstellungsartefakte wie Webpaketen und Datenbankskripts senden soll.</span><span class="sxs-lookup"><span data-stu-id="0deb2-117">The location to which Team Build should send build outputs, including deployment artifacts like web packages and database scripts.</span></span>
- <span data-ttu-id="0deb2-118">Die Zeitspanne, die jeden Build beibehalten werden sollen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-118">The amount of time that each build should be retained.</span></span>
- <span data-ttu-id="0deb2-119">Verschiedene andere Parameter des Buildprozesses.</span><span class="sxs-lookup"><span data-stu-id="0deb2-119">Various other parameters of the build process.</span></span>

> [!NOTE]
> <span data-ttu-id="0deb2-120">Weitere Informationen für alle Builddefinitionen finden Sie unter [Buildprozess definieren](https://msdn.microsoft.com/en-us/library/ms181715.aspx).</span><span class="sxs-lookup"><span data-stu-id="0deb2-120">For more information on build definitions, see [Define Your Build Process](https://msdn.microsoft.com/en-us/library/ms181715.aspx).</span></span>


<span data-ttu-id="0deb2-121">In diesem Thema wird gezeigt, wie zum Erstellen einer Builddefinition, die Konfigurationselemente, verwendet, damit ein Build ausgelöst wird, wenn ein Entwickler in neue Inhalte überprüft.</span><span class="sxs-lookup"><span data-stu-id="0deb2-121">This topic will show you how to create a build definition that uses CI, so that a build is triggered when a developer checks in new content.</span></span> <span data-ttu-id="0deb2-122">Wenn der Buildvorgang erfolgreich war, führt der Builddienst eine benutzerdefinierte Projektdatei, um die Lösung in einer testumgebung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-122">If the build succeeds, the build service runs a custom project file to deploy the solution to a test environment.</span></span>

<span data-ttu-id="0deb2-123">Wenn Sie einen Build auszulösen, müssen diese Aktionen ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="0deb2-123">When you trigger a build, these actions need to happen:</span></span>

- <span data-ttu-id="0deb2-124">Team Build sollte zuerst die Projektmappe erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="0deb2-124">First, Team Build should build the solution.</span></span> <span data-ttu-id="0deb2-125">Im Rahmen dieses Prozesses wird das Team Build Web veröffentlichen Pipeline (WPP) zum Generieren von Web-Bereitstellungspakete für jede der in der Projektmappe die Webanwendungsprojekte aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-125">As part of this process, Team Build will invoke the Web Publishing Pipeline (WPP) to generate web deployment packages for each of the web application projects in the solution.</span></span> <span data-ttu-id="0deb2-126">Teambuild führen auch Komponententests, die der Projektmappe zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="0deb2-126">Team Build will also run any unit tests associated with the solution.</span></span>
- <span data-ttu-id="0deb2-127">Wenn der Projektmappen-Buildvorgang scheitert, dauert der Teambuild keine weitere Aktion aus.</span><span class="sxs-lookup"><span data-stu-id="0deb2-127">If the solution build fails, Team Build should take no further action.</span></span> <span data-ttu-id="0deb2-128">Komponententest von Testfehlern sollte als Buildfehler behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="0deb2-128">Unit test failures should be treated as a build failure.</span></span>
- <span data-ttu-id="0deb2-129">Bei erfolgreicher Erstellung der Projektmappe sollte das benutzerdefinierte Projektdatei Team Build ausgeführt, die die Bereitstellung der Lösung steuert.</span><span class="sxs-lookup"><span data-stu-id="0deb2-129">If the solution build succeeds, Team Build should run the custom project file that controls the deployment of the solution.</span></span> <span data-ttu-id="0deb2-130">Im Rahmen dieses Prozesses Team Build wird aufgerufen, die Internetinformationsdienste (Internet Information Services, IIS)-Webbereitstellungstool (Web Deploy), um die gepackte Webanwendungen für die Ziel-Webserver zu installieren, und es wird aufgerufen, das VSDBCMD.exe-Hilfsprogramm, um das Erstellen einer Datenbank ausführen Skripts auf dem Zielserver für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0deb2-130">As part of this process, Team Build will invoke the Internet Information Services (IIS) Web Deployment Tool (Web Deploy) to install the packaged web applications on the destination web servers, and it will invoke the VSDBCMD.exe utility to run database creation scripts on the destination database servers.</span></span>

<span data-ttu-id="0deb2-131">Dies veranschaulicht den Prozess:</span><span class="sxs-lookup"><span data-stu-id="0deb2-131">This illustrates the process:</span></span>

![](creating-a-build-definition-that-supports-deployment/_static/image1.png)

<span data-ttu-id="0deb2-132">Die [Vorgesetzten Kontakts](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) errorhandling enthält eine benutzerdefinierte MSBuild-Projektdatei *Publish.proj*, die von MSBuild oder Team Build ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="0deb2-132">The [Contact Manager](../web-deployment-in-the-enterprise/the-contact-manager-solution.md) sample solution includes a custom MSBuild project file, *Publish.proj*, that you can run from MSBuild or Team Build.</span></span> <span data-ttu-id="0deb2-133">Wie in beschrieben [Verständnis des Build-Prozesses](../web-deployment-in-the-enterprise/understanding-the-build-process.md), diese Projektdatei definiert die Logik, mit denen die Webpakete und Datenbanken in einer zielumgebung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0deb2-133">As described in [Understanding the Build Process](../web-deployment-in-the-enterprise/understanding-the-build-process.md), this project file defines the logic that deploys your web packages and databases to a target environment.</span></span> <span data-ttu-id="0deb2-134">Die Datei enthält die Logik, die Gebäude und Verpackungsprozesses ausgelassen, wenn sie in Team Build, lassen nur die Bereitstellungsaufgaben ausführen ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="0deb2-134">The file includes logic that omits the building and packaging process if it's running in Team Build, leaving just the deployment tasks to run.</span></span> <span data-ttu-id="0deb2-135">Dies liegt daran, dass wenn Sie die Bereitstellung auf diese Weise automatisieren, Sie in der Regel möchten sicherstellen, dass das Projekt erfolgreich erstellt und Komponententests schon vorbei, bevor Sie der Bereitstellungsprozess beginnt.</span><span class="sxs-lookup"><span data-stu-id="0deb2-135">This is because when you automate deployment in this way, you'll typically want to ensure that the solution builds successfully and passes any unit tests before the deployment process commences.</span></span>

<span data-ttu-id="0deb2-136">Der nächste Abschnitt erklärt, wie Sie diesen Prozess zu implementieren, indem Sie eine neue Builddefinition erstellen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-136">The next section explains how to implement this process by creating a new build definition.</span></span>

> [!NOTE]
> <span data-ttu-id="0deb2-137">Diese Prozedur & #x 2014; in dem ein einzelner automatisierter Prozess erstellt wurde, überprüft, und stellt eine Lösung & #x 2014; ist es wahrscheinlich, dass sich am besten für die Bereitstellung So testen Sie Umgebungen werden.</span><span class="sxs-lookup"><span data-stu-id="0deb2-137">This procedure&#x2014;in which a single automated process builds, tests, and deploys a solution&#x2014;is likely to be most suited to deployment to test environments.</span></span> <span data-ttu-id="0deb2-138">Für die Staging-und produktionsumgebungen können Sie viel wahrscheinlicher zu Inhalten von einem vorherigen Build bereitgestellt, die Sie bereits überprüft haben, und in einer testumgebung überprüft werden soll.</span><span class="sxs-lookup"><span data-stu-id="0deb2-138">For staging and production environments you're a lot more likely to want to deploy content from a previous build that you've already verified and validated in a test environment.</span></span> <span data-ttu-id="0deb2-139">Dieser Ansatz wird im nächsten Thema beschrieben [Bereitstellen einer bestimmten Build](deploying-a-specific-build.md).</span><span class="sxs-lookup"><span data-stu-id="0deb2-139">This approach is described in the next topic, [Deploying a Specific Build](deploying-a-specific-build.md).</span></span>


### <a name="who-performs-this-procedure"></a><span data-ttu-id="0deb2-140">Wem diese Prozedur ausgeführt werden?</span><span class="sxs-lookup"><span data-stu-id="0deb2-140">Who Performs This Procedure?</span></span>

<span data-ttu-id="0deb2-141">In der Regel führt ein TFS-Administrator diese Prozedur.</span><span class="sxs-lookup"><span data-stu-id="0deb2-141">Typically, a TFS administrator performs this procedure.</span></span> <span data-ttu-id="0deb2-142">In einigen Fällen kann ein Entwickler Teamleiter Verantwortung für die Teamprojektsammlung in TFS dauern.</span><span class="sxs-lookup"><span data-stu-id="0deb2-142">In some cases, a developer team leader may take responsibility for the team project collection in TFS.</span></span> <span data-ttu-id="0deb2-143">Um eine neue Builddefinition erstellen, müssen Sie ein Mitglied der **Projektauflistungsadministratoren erstellen** Gruppe für die Teamprojektsammlung, die die Projektmappe enthält.</span><span class="sxs-lookup"><span data-stu-id="0deb2-143">In order to create a new build definition, you need to be a member of the **Project Collection Build Administrators** group for the team project collection that contains your solution.</span></span>

## <a name="create-a-build-definition-for-ci-and-deployment"></a><span data-ttu-id="0deb2-144">Erstellen Sie eine Builddefinition für CI und Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="0deb2-144">Create a Build Definition for CI and Deployment</span></span>

<span data-ttu-id="0deb2-145">Die nächste Prozedur beschreibt, wie eine Builddefinition erstellen, die CI auslöst.</span><span class="sxs-lookup"><span data-stu-id="0deb2-145">The next procedure describes how to create a build definition that CI triggers.</span></span> <span data-ttu-id="0deb2-146">Wenn der Buildvorgang erfolgreich war, wird die Lösung bereitgestellt mit Logik in eine benutzerdefinierte MSBuild-Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="0deb2-146">If the build succeeds, the solution is deployed using the logic in a custom MSBuild project file.</span></span>

<span data-ttu-id="0deb2-147">**So erstellen Sie eine Builddefinition für CI und Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="0deb2-147">**To create a build definition for CI and deployment**</span></span>

1. <span data-ttu-id="0deb2-148">In Visual Studio 2010 in der **Team Explorer** Fenster Erweitern des Teamprojektknotens der rechten Maustaste auf **Builds**, und klicken Sie dann auf **neue Builddefinition**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-148">In Visual Studio 2010, in the **Team Explorer** window, expand your team project node, right-click **Builds**, and then click **New Build Definition**.</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image2.png)
2. <span data-ttu-id="0deb2-149">Auf der **allgemeine** Registerkarte, benennen Sie der Builddefinition (z. B. **DeployToTest**) und eine optionale Beschreibung.</span><span class="sxs-lookup"><span data-stu-id="0deb2-149">On the **General** tab, give the build definition a name (for example, **DeployToTest**) and an optional description.</span></span>
3. <span data-ttu-id="0deb2-150">Auf der **Trigger** Registerkarte, wählen Sie die Kriterien, die auf dem Sie einen neuen Build auslösen möchten.</span><span class="sxs-lookup"><span data-stu-id="0deb2-150">On the **Trigger** tab, select the criteria on which you want to trigger a new build.</span></span> <span data-ttu-id="0deb2-151">Wählen Sie z. B. wenn die Projektmappe erstellt und in der testumgebung bereitstellen, jedes Mal, wenn ein Entwickler im neuen Code überprüft werden sollen, **Continuous Integration**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-151">For example, if you want to build the solution and deploy to the test environment every time a developer checks in new code, select **Continuous Integration**.</span></span>
4. <span data-ttu-id="0deb2-152">Auf der **Build-Standardwerte** Registerkarte die **Kopie Buildausgabe in den folgenden Ablageordner** Feld, geben Sie den Pfad (UNC = Universal Naming Convention) des Ordners "Drop" (z. B.  **\\TFSBUILD\Drops**).</span><span class="sxs-lookup"><span data-stu-id="0deb2-152">On the **Build Defaults** tab, in the **Copy build output to the following drop folder** box, type the Universal Naming Convention (UNC) path of your drop folder (for example, **\\TFSBUILD\Drops**).</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image3.png)

    > [!NOTE]
    > <span data-ttu-id="0deb2-153">Diese Dateiablage-Speicherort speichert mehrere Builds, abhängig von der Aufbewahrungsrichtlinie, die Sie konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="0deb2-153">This drop location stores several builds, depending on the retention policy you configure.</span></span> <span data-ttu-id="0deb2-154">Bei Bereitstellungselemente aus einem bestimmten Build in einer Staging- oder Produktion veröffentlichen soll, ist dies die, in dem sie suchen müssen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-154">When you want to publish deployment artifacts from a specific build to a staging or production environment, this is where you'll find them.</span></span>
5. <span data-ttu-id="0deb2-155">Auf der **Prozess** Registerkarte die **Buildprozessdatei** verlassen der Dropdown-Liste **DefaultTemplate.xaml** ausgewählten.</span><span class="sxs-lookup"><span data-stu-id="0deb2-155">On the **Process** tab, in the **Build process file** dropdown list, leave **DefaultTemplate.xaml** selected.</span></span> <span data-ttu-id="0deb2-156">Dies ist eine der Build Standardprozessvorlagen, die auf alle neuen Teamprojekten hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0deb2-156">This is one of the default build process templates that get added to all new team projects.</span></span>
6. <span data-ttu-id="0deb2-157">In der **Buildprozessparameter** Tabelle, klicken Sie in der **zu erstellende Dokumente** Zeile, und klicken Sie dann auf die **Auslassungszeichen** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="0deb2-157">In the **Build process parameters** table, click in the **Items to Build** row, and then click the **ellipsis** button.</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image4.png)
7. <span data-ttu-id="0deb2-158">In der **zu erstellende Dokumente** (Dialogfeld), klicken Sie auf **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-158">In the **Items to Build** dialog box, click **Add**.</span></span>
8. <span data-ttu-id="0deb2-159">Navigieren Sie zum Speicherort der Projektmappendatei, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-159">Browse to the location of your solution file, and then click **OK**.</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image5.png)
9. <span data-ttu-id="0deb2-160">In der **zu erstellende Dokumente** (Dialogfeld), klicken Sie auf **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-160">In the **Items to Build** dialog box, click **Add**.</span></span>
10. <span data-ttu-id="0deb2-161">In der **Elemente des Typs** Dropdownliste **MSBuild-Projektdateien**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-161">In the **Items of type** dropdown list, select **MSBuild Project files**.</span></span>
11. <span data-ttu-id="0deb2-162">Navigieren Sie zum Speicherort der benutzerdefinierten Projektdatei, mit denen Sie steuern des Bereitstellungsprozesses, wählen Sie die Datei, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-162">Browse to the location of the custom project file with which you control the deployment process, select the file, and then click **OK**.</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image6.png)
12. <span data-ttu-id="0deb2-163">Die **zu erstellende Dokumente** Dialogfeld sollte jetzt zwei Elemente anzeigen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-163">The **Items to Build** dialog box should now show two items.</span></span> <span data-ttu-id="0deb2-164">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-164">Click **OK**.</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image7.png)
13. <span data-ttu-id="0deb2-165">Auf der **Prozess** Registerkarte die **Buildprozessparameter** table, erweitern Sie die **erweitert** Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="0deb2-165">On the **Process** tab, in the **Build process parameters** table, expand the **Advanced** section.</span></span>
14. <span data-ttu-id="0deb2-166">In der **MSBuild-Argumente** Zeile, fügen Sie MSBuild Befehlszeilenargumente, die *entweder* erfordert der Items zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-166">In the **MSBuild Arguments** row, add any MSBuild command-line arguments that *either* of your items to build requires.</span></span> <span data-ttu-id="0deb2-167">Im Szenario Projektmappe Contact Manager sind diese Argumente erforderlich:</span><span class="sxs-lookup"><span data-stu-id="0deb2-167">In the Contact Manager solution scenario, these arguments are required:</span></span>

    [!code-console[Main](creating-a-build-definition-that-supports-deployment/samples/sample1.cmd)]

    ![](creating-a-build-definition-that-supports-deployment/_static/image8.png)
15. <span data-ttu-id="0deb2-168">In diesem Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0deb2-168">In this example:</span></span>

    1. <span data-ttu-id="0deb2-169">Die **DeployOnBuild = "true"** und **DeployTarget = Paket** Argumente sind erforderlich, wenn Sie die Projektmappe Contact Manager erstellen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-169">The **DeployOnBuild=true** and **DeployTarget=package** arguments are required when you build the Contact Manager solution.</span></span> <span data-ttu-id="0deb2-170">Dies weist MSBuild Web Bereitstellungspakete erstellen nach der Erstellung jedes Webanwendungsprojekt, wie in beschrieben [erstellen und Packen Webanwendungsprojekte](../web-deployment-in-the-enterprise/building-and-packaging-web-application-projects.md).</span><span class="sxs-lookup"><span data-stu-id="0deb2-170">This instructs MSBuild to create web deployment packages after building each web application project, as described in [Building and Packaging Web Application Projects](../web-deployment-in-the-enterprise/building-and-packaging-web-application-projects.md).</span></span>
    2. <span data-ttu-id="0deb2-171">Die **TargetEnvPropsFile** Argument ist erforderlich, bei der Erstellung der *Publish.proj* Datei.</span><span class="sxs-lookup"><span data-stu-id="0deb2-171">The **TargetEnvPropsFile** argument is required when you build the *Publish.proj* file.</span></span> <span data-ttu-id="0deb2-172">Diese Eigenschaft gibt den Speicherort der Konfigurationsdatei umgebungsspezifische an, wie in beschrieben [Verständnis des Build-Prozesses](../web-deployment-in-the-enterprise/understanding-the-build-process.md).</span><span class="sxs-lookup"><span data-stu-id="0deb2-172">This property indicates the location of the environment-specific configuration file, as described in [Understanding the Build Process](../web-deployment-in-the-enterprise/understanding-the-build-process.md).</span></span>
16. <span data-ttu-id="0deb2-173">Auf der **Aufbewahrungsrichtlinie** Registerkarte, konfigurieren, wie viele jedes Typs erstellt, nach Bedarf beibehalten möchten.</span><span class="sxs-lookup"><span data-stu-id="0deb2-173">On the **Retention Policy** tab, configure how many builds of each type you want to retain as required.</span></span>
17. <span data-ttu-id="0deb2-174">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-174">Click **Save**.</span></span>

## <a name="queue-a-build"></a><span data-ttu-id="0deb2-175">Hinzufügen eines Builds zur Warteschlange</span><span class="sxs-lookup"><span data-stu-id="0deb2-175">Queue a Build</span></span>

<span data-ttu-id="0deb2-176">An diesem Punkt haben Sie mindestens eine neue Builddefinition erstellt.</span><span class="sxs-lookup"><span data-stu-id="0deb2-176">At this point, you have created at least one new build definition.</span></span> <span data-ttu-id="0deb2-177">Der Build-Prozesses, die Sie definiert wird nun gemäß der Trigger ausgeführt, die Sie in der Builddefinition angegeben.</span><span class="sxs-lookup"><span data-stu-id="0deb2-177">The build process you defined will now run according to the triggers you specified in the build definition.</span></span>

<span data-ttu-id="0deb2-178">Wenn Sie die Builddefinition CI Verwendung konfiguriert haben, können Sie die Builddefinition auf zwei Arten testen:</span><span class="sxs-lookup"><span data-stu-id="0deb2-178">If you've configured your build definition to use CI, you can test your build definition in two ways:</span></span>

- <span data-ttu-id="0deb2-179">Checken Sie einige Inhalte mit dem Teamprojekt, einen automatische Build auszulösen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-179">Check in some content to the team project to trigger an automatic build.</span></span>
- <span data-ttu-id="0deb2-180">Die Warteschlange eines Builds manuell.</span><span class="sxs-lookup"><span data-stu-id="0deb2-180">Queue a build manually.</span></span>

<span data-ttu-id="0deb2-181">**Um manuell einen Build in die Warteschlange**</span><span class="sxs-lookup"><span data-stu-id="0deb2-181">**To queue a build manually**</span></span>

1. <span data-ttu-id="0deb2-182">In der **Team Explorer** rechten Maustaste auf die Builddefinition, und klicken Sie dann auf **neuen Build in Warteschlange**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-182">In the **Team Explorer** window, right-click the build definition, and then click **Queue New Build**.</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image9.png)
2. <span data-ttu-id="0deb2-183">In der **Build zur Warteschlange hinzufügen** (Dialogfeld), überprüfen Sie die Buildeigenschaften, und klicken Sie dann auf **Warteschlange**.</span><span class="sxs-lookup"><span data-stu-id="0deb2-183">In the **Queue Build** dialog box, review the build properties, and then click **Queue**.</span></span>

    ![](creating-a-build-definition-that-supports-deployment/_static/image10.png)

<span data-ttu-id="0deb2-184">So überprüfen den Status und das Ergebnis eines Builds & #x 2014; unabhängig davon, ob er ausgelöst wurde manuell oder automatisch & #x 2014; Doppelklicken Sie auf die Builddefinition ist jetzt in der **Team Explorer** Fenster.</span><span class="sxs-lookup"><span data-stu-id="0deb2-184">To review the progress and the outcome of a build&#x2014;regardless of whether it was triggered manually or automatically&#x2014;double-click the build definition in the **Team Explorer** window.</span></span> <span data-ttu-id="0deb2-185">Dies öffnet einen **Build Explorer** Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="0deb2-185">This will open a **Build Explorer** tab.</span></span>

![](creating-a-build-definition-that-supports-deployment/_static/image11.png)

<span data-ttu-id="0deb2-186">Von hier aus können Sie fehlerhafte Builds Probleme beheben.</span><span class="sxs-lookup"><span data-stu-id="0deb2-186">From here, you can troubleshoot failed builds.</span></span> <span data-ttu-id="0deb2-187">Wenn Sie einen einzelnen Build doppelklicken, können Sie Zusammenfassungsinformationen anzeigen und klicken Sie auf die ausführliche Protokolldateien.</span><span class="sxs-lookup"><span data-stu-id="0deb2-187">If you double-click an individual build, you can view summary information and click through to detailed log files.</span></span>

![](creating-a-build-definition-that-supports-deployment/_static/image12.png)

<span data-ttu-id="0deb2-188">Diese Informationen können Sie die Problembehandlung bei fehlerhaften Builds und Probleme zu beheben, bevor Sie, einen anderen Build versuchen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-188">You can use this information to troubleshoot failed builds and address any problems before you attempt another build.</span></span>

> [!NOTE]
> <span data-ttu-id="0deb2-189">Builds, die Bereitstellung Logik ausgeführt werden wahrscheinlich fehl, bis Sie die Build-Server alle in der zielumgebung erforderlichen Berechtigungen erteilt haben.</span><span class="sxs-lookup"><span data-stu-id="0deb2-189">Builds that execute deployment logic are likely to fail until you have granted the build server any permissions required in the destination environment.</span></span> <span data-ttu-id="0deb2-190">Weitere Informationen finden Sie unter [Konfigurieren von Berechtigungen für die Team Build-Bereitstellung](configuring-permissions-for-team-build-deployment.md).</span><span class="sxs-lookup"><span data-stu-id="0deb2-190">For more information, see [Configuring Permissions for Team Build Deployment](configuring-permissions-for-team-build-deployment.md).</span></span>


## <a name="monitor-the-build-process"></a><span data-ttu-id="0deb2-191">Überwachen des Buildprozesses</span><span class="sxs-lookup"><span data-stu-id="0deb2-191">Monitor the Build Process</span></span>

<span data-ttu-id="0deb2-192">TFS bietet eine Breite Palette von Funktionen, um während des Erstellungsprozesses überwachen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-192">TFS provides a broad range of functionality to help you monitor the build process.</span></span> <span data-ttu-id="0deb2-193">TFS können z. B. per e-Mail oder Warnungen in Ihrer Infobereich der Taskleiste angezeigt wird, wenn ein Build abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="0deb2-193">For example, TFS can send you an email or display alerts in your taskbar notification area when a build has completed.</span></span> <span data-ttu-id="0deb2-194">Weitere Informationen finden Sie unter [ausführen und Überwachen von Builds](https://msdn.microsoft.com/en-us/library/ms181721.aspx).</span><span class="sxs-lookup"><span data-stu-id="0deb2-194">For more information, see [Run and Monitor Builds](https://msdn.microsoft.com/en-us/library/ms181721.aspx).</span></span>

## <a name="conclusion"></a><span data-ttu-id="0deb2-195">Schlussfolgerung</span><span class="sxs-lookup"><span data-stu-id="0deb2-195">Conclusion</span></span>

<span data-ttu-id="0deb2-196">In diesem Thema beschriebene Vorgehensweise: erstellen eine Builddefinition in TFS.</span><span class="sxs-lookup"><span data-stu-id="0deb2-196">This topic described how to create a build definition in TFS.</span></span> <span data-ttu-id="0deb2-197">Die Builddefinition wurde für CI, konfiguriert, damit während des Erstellungsprozesses ausgeführt werden kann, wenn ein Entwickler Inhalt mit dem Teamprojekt eincheckt.</span><span class="sxs-lookup"><span data-stu-id="0deb2-197">The build definition is configured for CI, so the build process runs whenever a developer checks in content to the team project.</span></span> <span data-ttu-id="0deb2-198">Die Builddefinition führt eine benutzerdefinierte MSBuild-Projektdatei, um Web Deploy Packages and Datenbankskripts in einer zielumgebung für den Server bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="0deb2-198">The build definition executes a custom MSBuild project file to deploy web packages and database scripts to a target server environment.</span></span>

<span data-ttu-id="0deb2-199">In der Reihenfolge für eine automatische Bereitstellung im Rahmen des Buildprozesses erfolgreich ist müssen Sie die entsprechenden Berechtigungen für das Build-Dienstkonto auf dem Ziel-Webserver und den Zielserver für die Datenbank zu gewähren.</span><span class="sxs-lookup"><span data-stu-id="0deb2-199">In order for an automated deployment to succeed as part of a build process, you'll need to grant appropriate permissions to the build service account on the target web servers and the target database server.</span></span> <span data-ttu-id="0deb2-200">Das letzte Thema in diesem Lernprogramm [Konfigurieren von Berechtigungen für die Team Build Bereitstellung](configuring-permissions-for-team-build-deployment.md), beschreibt, wie Sie zu identifizieren und konfigurieren Sie die Berechtigungen für die automatisierte Bereitstellung von einem Team Build-Server erforderlich.</span><span class="sxs-lookup"><span data-stu-id="0deb2-200">The final topic in this tutorial, [Configuring Permissions for Team Build Deployment](configuring-permissions-for-team-build-deployment.md), describes how to identify and configure the permissions required for automated deployment from a Team Build server.</span></span>

## <a name="further-reading"></a><span data-ttu-id="0deb2-201">Weiterführende Themen</span><span class="sxs-lookup"><span data-stu-id="0deb2-201">Further Reading</span></span>

<span data-ttu-id="0deb2-202">Weitere Informationen zum Erstellen von Builddefinitionen finden Sie unter [Erstellen einer grundlegenden Builddefinition](https://msdn.microsoft.com/en-us/library/ms181716.aspx) und [Buildprozess definieren](https://msdn.microsoft.com/en-us/library/ms181715.aspx).</span><span class="sxs-lookup"><span data-stu-id="0deb2-202">For more information on creating build definitions, see [Create a Basic Build Definition](https://msdn.microsoft.com/en-us/library/ms181716.aspx) and [Define Your Build Process](https://msdn.microsoft.com/en-us/library/ms181715.aspx).</span></span> <span data-ttu-id="0deb2-203">Weitere Anleitungen auf queuing Builds finden Sie unter [einen Build zur Warteschlange](https://msdn.microsoft.com/en-us/library/ms181722.aspx).</span><span class="sxs-lookup"><span data-stu-id="0deb2-203">For more guidance on queuing builds, see [Queue a Build](https://msdn.microsoft.com/en-us/library/ms181722.aspx).</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="0deb2-204">[Zurück](configuring-a-tfs-build-server-for-web-deployment.md)
[Weiter](deploying-a-specific-build.md)</span><span class="sxs-lookup"><span data-stu-id="0deb2-204">[Previous](configuring-a-tfs-build-server-for-web-deployment.md)
[Next](deploying-a-specific-build.md)</span></span>