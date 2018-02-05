---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/entering-data
title: "Einführung in ASP.NET Web Pages - Eingabe von Datenbankdaten mit Forms | Microsoft Docs"
author: tfitzmac
description: Dieses Lernprogramm zeigt das Erstellen eines Formulars Eintrag und geben dann die Daten, die Sie erhalten von dem Formular in eine Datenbanktabelle bei der Verwendung von ASP.NET Web Pages...
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: d37c93fc-25fd-4e94-8671-0d437beef206
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/entering-data
msc.type: authoredcontent
ms.openlocfilehash: b74eecb16b2c4695bb417816b90f701f724cc9d0
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---entering-database-data-by-using-forms"></a>Einführung in ASP.NET Web Pages - Datenbankdaten mithilfe von Formularen eingeben
====================
durch [Tom FitzMacken](https://github.com/tfitzmac)

> In diesem Lernprogramm wird gezeigt, wie zum Erstellen eines Formulars Eintrag und geben dann die Daten, die Sie von dem Formular in eine Datenbanktabelle erhalten bei der Verwendung von ASP.NET Web Pages (Razor). Es wird vorausgesetzt, Sie haben die Reihe über abgeschlossen [Grundlagen von HTML-Formularen in ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251581).
> 
> Lernen Sie:
> 
> - Weitere Informationen wie Formulare verarbeitet.
> - Wie (Insert) Daten in einer Datenbank hinzugefügt.
> - Wie Sie sicherstellen, dass Benutzer einen erforderlichen Wert in einem Formular (Gewusst wie: Überprüfen von Benutzereingaben) eingegeben haben.
> - Vorgehensweise beim Anzeigen von Validierungsfehlern.
> - Wie aus der aktuellen Seite zu einer anderen Seite wechseln.
>   
> 
> Features/Technologien erläutert:
> 
> - Die `Database.Execute`-Methode.
> - Die SQL `Insert Into` Anweisung
> - Die `Validation` Helper.
> - Die `Response.Redirect`-Methode.


## <a name="what-youll-build"></a>Was müssen Sie erstellen

Im Lernprogramm weiter oben, die Sie wurde gezeigt, wie Sie eine Datenbank erstellen, eingegebene Datenbankdaten durch Bearbeiten der Datenbank direkt in WebMatrix, arbeiten der **Datenbank** Arbeitsbereich. In den meisten apps ist keine praktische Möglichkeit, die Daten jedoch in der Datenbank zu speichern. Daher in diesem Lernprogramm erstellen eine webbasierte Oberfläche Sie, mit dem Sie oder ein anderer Nutzer Daten eingeben und speichern Sie sie mit der Datenbank.

Sie erstellen eine Seite, in dem Sie neue Kinofilmen eingeben können. Die Seite wird eine Eintragsformular enthalten, die Felder (Textfelder) hat, in dem Sie eine Filmtitel, "Genre" und Jahr eingeben können. Die Seite wird auf dieser Seite formuliert werden:

![Seite im Browser "Film hinzufügen"](entering-data/_static/image1.png)

Textfelder werden HTML `<input>` Elemente, das aussieht wie dieses Markup:

`<input type="text" name="genre" value="" />`

## <a name="creating-the-basic-entry-form"></a>Erstellen die grundlegenden Eintragsformular

Erstellen Sie eine Seite mit dem Namen *AddMovie.cshtml*.

Neuigkeiten in der Datei durch Folgendes Markup zu ersetzen. Überschreiben Sie alles; Sie müssen einen Codeblock in Kürze oben hinzufügen.

[!code-cshtml[Main](entering-data/samples/sample1.cshtml)]

Dieses Beispiel zeigt typische HTML für die Erstellung eines Formulars an. Er verwendet `<input>` Elemente für die Textfelder und die Schaltfläche "Absenden". Die Beschriftungen für die Textfelder werden mit standardmäßigen erstellt `<label>` Elemente. Die `<fieldset>` und `<legend>` Elemente put ein nützliches Feld, um das Formular.

Beachten Sie, dass auf dieser Seite die `<form>` -Element verwendet `post` als Wert für die `method` Attribut. Im vorherigen Lernprogramm erstellt Sie ein Formular, mit denen die `get` Methode. Da jedoch das Formular Werte an den Server gesendet, die Anforderung keine Änderungen vorgenommen korrekt ist, war. Alles, was zuvor war Daten abzurufen, auf unterschiedliche Weise. Allerdings in dieser Seite *wird* Änderungen vornehmen – Sie sind im Begriff, die Datenbank neue Datensätze hinzufügen. Deshalb sollte dieser Form verwenden die `post` Methode. (Weitere Informationen zu den Unterschieden zwischen `GET` und `POST` Vorgänge, finden Sie unter der[GET, POST und HTTP-Verb für Sicherheit](https://go.microsoft.com/fwlink/?LinkId=251581#GET,_POST,_and_HTTP_Verb_Safety) Randleiste im vorherigen Lernprogramm.)

Beachten Sie, dass jedes Textfeld eine `name` Element (`title`, `genre`, `year`). Im vorherigen Lernprogramm haben Sie gesehen, sind diese Namen wichtig, da Sie diese Namen verfügen müssen, damit Sie später die Eingaben des Benutzers abrufen können. Sie können eine beliebige Namen verwenden. Es ist hilfreich, verwenden Sie aussagekräftige Namen ein, mit denen Sie denken Sie daran, welche Daten mit dem Sie arbeiten.

Die `value` -Attribut der einzelnen `<input>` Element enthält einen Teil der Razor-Code (z. B. `Request.Form["title"]`). Haben Sie gelernt eine Version von dieser Trick im vorherigen Lernprogramm den Wert eingegeben werden, in das Textfeld (sofern vorhanden) erhalten bleiben nach dem Senden des Formulars.

## <a name="getting-the-form-values"></a>Die Formularwerte abrufen

Als Nächstes fügen Sie Code, der das Formular verarbeitet. In umrissen müssen Sie Folgendes ausführen:

1. Überprüfen Sie, ob die Seite gesendet wird (gesendet wurde). Sie möchten in Ihrem Code nur ausgeführt, wenn Benutzer auf die Schaltfläche geklickt haben, nicht verwendet werden, wenn die Seite zuerst ausgeführt wird.
2. Ruft die Werte, die der Benutzer in die Textfelder eingegeben. In diesem Fall, da das Formular verwendet die `POST` Verb, erhalten Sie die Werte von der `Request.Form` Auflistung.
3. Fügen Sie die Werte als ein neuer Datensatz in die *Filme* Datenbanktabelle.

Fügen Sie am Anfang der Datei den folgenden Code hinzu:

[!code-cshtml[Main](entering-data/samples/sample2.cshtml)]

Die ersten Zeilen erstellen Sie Variablen (`title`, `genre`, und `year`) zum Speichern der Werte in den Textfeldern. Die Zeile `if(IsPost)` wird sichergestellt, dass die Variablen festgelegt werden *nur* Benutzer beim Klicken auf die **Film hinzufügen** Schaltfläche – d. h., wenn das Formular bereitgestellt wurde.

In einer früheren Lernprogramm haben Sie gesehen, erhalten Sie den Wert eines Textfelds mithilfe eines Ausdrucks wie `Request.Form["name"]`, wobei *Namen* ist der Name des der `<input>` Element.

Die Namen der Variablen (`title`, `genre`, und `year`) sind willkürlich. Wie die Namen, die Sie zuweisen `<input>` Elemente, Sie können die sie beliebig aufrufen. (Die Namen der Variablen keine entsprechend den Attributen "Name" der `<input>` Elemente auf dem Formular.) Aber wie bei der `<input>` Elemente, es ist eine gute Idee Variablennamen verwenden, die die Daten widerspiegeln, die sie enthalten. Wenn Sie Code schreiben, erleichtern konsistente Namen für Sie, welche Daten speichern, mit dem Sie arbeiten.

## <a name="adding-data-to-the-database"></a>Hinzufügen von Daten in der Datenbank

Im Code blockieren Sie die soeben hinzugefügte, nur die *in* die schließende geschweifte Klammer ( `}` ) von der `if` (nicht nur innerhalb des Codeblocks) blockieren, fügen Sie den folgenden Code hinzu:

[!code-csharp[Main](entering-data/samples/sample3.cs)]

Dieses Beispiel ähnelt der Code, den Sie in einem vorherigen Lernprogramm für abrufen und Anzeigen von Daten verwendet. Die Zeile, die mit beginnt `db =` geöffnet, die die Datenbank, z. B. vor, und die nächste Zeile definiert eine SQL-Anweisung erneut als Sie gesehen haben, bevor Sie. Jedoch dieses Mal definiert SQL `Insert Into` Anweisung. Das folgende Beispiel zeigt die allgemeine Syntax von der `Insert Into` Anweisung:

`INSERT INTO table (column1, column2, column3, ...) VALUES (value1, value2, value3, ...)`

Das heißt, geben Sie die Tabelle einzufügen, dann Listet die Spalten einzufügen und dann die einzufügenden Werte aufgeführt. (Wie bereits erwähnt, ist SQL kein Groß-/Kleinschreibung beachtet, aber einige Personen daraus Kapital schlagen die Schlüsselwörter zum Lesen des Befehls zu erleichtern.)

Die Spalten, die Sie in einfügen, sind bereits im Befehl aufgeführt – `(Title, Genre, Year)`. Ist das interessante daran, wie Sie die Werte in den Textfeldern in Abrufen der `VALUES` Teil des Befehls. Anstelle der eigentlichen Werte finden Sie unter `@0`, `@1`, und `@2`, dem sind natürlich Platzhalter. Wenn Sie den Befehl ausführen (auf der `db.Execute` Zeile), übergeben Sie die Werte, die Sie in den Textfeldern an.

**Wichtig!** Denken Sie daran, dass die einzige Möglichkeit, Sie sollte jemals enthalten Daten, die online von einem Benutzer in einer SQL­Anweisung eingegeben ist die Verwendung von Platzhaltern, wie Sie hier sehen (`VALUES(@0, @1, @2)`). Wenn Sie Benutzereingaben in einer SQL-Anweisung verketten, Sie öffnen, selbst für eine SQL-Injection-Angriff wie in beschrieben [Form-Grundlagen in ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251581) (dem vorherigen Lernprogramm).

Immer noch innerhalb der `if` blockieren, fügen Sie die folgende Zeile nach der `db.Execute` Zeile:

[!code-css[Main](entering-data/samples/sample4.css)]

Nachdem die neue Film in die Datenbank eingefügt wurde, wechselt diese Zeile Sie (leitet) zur der *Filme* Seite, damit Sie den Film sehen können, die Sie gerade eingegeben haben. Die `~` Operator bedeutet "Root der Website". (Die `~` Operator funktioniert nur im ASP.NET-Seiten nicht in HTML im Allgemeinen.)

Der Codeblock abgeschlossen sieht wie Folgendes Beispiel:

[!code-cshtml[Main](entering-data/samples/sample5.cshtml)]

## <a name="testing-the-insert-command-so-far"></a>Testen des Insert-Befehls (bisher)

Sie sind noch nicht fertig, aber jetzt ist ein guter Zeitpunkt, zu testen.

In der Strukturansicht der Dateien in WebMatrix, mit der Maustaste die *AddMovie.cshtml* Seite, und klicken Sie dann auf **in Browser starten**.

![Seite im Browser "Film hinzufügen"](entering-data/_static/image2.png)

(Wenn Sie sich mit einer anderen Seite im Browser beenden, stellen Sie sicher, dass die URL `http://localhost:nnnnn/AddMovie`), wobei  *nnnnn*  ist die Portnummer, die Sie verwenden.)

Haben Sie eine Fehlerseite erhalten? Wenn dies der Fall ist, wird lesen sie sorgfältig, und stellen Sie sicher, dass der Code genau wie weiter oben aufgeführt wurden.

Geben Sie einen Film im Formular &mdash; verwenden z. B. "Bürger Kane", "Filmen" und "1941". (Oder nach Belieben.) Klicken Sie dann auf **Film hinzufügen**.

Wenn alles gut geht, sind Sie umgeleitet, auf die *Filme* Seite. Stellen Sie sicher, dass der neue Film aufgeführt ist.

![Filme-Seite, die neu mit hinzugefügt Film](entering-data/_static/image3.png)

## <a name="validating-user-input"></a>Validieren von Benutzereingaben

Wechseln Sie zurück zu den *AddMovie* Seite, oder führen Sie es erneut. Geben Sie dieses Mal jedoch einen anderen Film, geben Sie nur einen Titel &mdash; Geben Sie beispielsweise "Einloggen ' in der Regen". Klicken Sie dann auf **Film hinzufügen**.

Sie sind umgeleitet, um die *Filme* Seite erneut. Sie erhalten den neuen Film, jedoch ist es unvollständig.

![Filme Seite mit neuen Film, die einige Werte fehlen](entering-data/_static/image4.png)

Beim Erstellen der *Filme* Tabelle, Sie explizit angegeben, dass keines der Felder null sein kann. Hier haben Sie ein Eintragsformular für neue Kinofilmen, und Sie können Felder leer lassen. Dies ist ein Fehler.

In diesem Fall hat nicht die Datenbank tatsächlich auslösen (oder *auslösen*) einen Fehler. Sie haben nicht einen "Genre" oder ein Jahr, also angeben den Code in der *AddMovie* Seite behandelt diese Werte als so genannten *leere Zeichenfolgen*. Wenn die SQL `Insert Into` -Befehl ausgeführt haben, die Felder "Genre" und das Jahr haben nicht nützliche Daten in diesen haben, aber null nicht waren.

Sie möchte nicht offensichtlich, Benutzer, die Hälfte leere Filminformationen in die Datenbank eingeben können. Die Lösung besteht darin, die Eingaben des Benutzers zu überprüfen. Zu Beginn die Überprüfung wird einfach stellen Sie sicher, dass der Benutzer einen Wert für alle Felder eingegeben hat (d. h., dass Sie nicht davon enthält eine leere Zeichenfolge).

> [!TIP] 
> 
> **NULL und leere Zeichenfolgen**
> 
> Bei der Programmierung wird unterschieden zwischen den verschiedenen Arten von "kein Wert". Im Allgemeinen ist ein Wert *null* Wenn nie festgelegt oder in irgendeiner Weise initialisiert wurde. Im Gegensatz dazu kann eine Variable, die Zeichendaten (Zeichenfolgen) erwartet festgelegt werden, um eine *leere Zeichenfolge*. In diesem Fall ist der Wert nicht null. Es ist nur explizit in eine Zeichenfolge von Zeichen festgelegt wurde, dessen Länge 0 (null) ist. Diese beiden Anweisungen zeigen den Unterschied:
> 
> [!code-csharp[Main](entering-data/samples/sample6.cs)]
> 
> Es ist etwas mehr als die kompliziert, aber der wichtige Punkt ist, die `null` eine Sortierung der unbestimmten Zustand darstellt.
> 
> Und ist es wichtig zu wissen genau, wenn ein Wert null ist und wenn es nur eine leere Zeichenfolge ist. Im Code für die *AddMovie* Seite erhalten Sie die Werte der Textfelder mit `Request.Form["title"]` und so weiter. Wenn die Seite zuerst ausgeführt wird, (bevor Sie auf die Schaltfläche klicken), wird der Wert des `Request.Form["title"]` ist null. Aber bei der Übermittlung des Formulars `Request.Form["title"]` Ruft den Wert der `title` Textfeld. Es ist nicht offensichtlich, aber ein leeres Textfeld ist ungleich null; Es enthält nur eine leere Zeichenfolge. Beim Ausführen des Codes als Antwort auf die Schaltfläche klicken, `Request.Form["title"]` weist eine leere Zeichenfolge auf.
> 
> Warum ist diese Unterscheidung wichtig? Beim Erstellen der *Filme* Tabelle, Sie explizit angegeben, dass keines der Felder null sein kann. Hier haben Sie ein Eintragsformular für neue Kinofilmen und können Sie Felder leer lassen. Sie würden vernünftigerweise erwarten, dass die Datenbank darüber beschweren, bei dem Versuch, neue Kinofilmen zu speichern, die Werte für "Genre" oder Jahr hatten. Aber dies ist der Punkt &mdash; , auch wenn Sie diese Felder leer lassen, werden die Werte null nicht; sie leere Zeichenfolgen sind. Daher können Sie Ihre neue Kinofilmen in der Datenbank mit den folgenden leeren Spalten speichern &mdash; jedoch nicht null! &mdash;-Werte sind. Aus diesem Grund müssen Sie sicherstellen, dass Benutzer eine leere Zeichenfolge übermitteln, Sie durch Eingabe des Benutzers überprüfen können.


### <a name="the-validation-helper"></a>Der Überprüfungshelfer

ASP.NET Web Pages enthält eine Hilfsprogramm &mdash; der `Validation` Helper &mdash; , Sie verwenden können, um sicherzustellen, dass Benutzer Daten eingeben, die Ihren Anforderungen entspricht. Die `Validation` Helper ist eine der Hilfsprogramme, die für ASP.NET Web Pages in erstellt wird, daher ist es nicht zu installieren, es als ein Paket mit NuGet, wie Sie das Hilfsobjekt a. mit Gravatar in einer früheren Lernprogramm installiert.

Um die Eingaben des Benutzers zu überprüfen, müssen Sie Folgendes ausführen:

- Verwenden Sie Code, um anzugeben, dass Sie Werte in die Textfelder auf der Seite erforderlich sein soll.
- Fügen Sie einen Test in den Code, damit die Informationen in der Datenbank hinzugefügt wird, nur, wenn alles ordnungsgemäß überprüft.
- Fügen Sie Code in das Markup zum Anzeigen von Fehlermeldungen an.

In den Codeblock in der *AddMovie* Seite, fügen Sie oben rechts oben vor Variablendeklarationen, den folgenden Code:

[!code-csharp[Main](entering-data/samples/sample7.cs)]

Rufen Sie `Validation.RequireField` einmal für jedes Feld (`<input>` Element), in dem einen Eintrag erstellt werden sollen. Sie können auch eine benutzerdefinierte Fehlermeldung für jeden Aufruf, wie Sie hier finden Sie unter hinzufügen. (Wir variiert die Nachrichten nur an, dass Sie einfügen können alles gewünschte vorhanden.)

Wenn ein Problem vorliegt, möchten Sie verhindert, dass die neue Filminformationen in die Datenbank eingefügt wird. In der `if(IsPost)` blockieren, verwenden Sie `&&` (logisches AND), um eine andere Bedingung hinzuzufügen, die testet `Validation.IsValid()`. Wenn Sie fertig sind, die gesamte `if(IsPost)` Block sieht dieser Code:

[!code-csharp[Main](entering-data/samples/sample8.cs)]

Wenn es tritt ein Validierungsfehler mit den Feldern, die Sie registriert den `Validation` Hilfsfunktion, die `Validation.IsValid` Methode "false" zurückgegeben. Und in diesem Fall keine des Codes in diesem Block wird ausgeführt, damit keine ungültigen Film-Einträge in der Datenbank eingefügt werden. Und Sie sind natürlich nicht umgeleitet die *Filme* Seite.

Der vollständige Codeblock, einschließlich des Validierungscodes sieht nun wie in diesem Beispiel:

[!code-cshtml[Main](entering-data/samples/sample9.cshtml?highlight=10)]

## <a name="displaying-validation-errors"></a>Anzeigen von Validierungsfehlern

Der letzte Schritt besteht darin alle Fehlermeldungen anzuzeigen. Sie können einzelne Nachrichten für jede Validierungsfehler oder anzeigen können Sie eine Zusammenfassung oder beides anzeigen. Für dieses Lernprogramm müssen Sie beide ausführen, damit Sie sehen, wie er funktioniert.

Neben den jeweiligen `<input>` Element, das Sie überprüfen möchten, rufen die `Html.ValidationMessage` Methode und übergeben sie den Namen des der `<input>` Element, die Sie überprüfen möchten. Setzen Sie die `Html.ValidationMessage` Methode rechts, der die Fehlermeldung angezeigt werden soll. Wenn die Seite ausgeführt wird, die `Html.ValidationMessage` -Methode rendert ein `<span>` Element, in dem der Validierungsfehler geht. (Wenn kein Fehler auftritt, die `<span>` Element gerendert wird, aber es ist kein Text darin.)

Ändern Sie das Markup der Seite, sodass er enthält ein `Html.ValidationMessage` -Methode für jede der drei `<input>` Elemente auf der Seite, wie im folgenden Beispiel:

[!code-cshtml[Main](entering-data/samples/sample10.cshtml?highlight=3,8,13)]

Um zu sehen, wie die Zusammenfassung funktioniert, auch fügen Sie das folgende Markup und code direkt nach der `<h1>Add a Movie</h1>` Element auf der Seite:

[!code-cshtml[Main](entering-data/samples/sample11.cshtml)]

Standardmäßig die `Html.ValidationSummary` Methode zeigt alle validierungsmeldungen in einer Liste (eine `<ul>` Elements in einem `<div>` Element). Wie bei der `Html.ValidationMessage` -Methode, das Markup für die validierungszusammenfassung wird immer gerendert; Wenn keine Fehler vorliegen, werden keine Listenelemente gerendert.

Die Zusammenfassung kann eine alternative Möglichkeit, anstelle von validierungsmeldungen angezeigt werden die `Html.ValidationMessage` Methode, um jeden Fehler feldspezifischen anzuzeigen. Oder Sie können eine Zusammenfassung und Details. Oder Sie können die `Html.ValidationSummary` Methode, um eine allgemeine Fehlermeldung angezeigt, und verwenden Sie dann auf einzelne `Html.ValidationMessage` aufrufen, um Details anzuzeigen.

Auf der Seite abgeschlossen sieht nun wie in diesem Beispiel:

[!code-cshtml[Main](entering-data/samples/sample12.cshtml)]

Das ist alles. Sie können jetzt die Seite testen, indem beim Hinzufügen eines Films jedoch eine oder mehrere Felder eingepasst. Wenn Sie dies tun, sehen Sie die folgende Fehlermeldung angezeigt:

![Hinzufügen von Film-Seite, die mit der Validierung Fehlermeldungen](entering-data/_static/image5.png)

## <a name="styling-the-validation-error-messages"></a>Formatieren von Validierungsfehlermeldungen

Sie können sehen, dass Fehlermeldungen enthalten sind, aber sie nicht wirklich sehr gut hervorzuheben. Es ist eine einfache Möglichkeit, über die Fehlermeldungen zu formatieren.

So formatieren Sie die einzelne Fehlermeldungen, die angezeigt werden `Html.ValidationMessage`, erstellen Sie eine CSS-Formatklasse `field-validation-error`. Um die Darstellung für die validierungszusammenfassung zu definieren, erstellen Sie eine CSS-Formatklasse `validation-summary-errors`.

Um zu sehen, wie diese Methode funktioniert, fügen einen `<style>` Element innerhalb der `<head>` Abschnitt der Seite. Definieren Sie dann mit dem Namen Formatklassen `field-validation-error` und `validation-summary-errors` , enthalten die folgenden Regeln:

[!code-cshtml[Main](entering-data/samples/sample13.cshtml?highlight=4-17)]

Normalerweise legen Sie wahrscheinlich die Stilinformationen in eine Separate *CSS* Datei, aber aus Gründen der Einfachheit Sie können ablegen, werden auf der Seite vorläufig. (Später in diesem Lernprogramm Satz, verschieben Sie die CSS-Regeln zu einem eigenen *CSS* Datei.)

Wenn ein Validierungsfehler, die `Html.ValidationMessage` -Methode rendert ein `<span>` -Element, enthält `class="field-validation-error"`. Durch Hinzufügen einer Formatdefinition für diese Klasse, können Sie konfigurieren, wie die Nachricht aussieht. Wenn Fehler vorhanden sind, die `ValidationSummary` Methode rendert entsprechend dynamisch das Attribut `class="validation-summary-errors"`.

Führen Sie die Seite erneut aus, und lassen Sie absichtlich ein Paar Felder. Die Fehler sind nun deutlicher. (In der Tat sie overdone sind, aber das ist einfach, um anzuzeigen, was Sie tun können.)

![Hinzufügen von Film-Seite, die mit der Validierungsfehler, die formatiert wurde, haben](entering-data/_static/image6.png)

## <a name="adding-a-link-to-the-movies-page"></a>Beim Hinzufügen einer Verknüpfung zur Seite "Filme"

Eine letzte Schritt besteht darin, um erhalten vereinfachen die *AddMovie* Seite aus dem ursprünglichen Film-Angebot.

Öffnen der *Filme* Seite erneut. Nach dem schließenden `</div>` Tag, das folgt die `WebGrid` Helper, fügen Sie das folgende Markup hinzu:

[!code-cshtml[Main](entering-data/samples/sample14.cshtml)]

Wie vorhin, ASP.NET interpretiert die `~` Operator als Stammverzeichnis der Website. Müssen Sie verwenden nicht die `~` -Operator können Sie das Markup `<a href="./AddMovie">Add a movie</a>` oder auf andere Weise auf den Pfad zu definieren, die HTML versteht. Aber die `~` Operator ist ein guter Allgemein Ansatz bei der Erstellung von Links für Razor-Pages, da die Site flexibler ist – Wenn Sie die aktuelle Seite in einen Unterordner verschieben, der Link wird weiterhin fahren Sie mit der *AddMovie* Seite. (Beachten Sie, dass die `~` Operator funktioniert nur in *cshtml* Seiten. ASP.NET weiß, aber es ist keine standard-HTML.)

Wenn Sie fertig sind, führen die *Filme* Seite. Es wird auf dieser Seite formuliert werden:

![Filme Seite mit Link zur Seite "Filme hinzufügen"](entering-data/_static/image7.png)

Klicken Sie auf die **hinzufügen einen Film** Link, um sicherzustellen, dass er auf geht die *AddMovie* Seite.

## <a name="coming-up-next"></a>Als Nächstes kommen

In den nächsten Lernprogrammen erfahren Sie, wie, damit Benutzer Daten bearbeiten, die bereits in der Datenbank vorhanden ist.

## <a name="complete-listing-for-addmovie-page"></a>Vollständige Auflistung für die Seite "AddMovie"

[!code-cshtml[Main](entering-data/samples/sample15.cshtml)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Einführung in ASP.NET-Webprogrammierung mithilfe der Razor-Syntax](https://go.microsoft.com/fwlink/?LinkID=202890)
- [SQL INSERT INTO-Anweisung](http://www.w3schools.com/sql/sql_insert.asp) auf der Website W3Schools
- [Validieren von Benutzereingaben in ASP.NET Web Pages Sites](https://go.microsoft.com/fwlink/?LinkId=253002). Weitere Informationen zum Arbeiten mit der `Validation` Helper.

>[!div class="step-by-step"]
[Zurück](form-basics.md)
[Weiter](updating-data.md)
