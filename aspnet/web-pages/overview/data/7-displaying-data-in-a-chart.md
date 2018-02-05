---
uid: web-pages/overview/data/7-displaying-data-in-a-chart
title: Anzeigen von Daten in einem Diagramm mit ASP.NET Web Pages (Razor) | Microsoft Docs
author: microsoft
description: "In diesem Kapitel wird erläutert, wie Daten in einem Diagramm angezeigt werden. In den vorherigen Kapiteln haben Sie gelernt, wie Sie Daten manuell, und in einem Raster anzeigen. In diesem Kapitel wird..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/22/2012
ms.topic: article
ms.assetid: f889fd46-4dac-4ecb-83d8-60e64c22036e
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/data/7-displaying-data-in-a-chart
msc.type: authoredcontent
ms.openlocfilehash: f252b74bc42d0ea65b8b1150973c4f3c50cc9cf4
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
<a name="displaying-data-in-a-chart-with-aspnet-web-pages-razor"></a>Anzeigen von Daten in einem Diagramm mit ASP.NET Web Pages (Razor)
====================
durch [Microsoft](https://github.com/microsoft)

> In diesem Artikel wird erläutert, wie ein Diagramm verwenden, zum Anzeigen von Daten in einer ASP.NET Web Pages (Razor)-Website unter Verwendung der `Chart` Helper.
> 
> **Lernen Sie**:
> 
> - Vorgehensweise beim Anzeigen von Daten in einem Diagramm.
> - Enthält Informationen zum Formatieren von Diagrammen, die mithilfe der integrierten Designs.
> - Gewusst wie: Speichern von Diagrammen und wie Sie sie zur Leistungssteigerung zwischenzuspeichern.
> 
> Hierbei handelt es sich um den ASP.NET Programmieren von Funktionen, die im Artikel:
> 
> - Die `Chart` Helper.
> 
> > [!NOTE]
> > Die Informationen in diesem Artikel gelten für ASP.NET Web Pages 1.0 und Web Pages 2.


<a id="The_Chart_Helper"></a>
## <a name="the-chart-helper"></a>Der Diagramm-Hilfsmethode

Wenn Sie Ihre Daten in grafischer Form anzeigen möchten, können Sie `Chart` Helper. Die `Chart` Hilfsprogramm kann ein Bild, das zeigt Daten in einer Vielzahl von Diagrammtypen rendern. Unterstützt viele Optionen für das Formatieren und beschriften. Die `Chart` Hilfsprogramm kann mehr als 30 Arten von Diagrammen, einschließlich aller Typen von Diagrammen, die Sie möglicherweise vertraut sind mit aus Microsoft Excel oder anderen Tools &#8212;Rendern; Flächendiagramme Balkendiagramme Säulendiagramme, Zeile Diagramme und Kreisdiagrammen zusammen mit mehr spezialisierter Diagramme, z. B. Kursdiagramme.

| **Flächendiagramm** ![Beschreibung: Bild des flächendiagrammtyps](7-displaying-data-in-a-chart/_static/image1.jpg) | **Balkendiagramm** ![Beschreibung: Bild des balkendiagrammtyps](7-displaying-data-in-a-chart/_static/image2.jpg) |
| --- | --- |
| **Säulendiagramm** ![Beschreibung: Bild des säulendiagrammtyps](7-displaying-data-in-a-chart/_static/image3.jpg) | **Liniendiagramm** ![Beschreibung: Bild des liniendiagrammtyps](7-displaying-data-in-a-chart/_static/image4.jpg) |
| **Kreisdiagramm** ![Beschreibung: Bild des Typs Kreisdiagramm](7-displaying-data-in-a-chart/_static/image5.jpg) | **Kursdiagramm** ![Beschreibung: Bild des kursdiagrammtyps](7-displaying-data-in-a-chart/_static/image6.jpg) |

### <a name="chart-elements"></a>Diagrammelemente

Diagramme zeigen Daten und zusätzliche Elemente, z. B. Legenden, Achsen, Reihen und So weiter. Die folgende Abbildung zeigt viele der Diagrammelemente, die Sie anpassen können, bei der Verwendung der `Chart` Helper. Diesem Artikel erfahren Sie, wie Sie einige festlegen (nicht alle) eines dieser Elemente.

![Beschreibung: Bild der Diagrammelemente](7-displaying-data-in-a-chart/_static/image7.jpg)

<a id="Creating_a_Chart"></a>
## <a name="creating-a-chart-from-data"></a>Erstellen eines Diagramms aus Daten

Die Daten in einem Diagramm angezeigten kann aus einem Array, aus den Ergebnissen aus einer Datenbank oder von Daten, die in einer XML-Datei befinden.

### <a name="using-an-array"></a>Verwenden eines Arrays

Wie in beschrieben [Einführung in ASP.NET Web Pages Programmierung mithilfe der Razor-Syntax](https://go.microsoft.com/fwlink/?LinkId=202890), ein Array können Sie eine Auflistung mit ähnlichen Elementen in einer Variablen zu speichern. Sie können Arrays verwenden, um die Daten aufzunehmen, die im Diagramm enthalten sein sollen.

Diese Prozedur zeigt, wie Sie ein Diagramm aus Daten in Arrays erstellen können mithilfe der Standarddiagrammtyp. Es wird gezeigt, wie im Diagramm auf der Seite angezeigt.

1. Erstellen Sie eine neue Datei mit dem Namen *ChartArrayBasic.cshtml*.
2. Ersetzen Sie den vorhandenen Inhalt durch Folgendes: 

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample1.cshtml)]

    Der Code wird zuerst erstellt ein neues Diagramm, und legt die Breite und Höhe. Geben Sie den Diagrammtitel mithilfe der `AddTitle` Methode. Um Daten hinzuzufügen, verwenden Sie die `AddSeries` Methode. In diesem Beispiel verwenden Sie die `name`, `xValue`, und `yValues` Parameter von der `AddSeries` Methode. Die `name` Parameter wird in der Diagrammlegende angezeigt. Die `xValue` Parameter enthält ein Array von Daten, die entlang der horizontalen Achse des Diagramms angezeigt werden. Die `yValues` Parameter enthält ein Array von Daten, die verwendet werden, um die vertikalen Punkte des Diagramms zu zeichnen.

    Die `Write` Methode rendert tatsächlich das Diagramm. In diesem Fall, da Sie einen Diagrammtyp angegeben haben die `Chart` -Hilfsmethode rendert die Standarddiagramms, also ein Säulendiagramm.
