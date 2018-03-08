---
title: "Verhindern von siteübergreifendem Skripting"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/cross-site-scripting
ms.openlocfilehash: d0880fda4ee726bd30a48cce0907a3887f2a4545
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="preventing-cross-site-scripting"></a>Verhindern von siteübergreifendem Skripting

<a name="security-cross-site-scripting"></a>

Cross-Site-Skripting (XSS) ist ein Sicherheitsrisiko Dadurch kann ein Angreifer clientseitige Skripts (normalerweise "JavaScript") in Webseiten zu platzieren. Wenn andere Benutzer betroffene Seiten geladen werden, die Angreifer Skripts ausgeführt werden, Aktivieren der Angreifer Cookies und Sitzungstoken zu stehlen ändern Sie den Inhalt der Webseite über DOM-Manipulation oder den Browser zu einer anderen Seite umgeleitet. XSS-Sicherheitsrisiken auftreten in der Regel auf, wenn eine Anwendung eine Benutzereingabe akzeptiert und ihn auf einer Seite gibt ohne überprüfen, Codierung, oder er Escapezeichen.

## <a name="protecting-your-application-against-xss"></a>Schützen Ihre Anwendung mit XSS

AT einer grundlegenden Ebene XSS funktioniert, indem Sie dazu die Anwendung in das Einfügen von einem `<script>` Tag in der gerenderten Seite oder durch Einfügen einer `On*` Ereignis in ein Element. Entwickler sollten die folgenden Schritte für die Verhinderung von, vermeiden Sie unnötigen XSS in ihrer Anwendung verwenden.

1. Nicht vertrauenswürdige Daten abgelegt nie die HTML-Eingaben, es sei denn, Sie führen Sie die restlichen Schritte ausführen. Nicht vertrauenswürdige Daten ist alle Daten, die von einem Angreifer, HTML-Formulareingaben, Abfragezeichenfolgen, HTTP-Header, selbst Daten stammen aus einer Datenbank, da ein Angreifer möglicherweise auf Ihre Datenbank zu verletzen, auch wenn sie Ihre Anwendung verletzen können nicht gesteuert werden können.

2. Stellen Sie sicher, dass es sich um HTML-codiert ist, vor dem Platzieren von nicht vertrauenswürdiger Daten innerhalb eines HTML-Elements. HTML-Codierung Zeichen wie z. B. akzeptiert &lt; und ändert diese in eine sichere Form wie &amp;Lt;

3. Stellen Sie sicher, dass es sich um HTML-Attributs codiert ist, vor dem Einfügen von nicht vertrauenswürdiger Daten in einem HTML-Attribut. HTML-attributcodierung eine Obermenge der HTML-Codierung codiert und zusätzliche Zeichen wie z. B. "und".

4. Platzieren Sie die Daten vor dem Einfügen von nicht vertrauenswürdiger Daten in JavaScript in einem HTML-Element, dessen Inhalt Sie zur Laufzeit abrufen. Wenn dies nicht möglich, stellen Sie sicher Daten wird JavaScript codiert. JavaScript-Codierung nimmt gefährliche Zeichen für JavaScript und z. B. durch ihre Hex ersetzt &lt; würde codiert werden, als `\u003C`.

5. Stellen Sie sicher, dass sie die URL-codiert ist, vor dem Einfügen von nicht vertrauenswürdiger Daten in einer URL-Abfragezeichenfolge.

## <a name="html-encoding-using-razor"></a>HTML-Codierung mit Razor

Das Razor-Modul automatisch in MVC verwendet codiert alle Ausgabe auf Variablen, stammen, es sei denn, Sie tatsächlich hart arbeiten, um zu verhindern, dass es auf diese Weise. Er verwendet die HTML-Attributs Codierungsregeln jedes Mal, wenn Sie die  *@*  Richtlinie. Als HTML ist das attributcodierung eine Obermenge der HTML-Codierung Dies bedeutet, dass Sie keine betreffen, selbst mit, ob Sie HTML-Codierung oder HTML-attributcodierung verwenden soll. Achten Sie darauf, nur @ in einem HTML-Kontext verwenden nicht, wenn nicht vertrauenswürdige Eingaben direkt in JavaScript einfügen möchten. Tag-Hilfsmethoden werden auch Eingabe codieren, die in der Tag-Parameter verwendet.

