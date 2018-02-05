---
uid: web-pages/overview/ui-layouts-and-themes/10-working-with-video
title: Anzeigen von Video in einer ASP.NET Web-Seiten (Razor) Standort | Microsoft Docs
author: tfitzmac
description: "In diesem Kapitel wird erläutert, wie Video in einer ASP.NET Web Pages mit Razor-Syntax-Seite angezeigt wird."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: 332fb3da-e2a5-460d-bb90-dd911e1e2c95
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/10-working-with-video
msc.type: authoredcontent
ms.openlocfilehash: a14659997d86d1b5cf5381e21e997c1a03a3f57c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
<a name="displaying-video-in-an-aspnet-web-pages-razor-site"></a>Anzeigen von Video an einem Standort der ASP.NET Web Pages (Razor)
====================
durch [Tom FitzMacken](https://github.com/tfitzmac)

> In diesem Artikel wird erläutert, wie verwenden Sie einen Player Video (Medium) auf einer Website für ASP.NET Web Pages (Razor), damit Sie Benutzern das Anzeigen von Videos, die auf der Website gespeichert sind. ASP.NET Web Pages mit Razor-Syntax können Sie die Wiedergabe von Flash (*SWF*), Media Player (*.wmv*), und Silverlight (*XAP*) Videos.
> 
> Lernen Sie:
> 
> - Wie Sie einen video Player auswählen.
> - Das Hinzufügen von Videos zu einer Webseite.
> - Vorgehensweise Videoplayer Attribute festgelegt werden.
> 
> Hierbei handelt es sich um den ASP.NET Razor-Pages in diesem Artikel vorgestellten Funktionen dar:
> 
> - Die `Video` Helper.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>In diesem Lernprogramm verwendeten Versionen der Software
> 
> 
> - ASP.NET Web Pages (Razor) 2
> - WebMatrix 2
>   
> 
> Dieses Lernprogramm funktioniert auch mit WebMatrix-3.


## <a name="introduction"></a>Einführung

Möglicherweise möchten die Darstellung eines Videos auf Ihrer Website. Eine Möglichkeit dazu besteht darin zu einer Website zu verknüpfen, die bereits im Video, z. B. YouTube. Wenn Sie ein Video von diesen Standorten direkt in Ihren eigenen Seiten einbetten möchten, können Sie HTML-Markup in der Regel aus der Website abrufen und kopieren sie dann in die Seite. Z. B. das folgende Beispiel zeigt einen YouTube Einbettung video:

[!code-html[Main](10-working-with-video/samples/sample1.html?highlight=10-14)]

Wenn Sie möchten ein Video abspielen, die auf eine eigene Website (nicht auf eine öffentliche Freigabe mit dem Video Website) ist, können nicht Sie direkt mit eingebetteten Markup wie folgt verknüpfen. Sie Videos über Ihren Standort wiedergeben, mit der `Video` Hilfsprogramm, das einen Media-Player direkt auf einer Seite gerendert wird.

<a id="Choosing_a_Video_Player"></a>
## <a name="choosing-a-video-player"></a>Einen Video Player auswählen

Es gibt viele Formate für Videodateien, und jedes Format in der Regel erfordert einen anderen Player und eine andere Möglichkeit, die der Spieler zu konfigurieren. In ASP.NET Razor-Pages, können Sie ein Video in einer Webseite mit spielen die `Video` Helper. Die `Video` Hilfsprogramm vereinfacht das Videos auf einer Webseite einbetten, da es automatisch generiert die `object` und `embed` HTML-Elemente, mit denen normalerweise Video auf der Seite hinzufügen.

Die `Video` Hilfsprogramm unterstützt die folgenden MediaPlayer:

- Adobe Flash
- Windows MediaPlayer
- Microsoft Silverlight

### <a name="the-flash-player"></a>Flash Player

Die `Flash` Player, der die `Video` Hilfsprogramm können Sie die Wiedergabe von Videos Flash (*SWF* Dateien) auf einer Webseite. Zumindest müssen Sie einen Pfad zu der Videodatei angeben. Wenn Sie ausschließlich den Pfad angeben, verwendet der Player Standardwerte aufgeführt, die von der aktuellen Version von Flash festgelegt sind. Übliche Standardnamespace Einstellungen sind:

- Das Video angezeigt wird, verwenden die Standardbreite und-Höhe und ohne eine Hintergrundfarbe.
- Das Video werden beim Laden der Seite automatisch wiedergegeben.
- Das Video Schleifen fortlaufend, bis er explizit beendet wird.
- Das Video wird skaliert, um alle das Video, anstatt das Video entsprechend eine bestimmte Größe zuschneiden anzuzeigen.
- Das Video in einem Fenster wiedergegeben wird.

### <a name="the-mediaplayer-player"></a>Der Media Player-Player

Die `MediaPlayer` Player, der die `Video` -Hilfsobjekt ermöglicht Ihnen die Wiedergabe von Windows Media-Videos (*.wmv* Dateien), Windows Media Audio (*.wma* Dateien), und MP3 (*MP3* Dateien) auf einer Webseite. Sie müssen die Mediendatei zum Wiedergeben von Pfadangaben; Alle anderen Parameter sind optional. Wenn Sie nur einen Pfad angeben, verwendet der Spieler Standardeinstellungen, die von der aktuellen Version von Media Player, wie z. B. festgelegt:

- Das Video wird mit dessen Standardwert Breite und Höhe angezeigt.
- Das Video werden beim Laden der Seite automatisch wiedergegeben.
- Das Video einmal wiedergegeben werden (es keine Schleife).
- Der Spieler zeigt den vollständigen Satz von Steuerelementen in der Benutzeroberfläche.
- Das Video spielt bei der in einem Fenster.

### <a name="the-silverlight-player"></a>Die Silverlight-Players

Die `Silverlight` Player, der die `Video` -Hilfsobjekt ermöglicht Ihnen die Wiedergabe von Windows Media Video (*.wmv* Dateien), Windows Media Audio (*.wma* Dateien), und MP3 (*MP3* -Dateien). Sie müssen die Path-Parameter, zeigen Sie auf ein Silverlight-basierten Anwendungspaket festlegen (*XAP* Datei). Sie müssen auch die Parameter für Breite und Höhe festlegen. Alle anderen Parameter sind optional. Bei Verwendung von Silverlight-Players Video, wenn Sie festlegen, dass nur die erforderlichen Parameter zeigt der Silverlight-Player das Video ohne eine Hintergrundfarbe.

> [!NOTE]
> Für den Fall, dass Sie Silverlight noch nicht kennen: die *XAP* Datei ist eine komprimierte Datei mit layoutanweisungen in einer *XAML* Datei von verwaltetem Code in Assemblys und optionale Ressourcen. Sie erstellen eine *XAP* Datei in Visual Studio als ein Silverlight-Anwendungsprojekt.


Die `Silverlight` Videoplayer verwendet sowohl die Einstellungen, die Sie für den Player bereitstellen und die Einstellungen, die finden Sie in der *XAP* Datei.

> [!TIP] 
> 
> <a id="SB_MimeTypes"></a>
> ### <a name="mime-types"></a>MIME-Typen
> 
> Wenn eine Datei von ein Browser heruntergeladen wird, sendet der Browser sicher, dass der Dateityp den MIME-Typ übereinstimmt, der für das Dokument angegeben wird, die gerendert wird. Der MIME-Typ ist der Typ oder ein Medium Inhaltstyp einer Datei. Die `Video` Hilfsprogramm verwendet die folgenden MIME-Typen:
> 
> - `application/x-shockwave-flash`
> - `application/x-mplayer2`
> - `application/x-silverlight-2`


<a id="Playing_Flash"></a>
## <a name="playing-flash-swf-videos"></a>Wiedergabe von Videos Flash (SWF)

Dieses Verfahren zeigt, wie ein Flash Video mit dem Namen abgespielt *sample.swf*. Vorausgesetzt, Sie haben einen Ordner namens *Medien* auf Ihrer Website, und dass die *SWF* Datei ist in diesem Ordner.

1. Der ASP.NET Web Helpers Library zu Ihrer Website hinzufügen, wie in beschrieben [installieren-Hilfsprogramme in einer ASP.NET Web Pages-Website](https://go.microsoft.com/fwlink/?LinkId=252372), wenn Sie bereits hinzugefügt haben.
2. Klicken Sie auf der Website, fügen Sie eine Seite, und nennen Sie sie *FlashVideo.cshtml*.
3. Fügen Sie auf der Seite das folgende Markup hinzu: 

    [!code-cshtml[Main](10-working-with-video/samples/sample2.cshtml)]
4. Führen Sie die Seite in einem Browser aus. (Stellen Sie sicher, dass die Seite ist ausgewählt, der **Dateien** Arbeitsbereich vor der Ausführung.) Die Seite wird angezeigt, und das Video automatisch wiedergegeben. 

    ![[image]](10-working-with-video/_static/image1.jpg "ch08_video-1.jpg")

Sie können festlegen, die `quality` -Parameter für ein Flash Video `low`, `autolow`, `autohigh`, `medium`, `high`, und `best`:

[!code-cshtml[Main](10-working-with-video/samples/sample3.cshtml)]

Sie können das Flash Video an eine bestimmte Größe mit wiedergegeben Ändern der `scale` -Parameter, der Sie die folgenden festlegen können:

- `showall` Dies macht das gesamte Video beim Beibehalten des ursprünglichen Seitenverhältnisses sichtbar. Allerdings können Sie Rahmen auf jeder Seite am Ende.
- `noorder` Skaliert das Video während das Originalseitenverhältnis beibehalten, aber zugeschnitten werden kann.
- `exactfit` Dies macht das gesamte Video sichtbar ohne das ursprüngliche Seitenverhältnis beibehalten, aber Verzerrung auftreten.

Wenn Sie nicht angeben einer `scale` Parameter, das gesamte Video werden angezeigt, und das ursprüngliche Seitenverhältnis beibehalten ohne sämtlichen Zuschneidevorgängen aussieht. Das folgende Beispiel zeigt, wie Sie die `scale` Parameter:

[!code-cshtml[Main](10-working-with-video/samples/sample4.cshtml)]

Flash Player unterstützt einen Einstellung mit dem Namen Videomodus `windowMode`. Sie können festlegen, um `window`, `opaque`, und `transparent`. Wird standardmäßig die `windowMode` festgelegt ist, um `window`, dem das Video in einem separaten Fenster auf der Webseite angezeigt. Die `opaque` Einstellung alles, was hinter das Video auf der Webseite werden ausgeblendet. Die `transparent` Einstellung ermöglicht es den Hintergrund der Webseite anzeigen, die durch das Video vorausgesetzt eines beliebigen Teils des Videos ist transparent.

<a id="Playing_MediaPlayer"></a>
## <a name="playing-mediaplayer-wmv-videos"></a>Wiedergeben von MediaPlayer (*.wmv*) Videos

Das folgende Verfahren veranschaulicht das Fenster Medien mit dem Namen Videowiedergabe *"Sample.wmv"* , die sich in der *Medien* Ordner.

1. Der ASP.NET Web Helpers Library zu Ihrer Website hinzufügen, wie in beschrieben [installieren-Hilfsprogramme in einer ASP.NET Web Pages-Website](https://go.microsoft.com/fwlink/?LinkId=252372), sofern Sie noch nicht geschehen.
2. Erstellen Sie eine neue Seite mit dem Namen *MediaPlayerVideo.cshtml*.
3. Fügen Sie auf der Seite das folgende Markup hinzu: 

    [!code-cshtml[Main](10-working-with-video/samples/sample5.cshtml)]
4. Führen Sie die Seite in einem Browser aus. Das Video geladen und automatisch wiedergegeben. 

    ![[image]](10-working-with-video/_static/image2.jpg "ch08_video-2.jpg")

Sie können festlegen, `playCount` in eine ganze Zahl, der angibt, wie oft automatisch das Video abzuspielen:

[!code-cshtml[Main](10-working-with-video/samples/sample6.cshtml)]

Die `uiMode` Parameter können Sie angeben, welche Steuerelemente in der Benutzeroberfläche angezeigt. Sie können festlegen, `uiMode` auf `invisible`, `none`, `mini`, oder `full`. Wenn Sie nicht angeben einer `uiMode` Parameter das Video werden seek mit dem Statusfenster angezeigt, Balken-, Schaltflächen und Volume-Steuerelementen neben der video-Fenster zu steuern. Diese Steuerelemente werden auch angezeigt werden, wenn Sie den Player verwenden, um eine Audiodatei. Hier ist ein Beispiel zum Verwenden der `uiMode` Parameter:

[!code-cshtml[Main](10-working-with-video/samples/sample7.cshtml)]

Standardmäßig ist Audio auf, bei der Wiedergabe des Videos. Sie können das Audio Stummschalten, durch Festlegen der `mute` Parameter auf "true":

[!code-cshtml[Main](10-working-with-video/samples/sample8.cshtml)]

Sie können das audio Maß an das Video MediaPlayer steuern, durch Festlegen der `volume` Parameter auf einen Wert zwischen 0 und 100. Der Standardwert ist 50. Im Folgenden ein Beispiel:

[!code-cshtml[Main](10-working-with-video/samples/sample9.cshtml)]

<a id="Playing_Silverlight"></a>
## <a name="playing-silverlight-videos"></a>Wiedergabe von Silverlight-Videos

Dieses Verfahren wird gezeigt, wie in Silverlight enthaltenen Video abzuspielen *XAP* Seite in einem Ordner mit dem Namen *Medien*.

1. Der ASP.NET Web Helpers Library zu Ihrer Website hinzufügen, wie in beschrieben [installieren-Hilfsprogramme in einer ASP.NET Web Pages-Website](https://go.microsoft.com/fwlink/?LinkId=252372), sofern Sie noch nicht geschehen.
2. Erstellen Sie eine neue Seite mit dem Namen *SilverlightVideo.cshtml*.
3. Fügen Sie auf der Seite das folgende Markup hinzu: 

    [!code-cshtml[Main](10-working-with-video/samples/sample10.cshtml)]
4. Führen Sie die Seite in einem Browser aus. 

    ![[image]](10-working-with-video/_static/image3.jpg "ch08_video-3.jpg")

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Zusätzliche Ressourcen


[Silverlight-Übersicht](https://msdn.microsoft.com/library/bb404700(VS.95).aspx)

[Flash-Objekt und das EMBED-Tag-Attributen](http://kb2.adobe.com/cps/127/tn_12701.html)

[Windows Media Player 11 SDK PARAM-Tags](https://msdn.microsoft.com/library/aa392321(VS.85).aspx)