3. Führen Sie die Seite im Browser. Der Browser zeigt das Diagramm. 

    ![](7-displaying-data-in-a-chart/_static/image8.jpg)

### <a name="using-a-database-query-for-chart-data"></a>Mithilfe einer Abfrage für Diagrammdaten

Wenn die Informationen, die Sie als Diagramm darstellen möchten in einer Datenbank ist, können Sie Ausführen eine Datenbankabfrage und dann Daten aus den Ergebnissen verwenden, um das Diagramm zu erstellen. Dieses Verfahren wird gezeigt, wie zum Lesen und Anzeigen der Daten aus der Datenbank erstellt, in dem Artikel [Einführung in die Arbeit mit einer Datenbank in ASP.NET Web Pages-Websites](https://go.microsoft.com/fwlink/?LinkId=202893).

1. Hinzufügen einer *App\_Daten* Ordner in das Stammverzeichnis der Website, wenn der Ordner nicht bereits vorhanden ist.
2. In der *App\_Daten* Ordner, fügen Sie die Datenbankdatei mit dem Namen *SmallBakery.sdf* beschriebene [Einführung in die Arbeit mit einer Datenbank in ASP.NET Web Pages-Websites](https://go.microsoft.com/fwlink/?LinkId=202893).
3. Erstellen Sie eine neue Datei mit dem Namen *ChartDataQuery.cshtml*.
4. Ersetzen Sie den vorhandenen Inhalt durch Folgendes:   

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample2.cshtml)]

    Der Code zuerst öffnet die SmallBakery-Datenbank und weist sie einer Variablen namens `db`. Diese Variable steht für ein `Database` -Objekt, das zum Lesen und Schreiben in die Datenbank verwendet werden kann. Als Nächstes führt den Code eine SQL-Abfrage, um den Namen und den Preis jedes Produkts zu erhalten. Der Code erstellt ein neues Diagramm, und übergibt Sie die Datenbankabfrage, durch den Aufruf des Diagramms `DataBindTable` Methode. Diese Methode akzeptiert zwei Parameter: den `dataSource` -Parameter ist für die Daten aus der Abfrage und die `xField` Parameter können Sie festlegen, welche Datenspalte für x-Achse des Diagramms verwendet wird.

    Als Alternative zur Verwendung der `DataBindTable` -Methode, die Sie verwenden die `AddSeries` Methode der `Chart` Helper. Die `AddSeries` Methode ermöglicht das Festlegen der `xValue` und `yValues` Parameter. Beispielsweise anstelle der `DataBindTable` Methode wie folgt:

    [!code-css[Main](7-displaying-data-in-a-chart/samples/sample3.css)]

    Sie können die `AddSeries` Methode wie folgt:

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample4.html)]

    Sowohl die gleichen Ergebnisse zu rendern. Die `AddSeries` Methode ist flexibler, da Sie den Diagrammtyp und Daten mehr explizit angeben können, aber die `DataBindTable` Methode ist einfacher zu verwenden, wenn Sie zusätzliche Flexibilität nicht benötigen.