Ausführen der folgenden Razor-Ansicht an.

```none
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Diese Sicht gibt den Inhalt der *UntrustedInput* Variable. Diese Variable enthält einige Zeichen, die im XSS-Angriffen, d. h. verwendet werden &lt;, "und &gt;. Untersuchen die Quelle zeigt die gerenderte Ausgabe als codiert:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC bietet eine `HtmlString` Klasse, die automatisch bei Ausgabe codiert ist nicht. Dies sollte nie in Kombination mit nicht vertrauenswürdigen Eingabe verwendet werden, da dies ein Sicherheitsrisiko XSS verfügbar machen soll.

## <a name="javascript-encoding-using-razor"></a>JavaScript-Codierung mithilfe von Razor

Möglicherweise gibt es Zeiten, die zum Einfügen eines Werts in JavaScript, die in der Ansicht verarbeitet werden sollen. Hierfür gibt es zwei Möglichkeiten. Die sicherste Möglichkeit, einfache Werte einfügen ist, platzieren den Wert in einem Datenattribut eines Tags und in Ihrer JavaScript-abgerufen. Zum Beispiel:

```none
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Dies erzeugt die folgenden HTML-Code

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Die, die Folgendes ausgeführt wird, rendert,

```none
<"123">
   <"123">
   ```

Sie können den JavaScript-Encoder auch direkt aufrufen,

```none
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
   ```

Dies wird wie folgt im Browser gerendert;

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> Verketten Sie keine nicht vertrauenswürdige Eingaben in JavaScript in der DOM-Elemente zu erstellen. Verwenden Sie `createElement()` und Zuweisen von Eigenschaftswerten entsprechend z. B. `node.TextContent=`, oder verwenden Sie `element.SetAttribute()` / `element[attribute]=` andernfalls Sie setzen DOM-basierte XSS.

## <a name="accessing-encoders-in-code"></a>Beim Zugriff auf den Encoder in code

