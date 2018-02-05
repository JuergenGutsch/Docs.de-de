---
uid: aspnet/web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
title: Programmierung ASP.NET Web Pages (Razor) mit Visual Studio | Microsoft Docs
author: tfitzmac
description: "In diesem Anhang wird erläutert, wie Sie Visual Studio 2010 noch Visual Web Developer 2010 Express Programm ASP.NET Web Pages mit Razor-Syntax verwenden können."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/13/2014
ms.topic: article
ms.assetid: 0acfec5a-48f2-4766-a801-a0f426966f0a
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
msc.type: authoredcontent
ms.openlocfilehash: 5cfeda206eda8fb3fd769d34fb40bae2c3b65093
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2017
---
<a name="programming-aspnet-web-pages-razor-using-visual-studio"></a>Programmieren von ASP.NET Web Pages (Razor) mithilfe von Visual Studio
====================
durch [Tom FitzMacken](https://github.com/tfitzmac)

> In diesem Artikel wird erläutert, wie Sie Visual Studio oder Visual Web Developer Express Programm Websites mit ASP.NET Web Pages (Razor) verwenden können.
> 
> Lernen Sie
> 
> - Was müssen Sie (falls zutreffend) zum Arbeiten mit ASP.NET Web Pages in Ihrer Version von Visual Studio installieren.
> - Wie Sie die Unterstützung für ASP.NET Web Pages Visual Web Developer 2010 Express hinzufügen.
> - Wie Sie Funktionen zum Arbeiten mit ASP.NET Razor-Pages, einschließlich IntelliSense und der Debugger in Visual Studio verwenden.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>In diesem Lernprogramm verwendeten Versionen der Software
> 
> 
> - ASP.NET Web Pages (Razor) 3
> - Visual Studio 2013
> - WebMatrix-3
>   
> 
> Dieses Lernprogramm funktioniert auch mit ASP.NET Web Pages 2, Visual Studio 2012, Visual Studio 2010 und WebMatrix 2.


Sie können ASP.NET Web Pages mit Razor-Syntax, die mithilfe von WebMatrix oder viele andere Code-Editoren programmieren. Sie können auch Microsoft Visual Studio, eine voll ausgestattete integrierte Entwicklungsumgebung (IDE), die einen Satz leistungsstarken Tools bietet für viele Arten von Anwendungen (nicht nur Websites) erstellen. Um mit ASP.NET Razor-Pages arbeiten, können Sie entweder eine der vollständigen Editionen von Visual Studio oder das kostenlose [Visual Studio Express für Web](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-2013-express) Edition.

Zwei besonders nützliche, Funktionen, die Visual Studio für die Programmierung mit Webseiten ASP.NET Razor bereitstellt sind:

- *IntelliSense*. Die IntelliSense-Funktion in Visual Studio integriert ist umfassender als IntelliSense in WebMatrix.
- *Debugger*. Mit dem Debugger können Sie Ihren Code zu lösen, indem Sie ein Programm beenden, während er ausgeführt wird, untersuchen von Variablen und den Code zeilenweise schrittweise durchlaufen.

## <a name="using-visual-studio-with-different-versions-of-aspnet-web-pages"></a>Mithilfe von Visual Studio mit verschiedenen Versionen von ASP.NET Web Pages

Visual Studio 2012 und Visual Studio 2013 enthalten die Unterstützung für ASP.NET Web Pages. (Die Pakete, die erforderlich sind, um die Unterstützung von ASP.NET Web Pages sind installiert, bei der Installation von Visual Studio.)

Visual Studio 2010 umfasst Unterstützung standardmäßig für ASP.NET Web Pages nicht. Um ASP.NET Web Pages mit Visual Studio 2010 verwenden zu können, müssen Sie das ASP.NET MVC-Paket installieren. Um ASP.NET Web Pages 2 zu erhalten, installieren Sie ASP.NET MVC 4.

In der folgenden Tabelle werden die Unterstützung für ASP.NET Web Pages in verschiedenen Versionen von Visual Studio zusammengefasst.

|  | Visual Studio 2010 | Visual Studio 2012 | Visual Studio 2013 |
| --- | --- | --- | --- |
| **ASP.NET Web Pages 2** | Installieren Sie ASP.NET MVC 4 | (Enthalten) | (Enthalten) |
| **ASP.NET Web Pages 3** |  | Update für ASP.NET Web Pages 3 über NuGet | (Enthalten) |

Um mit Visual Studio 2010 arbeiten, finden Sie unter [Installieren von Unterstützung für ASP.NET Web Pages in Visual Studio 2010](#vs2010support).

## <a name="launching-visual-studio-from-webmatrix"></a>Starten Visual Studio aus WebMatrix

Wenn Sie ein Projekt, klicken Sie in WebMatrix gestartet haben und Visual Studio wechseln möchten, bietet WebMatrix eine Schaltfläche, um das Projekt in Visual Studio problemlos zu öffnen. Sie benötigen Visual Studio installiert, die auf Ihren Computer auf diese Schaltfläche aktiviert werden. Die folgende Abbildung zeigt die Schaltfläche in WebMatrix.

![Starten Sie Visual Studio](program-asp-net-web-pages-in-visual-studio/_static/image1.png)

Wenn Sie die Schaltfläche klicken, wird das Projekt in Visual Studio geöffnet. Sie können zwischen WebMatrix und Visual Studio hin-und ohne Probleme wechseln. Sie werden benachrichtigt, wenn alle Dateien in der anderen Umgebung geändert haben und erneut geladen werden, damit die neuesten Änderungen abrufen müssen.

## <a name="creating-aspnet-razor-site-in-visual-studio"></a>Erstellen von ASP.NET Razor-Website in Visual Studio

So erstellen eine ASP.NET Razor-Website in Visual Studio:

1. Starten Sie Visual Studio oder Visual Web Developer.
2. In der **Datei** Menü klicken Sie auf **neue Website**.

    ![neue Website erstellen](program-asp-net-web-pages-in-visual-studio/_static/image2.png)
3. In der **neue Website** Dialogfeld wählen die Sprache an (Visual c# oder Visual Basic) verwenden.
4. Wählen Sie die **ASP.NET-Website (Razor)** Vorlage.

    ![Razor-Website](program-asp-net-web-pages-in-visual-studio/_static/image3.png)
5. Klicken Sie auf **OK**.

Das neue Projekt vorhanden ist und mit einigen Webseiten Standard, um Ihnen beim Einstieg helfen gefüllt.

### <a name="using-intellisense"></a>Using IntelliSense

Nun, dass Sie eine Website erstellt haben, können Sie die Funktionsweise von IntelliSense in Visual Studio finden Sie unter.

1. Öffnen Sie auf der Website, die Sie gerade erstellt haben, die *Default.cshtml* Seite.
2. Nach der `<h3>` Tags auf der Seite eingeben `@ServerInfo.` (einschließlich des durch Punktes). Beachten Sie, wie IntelliSense zeigt an, die verfügbaren Methoden für die `ServerInfo` Helper in einer Dropdownliste. 

    ![IntelliSense](program-asp-net-web-pages-in-visual-studio/_static/image4.png)
3. Wählen Sie die `GetHtml` Methode aus der Liste aus und drücken Sie dann die EINGABETASTE. IntelliSense ist in der Methode füllt. (Wie jeder Methode in C# geschrieben, das Sie hinzufügen müssen `()` Zeichen nach der Methode.)  
 Der vollständige Code für die `GetHtml` -Methode sieht wie im folgenden Beispiel:  

    [!code-cshtml[Main](program-asp-net-web-pages-in-visual-studio/samples/sample1.cshtml)]
4. Drücken Sie STRG + F5, um die Seite auszuführen. Sieht die Seite wie wenn in einem Browser angezeigt: 

    ![Standardseite im browser](program-asp-net-web-pages-in-visual-studio/_static/image5.png)
5. Schließen Sie den Browser.

### <a name="using-the-debugger"></a>Verwenden des Debuggers

1. Am oberen Rand der *Default.cshtml* Seite nach der Zeile, die mit beginnt `Page.Title`, fügen Sie die folgende Codezeile hinzu: 

    [!code-csharp[Main](program-asp-net-web-pages-in-visual-studio/samples/sample2.cs)]
2. Klicken Sie in den grauen Rand des Editors auf der linken Seite des Codes auf neben dieser neuen Zeile zum Hinzufügen einer *Haltepunkt*. Ein Haltepunkt ist ein Marker, der weist den Debugger zu beenden, das Programm zu diesem Zeitpunkt ausgeführt werden, damit Sie sehen, was geschieht.

    ![Haltepunkt festlegen](program-asp-net-web-pages-in-visual-studio/_static/image6.png)
3. Entfernen Sie den Aufruf an die `ServerInfo.GetHtml` -Methode, und fügen Sie einen Aufruf an die `@myTime` an seiner Stelle Variable. Dieser Aufruf zeigt die aktuelle Zeitwert, der von der neuen Zeile des Codes zurückgegeben wird.
4. Drücken Sie F5, um die Seite im Debugger auszuführen. Die Seite hält am Haltepunkt, den Sie festlegen. Die folgende Abbildung zeigt, wie die Seite im Editor mit dem Haltepunkt (in Gelb) aussieht. 

    ![Haltepunkt Debuggen](program-asp-net-web-pages-in-visual-studio/_static/image7.png)
5. Klicken Sie in der Debug-Symbolleiste auf die **Einzelschritt** Schaltfläche (oder drücken Sie F11) auf die nächste Zeile des Codes ausgeführt. Bei jedem Klicken auf diese Schaltfläche, wechseln Sie mit die Ausführung in die nächste Zeile des Codes.

    ![Schaltfläche Einzelschritt](program-asp-net-web-pages-in-visual-studio/_static/image8.png)
6. Überprüfen Sie den Wert, der die `myTime` Variable, indem Sie den Mauszeiger darüber halten oder Überprüfen der Werte angezeigt, der **"lokal"** und **Aufrufliste** Windows. Den Wert der Variablen in Visual Studio angezeigt.

    ![Zeit-Wert](program-asp-net-web-pages-in-visual-studio/_static/image9.png)
7. Wenn Sie fertig sind, untersuchen von Variablen und Code schrittweise durchlaufen, drücken Sie F5, um die Seite ausgeführt, ohne bei jeder Zeile fortzusetzen. Wenn Sie alle den Code schrittweise durchlaufen haben, zeigt der Browser die Seite.

Weitere Informationen über den Debugger und Informationen zum Debuggen von Code in Visual Studio finden Sie unter [Exemplarische Vorgehensweise: Debuggen von Webseiten in Visual Web Developer](https://msdn.microsoft.com/library/z9e7w6cs.aspx).

## <a name="using-razor-in-aspnet-mvc-projects-with-visual-studio"></a>Verwenden von Razor in ASP.NET MVC-Projekte mit Visual Studio

Die Razor-Syntax ist auch sehr häufig in ASP.NET MVC-Projekte verwendet. MVC ist eine leistungsfähige, auf Mustern basierende Möglichkeit zum Erstellen dynamischer Websites. Wenn Ihre Website für ASP.NET Web Pages schwierig zu verwalten ist, empfiehlt es sich, sollten ihn in einer ASP.NET MVC-Anwendung zu konvertieren. Ein Beispiel zum Erstellen einer MVC-Anwendung finden Sie unter [erste Schritte mit ASP.NET MVC 5](../../../mvc/overview/getting-started/introduction/getting-started.md).

<a id="vs2010support"></a>
## <a name="installing-support-for-aspnet-web-pages-in-visual-studio-2010"></a>Installieren von Unterstützung für ASP.NET Web Pages in Visual Studio 2010

In diesem Abschnitt wird gezeigt, wie Visual Web Developer Express 2010 und die ASP.NET Web Pages-Tools für Visual Studio installiert.

1. Wenn Sie den Webplattform-Installer noch nicht haben, laden Sie es unter folgender URL herunter:

    [https://www.Microsoft.com/Web/Downloads/Platform.aspx](https://www.microsoft.com/web/downloads/platform.aspx)
2. Führen Sie den Webplattform-Installer.
3. Klicken Sie auf die **Produkte** Registerkarte.

    ![Registerkarte "WebPI Produkte"](program-asp-net-web-pages-in-visual-studio/_static/image10.png)
4. Suchen Sie nach **ASP.NET MVC 4** (für ASP.NET Web Pages 2), und klicken Sie dann auf **hinzufügen**. Diese Produkte umfassen Visual Studio-Tools zum Erstellen von ASP.NET Razor-Websites.

    ![WebPi-Installationsoptionen](program-asp-net-web-pages-in-visual-studio/_static/image11.png)
5. Klicken Sie auf **installieren** um die Installation abzuschließen.