5. Führen Sie die Seite in einem Browser aus. 

    ![](7-displaying-data-in-a-chart/_static/image9.jpg)

### <a name="using-xml-data"></a>Verwenden von XML-Daten

Die dritte Option für das Diagramm ist die Verwendung eine XML-Datei wie die Daten für das Diagramm. Dies erfordert, dass die XML-Datei auch eine Schemadatei (*XSD* Datei), die die XML-Struktur beschreibt. Dieses Verfahren wird gezeigt, wie Daten aus einer XML-Datei zu lesen.

1. In der *App\_Daten* Ordner, erstellen Sie eine neue XML-Datei mit dem Namen *data.xml*.
2. Ersetzen Sie das vorhandene XML mit den folgenden, die d. h. für einige XML-Daten zu Mitarbeitern in einem fiktiven Unternehmen. 

    [!code-xml[Main](7-displaying-data-in-a-chart/samples/sample5.xml)]
3. In der *App\_Daten* Ordner, erstellen Sie eine neue XML-Datei mit dem Namen *data.xsd*. (Beachten Sie, dass die Erweiterung diesmal *XSD*.)
4. Ersetzen Sie den vorhandenen XML-Code durch Folgendes: 

    [!code-xml[Main](7-displaying-data-in-a-chart/samples/sample6.xml)]
