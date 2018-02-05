---
uid: web-pages/overview/testing-and-debugging/introduction-to-debugging
title: "Einführung in Debugging ASP.NET Web Pages (Razor) Standorte | Microsoft Docs"
author: tfitzmac
description: "Debuggen ist der Prozess des Suchens und Behebens von Fehlern in Ihren Codeseiten. Das Kapitel zeigt, die einige Tools und Techniken, die Sie zum Debuggen verwenden können und Analyz..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: 68de4326-7611-4b9b-b5f6-79b7adc3069f
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/testing-and-debugging/introduction-to-debugging
msc.type: authoredcontent
ms.openlocfilehash: 0b6b5a886efe515b434948dade1ae840ddaecd42
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
<a name="introduction-to-debugging-aspnet-web-pages-razor-sites"></a>Einführung in Debugging ASP.NET Web Pages (Razor)-Websites
====================
durch [Tom FitzMacken](https://github.com/tfitzmac)

> Dieser Artikel beschreibt verschiedene Möglichkeiten zum Debuggen von Seiten auf einer Website für ASP.NET Web Pages (Razor). Debuggen ist der Prozess des Suchens und Behebens von Fehlern in Ihren Codeseiten.
> 
> **Lernen Sie:** 
> 
> - Vorgehensweise beim Anzeigen von Informationen, die der Identifikation analysieren und Debuggen von Seiten.
> - Gewusst wie: Verwenden Sie das Debuggen in Visual Studio-tools.
>   
> 
> Dies sind die Funktionen von ASP.NET im Artikel:
> 
> - Die `ServerInfo` Helper.
> - `ObjectInfo`Hilfsmethode.
>   
> 
> ## <a name="software-versions"></a>Versionen der Software
> 
> 
> - ASP.NET Web Pages (Razor) 3
> - Visual Studio 2013
>   
> 
> Dieses Lernprogramm funktioniert auch mit ASP.NET Web Pages 2. Können Sie WebMatrix 3, aber der integrierte Debugger wird nicht unterstützt.


Ein wichtiger Aspekt der Problembehandlung von Fehlern und Probleme in Ihrem Code ist in erster Linie vermeiden. Möglich, die verlegen Sie Abschnitte des Codes die Ursachen von Fehlern in `try/catch` blockiert. Weitere Informationen finden Sie im Abschnitt zum Behandeln von Fehlern in [Einführung in ASP.NET Web-Programmierung mithilfe der Razor-Syntax](https://go.microsoft.com/fwlink/?LinkId=202890).

Die `ServerInfo` Helper ist ein Diagnosetool, mit dem Sie einen Überblick über die Informationen zu der webserverumgebung bietet, die Ihre Seite hostet. Es zeigt auch, HTTP-Anforderungsinformationen, die gesendet wird, wenn ein Browser die Seite anfordert. Die `ServerInfo` Hilfsprogramm zeigt die aktuelle Benutzeridentität, den Typ des Browsers, die die Anforderung vorgenommen und so weiter. Diese Art von Informationen helfen Ihnen die Behandlung allgemeiner Probleme bei.

1. Erstellen Sie eine neue Webseite mit dem Namen *ServerInfo.cshtml*.
2. Am Ende der Seite nur vor dem abschließenden `</body>` zu kennzeichnen, fügen Sie `@ServerInfo.GetHtml()`:

    [!code-cshtml[Main](introduction-to-debugging/samples/sample1.cshtml)]

    Sie können Hinzufügen der `ServerInfo` Code an einer beliebigen Stelle auf der Seite. Am Ende hinzufügen erhält, behält jedoch die Ausgabe getrennt von der anderen Seiteninhalt, dadurch ist es einfacher zu lesen.

    > [!NOTE] 
    > 
    > **Wichtige** sollten Sie alle Diagnosecode aus Webseiten entfernen, bevor Sie Webseiten auf einem Produktionsserver verschieben. Dies gilt für die `ServerInfo` Helper als auch die anderen Diagnosetechniken in diesem Artikel, bei denen Hinzufügen von Code zu einer Seite. Sie möchten nicht Besucher Ihrer Website, um Informationen zu Ihrem Servernamen, Benutzernamen, Pfaden auf dem Server und ähnliche Details anzuzeigen, da diese Art von Informationen für Personen mit böswilligen Absichten nützlich sein kann.
3. Speichern Sie die Seite, und führen Sie es in einem Browser.

    ![Debugging-1](introduction-to-debugging/_static/image1.jpg)

    Die `ServerInfo` Hilfsprogramm zeigt vier Tabellen von Informationen auf der Seite:

    - Konfiguration des Servers. Dieser Abschnitt enthält Informationen zum hosting Webserver, einschließlich Computernamen, die Version von ASP.NET, die Sie ausführen, den Domänennamen und die Serverzeit an.
    - ASP.NET Servervariablen. Dieser Abschnitt enthält Details über die viele HTTP-Protokolldetails (aufgerufene HTTP-Variablen) und Werte, der jede webseitenanforderung angehören.
    - HTTP-Laufzeitinformationen. Dieser Abschnitt enthält details zu, die die Version von Microsoft .NET Framework, die Ihre Webseite unter ausgeführt wird, den Pfad, der Details zu den Cache und So weiter. (Wie in haben Sie gelernt [Einführung in ASP.NET Web-Programmierung mithilfe der Razor-Syntax](https://go.microsoft.com/fwlink/?LinkId=202890), ASP.NET Web Pages mithilfe der Razor-Syntax basieren auf Microsoft ASP.NET Web Server-Technologie, die selbst auf eine umfangreiche Software erstellt wird Entwicklung Bibliothek als .NET Framework bezeichnet.)
    - Umgebungsvariablen. Dieser Abschnitt enthält eine Übersicht über die lokale Umgebungsvariablen und deren Werte auf dem Webserver an.

    Eine vollständige Beschreibung aller Informationen für die Server und die Anforderung ist nicht Gegenstand dieses Artikels, aber Sie sehen, dass die `ServerInfo` Helper gibt eine Vielzahl von diagnostische Informationen zurück. Weitere Informationen zu den Werten, die `ServerInfo` zurückgibt, finden Sie unter [Umgebungsvariablen erkannt](https://technet.microsoft.com/library/dd560744(WS.10).aspx) auf der Microsoft TechNet-Website und [IIS-Servervariablen](https://msdn.microsoft.com/library/ms524602(VS.90).aspx) auf der MSDN-Website.

## <a name="embedding-output-expressions-to-display-page-values"></a>Einbetten von Ausgabe-Ausdrücke zum Anzeigen von Seite

Eine weitere Möglichkeit, um festzustellen, was im Code geschieht ist Ausgabe Ausdrücke auf der Seite eingebettet. Wie Sie wissen, können Sie den Wert einer Variablen direkt ausgegeben, durch Hinzufügen von etwa `@myVariable` oder `@(subTotal * 12)` auf der Seite. Für das Debuggen, können Sie diese Ausgabe Ausdrücke an strategischen Stellen im Code platzieren. Dadurch können Sie den Wert von Schlüsselvariablen oder das Ergebnis von Berechnungen sehen, wenn die Seite ausgeführt wird. Wenn Sie fertig sind Debuggen, können Sie die Ausdrücke entfernen, oder kommentieren Sie sie. Diese Prozedur zeigt eine typische Herangehensweise an die eingebettete Ausdrücke verwenden, um eine Seite zu debuggen.

1. Erstellen Sie eine neue WebMatrix-Seite mit dem Namen *OutputExpression.cshtml*.
2. Ersetzen Sie den Seiteninhalt durch Folgendes:

    [!code-html[Main](introduction-to-debugging/samples/sample2.html)]

    Im Beispiel wird eine `switch` Anweisung, um die Überprüfung des Werts der `weekday` Variable und klicken Sie dann anzeigen, die eine andere Ausgabenachricht je nach den Tag der Woche ist. Im Beispiel die `if` Block in der ersten Codeblock ändert den Tag der Woche nach dem Zufallsprinzip durch Hinzufügen eines Tages auf den aktuellen Wochentag-Wert. Dies ist ein Fehler, die zur Veranschaulichung eingeführt.
3. Speichern Sie die Seite, und führen Sie es in einem Browser.

    Die Seite zeigt die Meldung für den falschen Tag der Woche. Der Tag der Woche es tatsächlich handelt, wird die Nachricht für einen Tag zu einem späteren Zeitpunkt angezeigt werden. Obwohl in diesem Fall Sie wissen, warum die Nachricht deaktiviert ist (da der Code den falsche Tageswert absichtlich versetzt wird), in der Praxis ist es häufig schwer feststellbar ist, in dem Status der Vorgänge im Code falsche an. Um zu debuggen, müssen Sie herausfinden, was auf den Wert der wichtigsten Objekte und Variablen wie z. B. geschieht `weekday`.
4. Ausgabe Ausdrücke hinzufügen, indem Sie einfügen `@weekday` wie an zwei Stellen angegeben durch den Kommentaren im Code gezeigt. Diese Ausgabe-Ausdrücke werden die Werte der Variablen an diesem Punkt in der Ausführung des Codes angezeigt.

    [!code-csharp[Main](introduction-to-debugging/samples/sample3.cs?highlight=2-3,15-16)]
5. Speichern Sie, und führen Sie die Seite in einem Browser.

    Die Seite zeigt die tatsächlichen Tag der Woche zuerst, und klicken Sie dann die aktualisierte Tag der Woche, resultiert aus dem Hinzufügen eines Tages, und klicken Sie dann die resultierende Nachricht aus der `switch` Anweisung. Die Ausgabe aus den beiden Variablen Ausdrücken (`@weekday`) ohne Leerzeichen zwischen den Tagen hat, da Sie die HTML-Elemente hinzugefügt haben `<p>` Tags aus, um die Ausgabe die Ausdrücke, die nur zu Testzwecken dienen.

    ![Debugging-2](introduction-to-debugging/_static/image2.jpg)

    Jetzt sehen Sie, wo der Fehler ist. Wenn Sie zum ersten Mal Anzeigen der `weekday` Variable im Code, zeigt die richtige Tag. Wenn Sie ihn anzeigen zweiten Mal nach der `if` blockieren im Code, der Tag, die von einem deaktiviert ist. Damit Sie wissen, dass ein Fehler zwischen der ersten und zweiten Darstellung der Wochentag Variablen aufgetreten ist. Wenn dies einen echten Fehler wäre, würde diese Art von Ansatz helfen Ihnen bei den Speicherort des Codes einzugrenzen, die das Problem verursacht.
6. Beheben Sie den Code auf der Seite durch Entfernen von zwei Ausgabe-Ausdrücken, die Sie hinzugefügt, und entfernen den Code, der den Tag der Woche ändert. Die verbleibenden, vollständige Codeblock sieht wie im folgenden Beispiel:

    [!code-cshtml[Main](introduction-to-debugging/samples/sample4.cshtml)]
7. Führen Sie die Seite in einem Browser aus. Diesmal sehen Sie die richtige Nachricht, die für den aktuellen Tag der Woche angezeigt.

## <a name="using-the-objectinfo-helper-to-display-object-values"></a>Verwenden der ObjectInfo-Hilfsmethode zum Objekt anzeigen

Die `ObjectInfo` Hilfsprogramm zeigt den Typ und den Wert des jeweiligen Objekts, das Sie übergeben. Können Sie sie zum Anzeigen des Werts von Variablen und Objekte in Ihrem Code (z. B. die Ausgabe Ausdrücke im vorherigen Beispiel wurde) sowie die sehen Sie den Datentyp von Informationen über das Objekt enthält.

1. Öffnen Sie die Datei mit dem Namen *OutputExpression.cshtml* , die Sie zuvor erstellt haben.
2. Ersetzen Sie alle Code auf der Seite mit den folgenden Codeblock:

    [!code-html[Main](introduction-to-debugging/samples/sample5.html)]
3. Speichern Sie, und führen Sie die Seite in einem Browser.

    ![Debugging-4](introduction-to-debugging/_static/image3.jpg)

    In diesem Beispiel wird die `ObjectInfo` Helper werden zwei Elemente angezeigt:

    - Der Typ. Für die erste Variable, der Typ ist `DayOfWeek`. Für die zweite Variable, der Typ ist `String`.
    - Der Wert. In diesem Fall, da Sie bereits den Wert der Variable für die Grußformel auf der Seite angezeigt, der Wert wird erneut angezeigt, wenn Sie die Variable übergeben `ObjectInfo`.

    Für komplexere Objekte die `ObjectInfo` Helper kann weitere Informationen &#8212;anzeigen; es kann im Grunde wird angezeigt, die Typen und Werte aller Eigenschaften des Objekts.

## <a name="using-debugging-tools-in-visual-studio"></a>Verwenden von Tools zum Debuggen in Visual Studio

Verwenden Sie für eine umfassende Debugleistung erzielen Sie, Visual Studio 2013 oder die kostenlose [Visual Studio Express 2013 für Web](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-2013-express). Mit Visual Studio können Sie einen Haltepunkt im Code über die Befehlszeile festlegen, die Sie untersuchen möchten.

![Haltepunkt festlegen](introduction-to-debugging/_static/image1.png)

Wenn Sie die Website testen, hält die Ausführung von Benutzercode am Haltepunkt.

![Erreichen des Haltepunkts](introduction-to-debugging/_static/image2.png)

Sie können die aktuellen Werte von Variablen und durchlaufen Sie den Code Zeile für Zeile überprüfen.

![Werte anzeigen](introduction-to-debugging/_static/image3.png)

Informationen zur Verwendung von integrierten Debuggers in Visual Studio zum Debuggen von ASP.NET Razor-Pages finden Sie unter [Programmieren von ASP.NET Web Pages (Razor) mit Visual Studio](https://go.microsoft.com/fwlink/?LinkId=205854).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Programmieren von ASP.NET Web Pages (Razor) mithilfe von Visual Studio](https://go.microsoft.com/fwlink/?LinkId=205854)
- [IIS-Servervariablen](https://msdn.microsoft.com/library/ms524602(VS.90).aspx) (MSDN)
- [Umgebungsvariablen erkannt](https://technet.microsoft.com/library/dd560744(WS.10).aspx) (TechNet)