Die HTML, JavaScript und URL-Encoder für Ihren Code auf zwei Arten verfügbar sind, können Sie sie über einfügen [Abhängigkeitsinjektion](../fundamentals/dependency-injection.md#fundamentals-dependency-injection) oder Sie können die Standard-Encoder in enthalten die `System.Text.Encodings.Web` Namespace. Wenn Sie die Standard-Encoder verwenden, und klicken Sie dann alle auf Sie angewendet Zeichenbereiche werden als sicher behandelt wird nicht wirksam: Verwenden Sie die Standard-Encoder die sichersten mögliche Codierungsregeln.

Die konfigurierbaren Encoder über DI verwenden Ihre Konstruktoren sollte ein *HtmlEncoder*, *JavaScriptEncoder* und *UrlEncoder* Parameter nach Bedarf. Beispiel:

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>Codierung von URL-Parameter

Wenn Sie eine URL-Abfragezeichenfolge mit nicht vertrauenswürdigen Eingabe als Wert Verwendung erstellen möchten die `UrlEncoder` um den Wert zu codieren. Ein auf ein Objekt angewendeter

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Nach der Codierung der Codierterwert enthält Variable `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Leerzeichen, Anführungszeichen, Satzzeichen und andere unsicheren Zeichen in Prozent in ihren Hexadezimalwert codiert, z. B. wird ein Leerzeichen %20 sein.

>[!WARNING]
> Verwenden Sie keine nicht vertrauenswürdigen Eingabe als Teil eines URL-Pfads. Übergeben Sie nicht vertrauenswürdige Eingaben immer als Wert einer Abfragezeichenfolge.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Anpassen der Encoder

Standardmäßig Encoder verwenden eine Sicherungsliste begrenzt auf die grundlegenden lateinischen Unicode-Bereich, und Codieren aller Zeichen außerhalb dieses Bereichs als ihre entsprechenden Code. Dieses Verhalten wirkt sich auch auf die Razor taghelpers und HtmlHelper Rendering aus, wie die Encoder zur Ausgabe von Zeichenfolgen verwendet werden.

Die Überlegung hinter dieser wird zum Schutz von unbekannten oder zukünftige Browser Fehler (vorherige Browser Fehler haben das Einrichten der Analyse basierend auf der Verarbeitung von nicht-englische Zeichen aktiviert). Wenn Ihre Website starke Nutzung von nicht-lateinische Zeichen, z. B. Chinesisch, macht ist Kyrillisch oder andere Personen dies wahrscheinlich nicht das gewünschte Verhalten.

Sie können anpassen, die Listen der Encoder sichere Einbeziehung Unicode, Bereiche, die für Ihre Anwendung während des Starts geeignet ist, in `ConfigureServices()`.

Z. B. mit der Konfiguration können Sie eine Razor-HtmlHelper wie;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Wenn Sie die Quelle der Webseite anzeigen, sehen Sie, dass er wie folgt mit der chinesischem Text codiert gerendert wurde;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Erweitert werden, die Zeichen behandelt als sicher vom Encoder Sie würde fügen Sie die folgende Zeile in der `ConfigureServices()` Methode in `startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

In diesem Beispiel erweitert die Sicherungsliste, um die Unicode-Bereich CjkUnifiedIdeographs einzuschließen. Die gerenderte Ausgabe werden jetzt

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Liste sicherer Bereiche werden als Unicode-codeübersichten, nicht die Sprachen angegeben. Die [Unicode-Standard](http://unicode.org/) enthält eine Liste der [code Diagramme](http://www.unicode.org/charts/index.html) können Sie das Diagramm enthält die Zeichen suchen. Jede Encoder, Html, JavaScript und -Url muss separat konfiguriert werden.

> [!NOTE]
> Anpassen der Liste der sicheren wirkt sich nur auf Encoder über DI stammen. Wenn Sie einen Encoder über direkten Zugriff auf `System.Text.Encodings.Web.*Encoder.Default` klicken Sie dann die in der Standardeinstellung lateinischen nur von Listen sicherer Adressen verwendet werden.

## <a name="where-should-encoding-take-place"></a>Wo sollten Codierung Take platzieren?

Die allgemeinen angenommen, dass die Methode besteht darin, dass die Codierung erfolgt zum Zeitpunkt der Ausgabe und codierte Werte nie in einer Datenbank gespeichert werden sollen. Zum Zeitpunkt der Ausgabe Codierung ermöglicht die Verwendung von Daten, z. B. von HTML in einen Zeichenfolgenwert für die Abfrage zu ändern. Außerdem ermöglicht es Ihnen, Ihre Daten einfach zu suchen, ohne Werte vor dem Suchen codieren und bietet die Möglichkeit, alle Änderungen oder bei Fehlerbehebungen versucht, Encoder nutzen.

## <a name="validation-as-an-xss-prevention-technique"></a>Überprüfung als eine XSS-Technik zum Verhindern von Datenverlusten

Überprüfung kann ein nützliches Tool in XSS-Angriffen eingeschränkt werden. Wird nicht für eine einfache numerische Zeichenfolge, enthält nur die Zeichen 0-9 z. B. ein XSS-Angriffen ausgelöst werden. Überprüfung komplizierter Sie HTML in Benutzereingaben - akzeptieren möchten, sollten Analyse von HTML-Eingaben schwierig, wenn nicht sogar unmöglich ist. MarkDown und andere Textformate wäre eine sicherere Option für umfangreiche Eingaben auf. Sie sollten niemals zur Validierung von allein verlassen. Nicht vertrauenswürdige Eingaben vor der Ausgabe immer zu codieren, unabhängig davon, was eine Überprüfung durchgeführt.