5. Erstellen Sie im Stammverzeichnis der Website, eine neue Datei namens *ChartDataXML.cshtml*.
6. Ersetzen Sie den vorhandenen Inhalt durch Folgendes: 

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample7.cshtml)]

    Der Code erstellt zunächst eine `DataSet` Objekt. Dieses Objekt wird verwendet, um die Daten zu verwalten, die aus der XML-Datei gelesen und Organisieren sie anhand der Informationen in der Schemadatei. (Beachten Sie, dass der Anfang des Codes die Anweisung enthält `using SystemData`. Dies ist erforderlich, damit es zur Bearbeitung kann die `DataSet` Objekt. Weitere Informationen finden Sie unter [ &quot;Using&quot; -Anweisungen und voll qualifizierte Namen](#SB_UsingStatements) weiter unten in diesem Artikel.)

    Als Nächstes erstellt der Code eine `DataView` Objekt auf Grundlage des Datasets. Die Datensicht bietet ein Objekt, das das Diagramm zu binden kann &#8212; d. h. lesen, und zeichnen. Im Diagramm bindet, um die Daten mit der `AddSeries` -Methode, wie Sie weiter oben gesehen haben, wenn die Daten des Arrays, außer dass diesmal Diagramm der `xValue` und `yValues` Parameter werden festgelegt, um die `DataView` Objekt.

    Dieses Beispiel zeigt auch das Angeben eines bestimmten Diagrammtyps. Wenn die Daten hinzugefügt werden, der `AddSeries` -Methode, die `chartType` Parametersatz ist auch ein Kreisdiagramm angezeigt.
7. Führen Sie die Seite in einem Browser aus. 

    ![](7-displaying-data-in-a-chart/_static/image10.jpg)

> [!TIP] 
> 
> <a id="SB_UsingStatements"></a>
> ### <a name="using-statements-and-fully-qualified-names"></a>"Using"-Anweisungen und voll qualifizierte Namen
> 
> .NET Framework, dem ASP.NET Web Pages mit Razor-Syntax basiert besteht aus Tausenden von Komponenten (Klassen). Um ihn zur Bearbeitung von all diese Klassen verwaltbar zu machen, sind sie in organisiert *Namespaces*, wobei es sich um etwas wie Bibliotheken. Z. B. die `System.Web` -Namespace enthält Klassen, die Browser-/Serverkommunikation unterstützen die `System.Xml` -Namespace enthält Klassen, die zum Erstellen und Lesen von XML-Dateien verwendet werden und die `System.Data` -Namespace enthält Klassen, mit denen Sie arbeiten können mit Daten.
> 
> Zum Zugriff auf einer bestimmten Klasse in .NET Framework muss Code kennen, nicht nur der Klassenname, sondern auch den Namespace, dem die Klasse wird. Z. B. zum Verwenden der `Chart` Helper, Code muss finden die `System.Web.Helpers.Chart` -Klasse, die den Namespace kombiniert (`System.Web.Helpers`) mit dem Klassennamen (`Chart`). Dies bezeichnet man der Klasse *vollqualifizierten* Name &#8212; der vollständige, eindeutige Position innerhalb der Umfangs von .NET Framework. Im Code würde dies wie folgt aussehen:
> 
> `var myChart = new System.Web.Helpers.Chart(width: 600, height: 400) // etc.`
> 
> Es ist jedoch umständlich (und fehleranfällig), müssen Sie diese langen, eine vollqualifizierte Namen verwenden, jedes Mal, wenn Sie eine Klasse oder das Hilfsprogramm verweisen möchten. Verwenden Sie den Klassennamen zu vereinfachen, können Sie daher *importieren* Sie insbesondere interessieren, Namespaces in der Regel wird nur eine Handvoll aus vielen-Namespaces in .NET Framework. Wenn Sie einen Namespace importiert haben, können Sie nur ein Klassenname (`Chart`) anstelle der vollqualifizierte Name (`System.Web.Helpers.Chart`). Wenn Code ausgeführt wird und einen Klassennamen auftritt, kann es nur die Namespaces aussehen, die Sie importiert haben, um diese Klasse finden.
> 
> Wenn Sie ASP.NET Web Pages mit Razor-Syntax verwenden, um Webseiten zu erstellen, Sie in der Regel verwenden Sie den gleichen Satz von Klassen in jedem Fall einschließlich der `WebPage` -Klasse, die verschiedene Hilfsprogramme usw. Zum Speichern der Arbeit die relevanten Namespaces importieren, jedes Mal, wenn Sie eine Website erstellen wird ASP.NET konfiguriert, damit er automatisch einen Satz von Core-Namespaces für jede Website importiert. Daher mussten noch nicht für den Umgang mit Namespaces, oder importieren Sie bis jetzt; alle Klassen, mit denen Sie gearbeitet haben, werden in Namespaces, die Sie bereits importiert sind.
> 
> Allerdings müssen gelegentlich Sie arbeiten mit einer Klasse, die nicht in einem Namespace, die für Sie automatisch importiert wird. In diesem Fall können Sie entweder den vollqualifizierten Namen von dieser Klasse ab, oder Sie können den Namespace, der die Klasse enthält, manuell importieren. Um einen Namespace zu importieren, verwenden Sie die `using` Anweisung (`import` in Visual Basic), wie Sie in einem Beispiel vorhin den Artikel.
> 
> Z. B. die `DataSet` Klasse befindet sich in der `System.Data` Namespace. Die `System.Data` Namespace ist nicht automatisch für ASP.NET Razor-Pages zur Verfügung. Aus diesem Grund zur Bearbeitung der `DataSet` -Klasse unter Verwendung des vollqualifizierten Namens, können Sie Code wie folgt verwenden:
> 
> `var dataSet = new System.Data.DataSet();`
> 
> Wenn Sie verwenden die `DataSet` -Klasse wiederholt Sie einen Namespace wie folgt importieren und verwenden Sie nur den Klassennamen in Code:
> 
> [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample8.cshtml)]
> 
> Sie können hinzufügen `using` -Anweisungen für alle anderen .NET Framework-Namespaces, die Sie verweisen möchten. Wie bereits erwähnt, Sie wird nicht jedoch häufig dazu, da die meisten Klassen, mit denen Sie zusammenarbeiten müssen in Namespaces befinden, die von ASP.NET automatisch, für die Verwendung in importiert werden *cshtml* und *vbhtml* Seiten.


<a id="Displaying_Charts"></a>
## <a name="displaying-charts-inside-a-web-page"></a>Anzeigen von Diagrammen in einer Webseite

Sie haben bisher gesehen Erstellen eines Diagramms, und klicken Sie dann das Diagramm wird direkt an den Browser als Grafik gerendert, in den Beispielen. In vielen Fällen möchten jedoch ein Diagramm als Teil einer Seite nicht nur alleine im Browser angezeigt. Zu diesem Zweck müssen einen zweistufiger Prozess. Der erste Schritt besteht, erstellen Sie eine Seite, die das Diagramm generiert, wie Sie bereits gesehen haben.

Der zweite Schritt ist das resultierende Image in eine andere Seite angezeigt. Um das Bild angezeigt wird, verwenden Sie ein HTML `<img>` Element, auf die gleiche Weise um Bild anzuzeigen. Jedoch anstelle von Verweisen auf eine *jpg* oder *PNG* Datei, die `<img>` Elementverweise der *cshtml* Datei, enthält die `Chart` Helper, erstellt das Diagramm an. Wenn die Seite "Anzeige" ausgeführt wird, wird die `<img>` Element Ruft die Ausgabe von der `Chart` Helper und rendert das Diagramm.

![](7-displaying-data-in-a-chart/_static/image11.jpg)

1. Erstellen Sie eine Datei mit dem Namen *ShowChart.cshtml*.
2. Ersetzen Sie den vorhandenen Inhalt durch Folgendes: 

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample9.html)]

    Der Code verwendet die `<img>` Element im Diagramm angezeigt, die Sie zuvor erstellt die *ChartArrayBasic.cshtml* Datei.
3. Führen Sie die Webseite in einem Browser aus. Die *ShowChart.cshtml* Datei zeigt das Diagrammbild basierend auf der Code in der *ChartArrayBasic.cshtml* Datei.

<a id="Styling_a_Chart"></a>
## <a name="styling-a-chart"></a>Formatieren eines Diagramms

Die `Chart` Helper unterstützt eine große Anzahl von Optionen, mit denen Sie die Darstellung des Diagramms anpassen können. Sie können Farben, Schriftarten, Rahmen und usw. festlegen. Eine einfache Möglichkeit zum Anpassen der Darstellung eines Diagramms ist die Verwendung einer *Design*. Designs sind Informationssammlungen, die angeben, wie ein Diagramm mithilfe von Schriftarten, Farben, Beschriftungen, Paletten, Rahmen und Effekten gerendert. (Beachten Sie, dass das Format eines Diagramms den Typ des Diagramms nicht angegeben ist.)

Die folgende Tabelle enthält die integrierten Designs.

| Design | Beschreibung |
| --- | --- |
| `Vanilla` | Zeigt den roten Spalten auf weißem Hintergrund an. |
| `Blue` | Zeigt blaue Spalten in einem blauen Verlauf Hintergrund dargestellt. |
| `Green` | Zeigt Blau Spalten auf einen grünen Farbverlaufshintergrund. |
| `Yellow` | Zeigt den orangefarbenen Spalten auf einen Farbverlauf gelben Hintergrund an. |
| `Vanilla3D` | Zeigt 3D Rot Spalten auf weißem Hintergrund an. |

Sie können angeben, dass das Design zu verwenden, wenn Sie ein neues Diagramm erstellen.

1. Erstellen Sie eine neue Datei mit dem Namen *ChartStyleGreen.cshtml*.
2. Ersetzen Sie den vorhandenen Inhalt auf der Seite mit den folgenden:

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample10.cshtml)]

    Dieser Code entspricht dem oben aufgeführten Beispiel, das die Datenbank für Daten verwendet, fügt jedoch das `theme` Parameter beim Erstellen der `Chart` Objekt. Das folgende Beispiel zeigt den geänderten Code aus:

    [!code-csharp[Main](7-displaying-data-in-a-chart/samples/sample11.cs)]
3. Führen Sie die Seite in einem Browser aus. Sehen Sie die gleichen Daten wie vor, aber das Diagramm sieht professionelles aus: 

    ![](7-displaying-data-in-a-chart/_static/image12.jpg)

<a id="Saving_a_Chart"></a>
## <a name="saving-a-chart"></a>Speichern eines Diagramms

Bei Verwendung der `Chart` Helper als Sie kennen gelernt haben bisher in diesem Artikel das Hilfsprogramm erneut das Diagramm von Grund auf neu erstellt bei jedem Aufruf erfolgte. Bei Bedarf wird in der Code für das Diagramm auch erneut Abfragen die Datenbank oder erneut liest die XML-Datei zum Abrufen der Daten. In einigen Fällen auf diese Weise kann ein komplexer Vorgang sein, z. B. wenn die Datenbank, die Sie Abfragen sind sehr groß ist oder wenn die XML-Datei eine große Datenmenge enthält. Selbst wenn das Diagramm eine große Datenmenge nicht einbezogen, beanspruchen dynamisch erstellen ein Abbild von Serverressourcen, und wenn viele Personen anfordern die Seite oder Seiten, die im Diagramm angezeigt werden, treten möglicherweise Auswirkungen auf die Leistung Ihrer Website.

Um der potenziellen Leistungseinbußen durch Erstellen eines Diagramms reduzieren, können Sie einem Diagramm den ersten Zeitpunkt erstellen Sie ihn benötigen, und speichern Sie sie. Wird das Diagramm wieder gebraucht, anstatt Sie neu, können Sie einfach die gespeicherte Version abrufen und rendern.

Sie können ein Diagramm wie folgt speichern:

- Das Diagramm im Arbeitsspeicher des Computers (auf dem Server) im Cache.
- Speichern Sie das Diagramm als Bilddatei.
- Speichern Sie das Diagramm als eine XML-Datei. Diese Option können Sie das Diagramm zu ändern, bevor Sie sie speichern.

### <a name="caching-a-chart"></a>Zwischenspeichern eines Diagramms

Nachdem Sie ein Diagramm erstellt haben, können Sie ihn Zwischenspeichern. Zwischenspeichern eines Diagramms bedeutet, dass es nicht neu erstellt werden, wenn er erneut angezeigt werden muss. Wenn Sie ein Diagramm im Cache speichern, weisen Sie dieser einen Schlüssel, der für dieses Diagramm eindeutig sein muss.

Diagramme, die im Cache gespeichert möglicherweise entfernt werden, wenn der Server für den Arbeitsspeicher knapp wird. Darüber hinaus wird der Cache geleert, wenn die Anwendung aus irgendeinem Grund neu gestartet wird. Daher ist die gängiges Verfahren zum Arbeiten mit einem zwischengespeicherte Diagramm um immer zuerst überprüfen, ob im Cache verfügbar ist, und falls nicht, und klicken Sie dann zum Erstellen oder neu erstellen.

1. Erstellen Sie im Stammverzeichnis der Website eine Datei namens *ShowCachedChart.cshtml*.
2. Ersetzen Sie den vorhandenen Inhalt durch Folgendes: 

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample12.html)]

    Die `<img>` Tag enthält eine `src` -Attribut, das auf verweist die *ChartSaveToCache.cshtml* Datei und einen Schlüssel auf die Seite als eine Abfragezeichenfolge übergeben. Der Schlüssel enthält den Wert &quot;MyChartKey&quot;. Die *ChartSaveToCache.cshtml* -Datei enthält die `Chart` -Hilfsprogramm, das das Diagramm erstellt. Erstellen Sie diese Seite in wenigen Augenblicken.

    Am Ende der Seite wird ein Link zu einer Seite mit dem Namen *ClearCache.cshtml*. Dies ist eine Seite, die Sie auch in Kürze erstellen müssen. Müssen Sie die *ClearCache.cshtml* nur für die Zwischenspeicherung für dieses Beispiel zu testen – ist nicht, einen Link oder eine Seite, die Sie normalerweise beim Arbeiten mit Diagrammen zwischengespeicherte enthalten würde.
3. Erstellen Sie im Stammverzeichnis der Website eine neue Datei namens *ChartSaveToCache.cshtml*.
4. Ersetzen Sie den vorhandenen Inhalt durch Folgendes:

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample13.cshtml)]

    Der Code überprüft zunächst, ob alle Elemente als der Schlüsselwert in der Abfragezeichenfolge übergeben wurde. Wenn also der Code versucht, lesen ein Diagramm aus dem Cache durch Aufrufen der `GetFromCache` -Methode und übergeben sie den Schlüssel. Wenn sich herausstellt, die es "nothing" in den Cache unter diesem Schlüssel wird (geschieht erstmalig, die das Diagramm angefordert wird), erstellt der Code im Diagramm wie gewohnt aus. Wenn das Diagramm abgeschlossen ist, der Code speichert es in den Cache durch Aufrufen von `SaveToCache`. Diese Methode benötigt einen Schlüssel (damit das Diagramm später angefordert werden kann) und die Zeitspanne, die das Diagramm im Cache gespeichert werden soll. (Die genaue Uhrzeit, die Sie einem Diagramm Zwischenspeichern würde hängt wie oft betrachtet werden kann, dass die Daten, die er darstellt geändert werden können.) Die `SaveToCache` Methode erfordert außerdem eine `slidingExpiration` Parameter &#8212; Wenn diese Option festgelegt ist auf "true", das Timeout Indikator wird jedes Mal zurückgesetzt Diagramm zugegriffen wird. In diesem Fall bedeutet dies faktisch Cacheeintrag des Diagramms endet die Gültigkeit 2 Minuten seit der letzten jemand das Diagramm zugreifen. (Die Alternative zur Ablaufzeit ist ein absoluter Ablauf, was bedeutet, dass der Cacheeintrag abläuft würde genau zwei Minuten, nachdem er angehalten wurde, in den Cache, unabhängig davon, wie oft es zugegriffen wurde.)

    Schließlich verwendet der Code die `WriteFromCache` Methode zum Abrufen und das Diagramm aus dem Cache zu rendern. Beachten Sie, dass diese Methode außerhalb der `if` Block, der den Cache überprüft werden, da das Diagramm aus dem Cache abgerufen werden sollen, ob das Diagramm es zunächst gab oder mussten generiert und im Cache gespeichert werden.

    Beachten Sie, dass im Beispiel die `AddTitle` Methode enthält einen Zeitstempel. (Fügt das aktuelle Datum und Uhrzeit &#8212; `DateTime.Now` &#8212; um den Titel.)
5. Erstellen Sie eine neue Seite mit dem Namen *ClearCache.cshtml* und seinen Inhalt durch Folgendes ersetzen:

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample14.cshtml)]

    Diese Seite verwendet der `WebCache` Hilfsfunktion, um das Diagramm zu entfernen, die im Zwischenspeicher *ChartSaveToCache.cshtml*. Wie bereits erwähnt, müssen Sie normalerweise eine Seite wie folgt. Sie erstellen hier nur um das Zwischenspeichern testen zu vereinfachen.
6. Führen Sie die *ShowCachedChart.cshtml* Webseite in einem Browser. Die Seite zeigt das Diagrammbild basierend auf der Code in der *ChartSaveToCache.cshtml* Datei. Beachten Sie in den Diagrammtitel der Zeitstempel Aussage. 

    ![Beschreibung: Bild eines einfachen Diagramms mit Timestamp in den Diagrammtitel](7-displaying-data-in-a-chart/_static/image13.jpg)
7. Schließen Sie den Browser.
8. Führen Sie die *ShowCachedChart.cshtml* erneut aus. Beachten Sie, dass der Zeitstempel der gleiche wie zuvor – gibt an, dass das Diagramm wurde nicht erneut generiert, sondern stattdessen aus dem Cache gelesen wurde.
9. In *ShowCachedChart.cshtml*, klicken Sie auf die **Cache löschen** Link. Dadurch gelangen Sie zu *ClearCache.cshtml*, der gemeldet wird, dass der Cache gelöscht wurde.
10. Klicken Sie auf die **zurück zu ShowCachedChart.cshtml** verknüpfen, oder führen Sie erneut aus *ShowCachedChart.cshtml* von WebMatrix. Beachten Sie, dass es sich bei dieser Zeit die Zeitstempel geändert hat, da der Cache gelöscht wurde. Daher musste der Code zum Generieren des Diagramms, und fügen Sie sie wieder in den Cache ein.

### <a name="saving-a-chart-as-an-image-file"></a>Speichern eines Diagramms als Bilddatei

Sie können auch ein Diagramm speichern, als Bilddatei (z. B. eine *jpg* Datei) auf dem Server. Anschließend können Sie die Bilddatei genauso wie jedes Bild. Der Vorteil ist die Datei wird gespeichert, sondern in einem temporären Cache gespeichert. Sie können speichern ein neuen Diagrammbilds zu unterschiedlichen Zeitpunkten (z. B. einmal pro Stunde) und behalten Sie eine permanente Aufzeichnung der Änderungen, die im Zeitverlauf. Beachten Sie, dass Sie sicherstellen müssen, dass Ihre Webanwendung verfügt über die Berechtigung zum Speichern einer Datei in den Ordner auf dem Server, auf dem Sie die Bilddatei aufnehmen möchten.

1. Erstellen Sie im Stammverzeichnis der Website einen Ordner namens  *\_ChartFiles* , wenn sie nicht bereits vorhanden ist.
2. Erstellen Sie im Stammverzeichnis der Website eine neue Datei namens *ChartSave.cshtml*.
3. Ersetzen Sie den vorhandenen Inhalt durch Folgendes:

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample15.cshtml)]

    Der Code zunächst geprüft, ob die *jpg* Datei vorhanden ist, durch Aufrufen der `File.Exists` Methode. Wenn die Datei nicht vorhanden ist, erstellt der Code ein neues `Chart` aus einem Array. Diesmal ist der Code Ruft die `Save` -Methode auf und übergibt die `path` Parameter, um den Pfad und Dateinamen zum Speichern des Diagramms anzugeben. Im Hauptteil der Seite ein `<img>` Element verwendet den Pfad zur zeigen Sie auf die *jpg* Datei angezeigt.
4. Führen Sie die *ChartSave.cshtml* Datei.
5. WebMatrix zurück. Beachten Sie die Bilddatei mit dem Namen *chart01.jpg* wurde gespeichert, der  *\_ChartFiles* Ordner.

### <a name="saving-a-chart-as-an-xml-file"></a>Speichern eines Diagramms als eine XML-Datei

Schließlich können Sie ein Diagramm als eine XML-Datei auf dem Server speichern. Ein Vorteil der Verwendung dieser Methode über Zwischenspeichern des Diagramms oder Speichern des Diagramms in eine Datei ist, dass Sie den XML-Code vor der Anzeige des Diagramms aus, wenn gewünscht ändern können. Die Anwendung muss über Lese-/Schreibberechtigungen für den Ordner auf dem Server verfügen, in dem Sie die Bilddatei aufnehmen möchten.

1. Erstellen Sie im Stammverzeichnis der Website eine neue Datei namens *ChartSaveXml.cshtml*.
2. Ersetzen Sie den vorhandenen Inhalt durch Folgendes:

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample16.cshtml)]

    Dieser Code stellt ähnlich dem Code, den Sie zuvor gesehen zum Speichern eines Diagramms im Cache, haben außer dass es sich um eine XML-Datei verwendet. Der Code prüft zunächst, ob die XML-Datei vorhanden, durch Aufrufen ist der `File.Exists` Methode. Wenn die Datei vorhanden ist, erstellt der Code ein neues `Chart` -Objekt und übergibt den Dateinamen als die `themePath` Parameter. Dadurch wird das Diagramm anhand jeder beliebigen XML-Datei wird erstellt. Wenn die XML-Datei nicht bereits vorhanden ist, wird der Code erstellt ein Diagramm wie üblich und ruft dann `SaveXml` zu speichern. Das Diagramm gerendert wird, mithilfe der `Write` -Methode, wie Sie haben gesehen vor.

    Wie bei der Seite, die caching ergab, enthält dieser Code einen Zeitstempel in den Diagrammtitel ein.
3. Erstellen Sie eine neue Seite mit dem Namen *ChartDisplayXMLChart.cshtml* und fügen Sie das folgende Markup hinzu: 

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample17.html)]
4. Führen Sie die *ChartDisplayXMLChart.cshtml* Seite. Das Diagramm wird angezeigt. Notieren Sie sich den Zeitstempel in das Diagramm Titel.
5. Schließen Sie den Browser.
6. WebMatrix, mit der Maustaste die  *\_ChartFiles* Ordner, klicken Sie auf **aktualisieren**, und öffnen Sie den Ordner. Die *XMLChart.xml* Datei in diesem Ordner erstellt wurde, indem die `Chart` Helper. 

    ![Beschreibung: Der _ChartFiles Ordner die XMLChart.xml-Datei erstellt, indem das Hilfsprogramm Diagramm angezeigt.](7-displaying-data-in-a-chart/_static/image14.jpg)
7. Führen Sie die *ChartDisplayXMLChart.cshtml* Seite erneut. Das Diagramm zeigt den gleichen Zeitstempel als ausführen auf der Seite "wurde zum ersten Mal an. Grund hierfür das Diagramm aus der XML-Code generiert wird, die Sie zuvor gespeichert ist.
8. Öffnen Sie in WebMatrix die  *\_ChartFiles* Ordner und löschen die *XMLChart.xml* Datei.
9. Führen Sie die *ChartDisplayXMLChart.cshtml* Seite erneut. Dieses Mal der Zeitstempel aktualisiert wurde, da die `Chart` Helper musste der XML-Datei erneut zu erstellen. Wenn Sie möchten, überprüfen Sie die  *\_ChartFiles* Ordner, und beachten Sie, dass die XML-Datei zurück.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Einführung in die Arbeit mit einer Datenbank in der ASP.NET Web Pages Standorte](https://go.microsoft.com/fwlink/?LinkId=202893)
- [Verwendung von Caching in ASP.NET Web Pages Standorte zur Verbesserung der Leistung](https://go.microsoft.com/fwlink/?LinkId=202903)
- [Klasse "diagrammflächeneigenschaften"](https://msdn.microsoft.com/library/system.web.helpers.chart(v=vs.99)) (ASP.NET Web Pages-API-Referenz auf MSDN)
