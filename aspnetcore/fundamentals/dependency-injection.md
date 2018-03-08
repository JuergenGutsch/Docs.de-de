---
title: "Abhängigkeitsinjektion in ASP.NET Core"
author: ardalis
description: "Erfahren Sie, wie ASP.NET Core Abhängigkeitsinjektion implementiert und wie Sie es verwenden."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/dependency-injection
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3a6f755d8825d4bd31cad4e459750c549709c6d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="introduction-to-dependency-injection-in-aspnet-core"></a>Einführung in die Abhängigkeitsinjektion in ASP.NET Core

<a name="fundamentals-dependency-injection"></a>

Durch [Steve Smith](https://ardalis.com/) und [Scott Addie](https://scottaddie.com)

ASP.NET Core dient zur Unterstützung und Abhängigkeitsinjektion Nutzen von Grund auf neu einrichten. ASP.NET Core-Anwendungen können integriertes Framework Services, wenn diese in Methoden, die in die Startklasse eingeschleust nutzen und Anwendungsdienste für Injection ebenfalls konfiguriert werden können. Der von ASP.NET Core bereitgestellten Dienste Standardcontainer bietet eine minimale Funktion festgelegt und ist nicht vorgesehen, um andere Container zu ersetzen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-dependency-injection"></a>Was ist die Abhängigkeitsinjektion?

Abhängigkeiteneinschleusung (DI) ist eine Technik zum Erreichen einer losen Kopplung zwischen Objekten und ihren Mitarbeitern oder Abhängigkeiten. Statt direkt instanziieren Projektmitarbeiter oder der Code statische Verweise verwenden werden die Objekte, die eine Klasse benötigt wird, um ihre Aktionen, auf die Klasse in irgendeiner Weise bereitgestellt. In den meisten Fällen werden Klassen deklarieren ihre Abhängigkeiten über ihren Konstruktor, der ihnen ermöglicht, befolgen die [expliziten Abhängigkeiten Prinzip](http://deviq.com/explicit-dependencies-principle/). Dieser Ansatz wird als "Konstruktoreinfügung" bezeichnet.

Bei Klassen mit DI entworfen werden, sind sie Lose gekoppelt, da diese keine direkte, hartcodierte Abhängigkeiten für ihre Mitarbeiter haben. Dies folgt dem [Abhängigkeit Umkehrung Prinzip](http://deviq.com/dependency-inversion-principle/), der gibt an, dass *"hohe Ebene Module darf nicht auf niedriger Ebene Module abhängen;" sollte sowohl Abstraktionen abhängen."* Anstelle von Verweisen auf bestimmte Implementierungen Klassen Abstraktionen anfordern (in der Regel `interfaces`) die werden Ihnen bereitgestellt, wenn die Klasse erstellt wird. Extrahieren von Abhängigkeiten in Schnittstellen und Implementierungen dieser Schnittstellen als Parameter bereitstellen, ist auch ein Beispiel für die [Strategie Entwurfsmuster](http://deviq.com/strategy-design-pattern/).

Wenn ein System mit DI konzipiert ist, ist es mit vielen Klassen anfordern ihre Abhängigkeiten über ihren Konstruktor (oder Eigenschaften) hilfreich, wenn eine Klasse, die für diese Klassen mit ihren zugehörigen Abhängigkeiten erstellen können. Diese Klassen werden als bezeichnet *Container*, genauer gesagt, [Inversion of Control (IoC)](http://deviq.com/inversion-of-control/) Container oder Container (Dependency Injection, DI). Ein Container ist im Wesentlichen eine Factory, die ist verantwortlich für das Bereitstellen von Instanzen von Typen, die von ihm angefordert werden. Wenn ein bestimmten Typs deklariert hat, dass sie Abhängigkeiten und der Container konfiguriert wurde, dass die Abhängigkeit Typen bereitzustellen, erstellt er die Abhängigkeiten im Rahmen der Erstellung der angeforderten Instanz. Auf diese Weise können komplexe Abhängigkeitsdiagramme auf Klassen ohne die Notwendigkeit für alle hartcodierten Objektkonstruktion bereitgestellt werden. Zusätzlich zum Erstellen von Objekten mit deren Abhängigkeiten, Verwalten von Containern in der Regel Objektlebensdauer innerhalb der Anwendung.

ASP.NET Core enthält einen einfachen integrierte Container (dargestellt durch die `IServiceProvider` Schnittstelle), konstruktoreinschleusung standardmäßig unterstützt und ASP.NET macht bestimmte Dienste DI erhältlich. ASP IST. NET Container bezieht sich auf die Typen, die er, als verwaltet *Services*. Im weiteren Verlauf dieses Artikels *Services* verweist auf Typen, die von ASP.NET Core des IoC-Container verwaltet werden. Sie konfigurieren den integrierten Container Dienste in der `ConfigureServices` Methoden in Ihrer Anwendung `Startup` Klasse.

> [!NOTE]
> Smell verfügt über einen umfangreichen Artikel geschrieben, auf [die Umkehrung der Steuerelementcontainer und dem Dependency Injection Muster](https://www.martinfowler.com/articles/injection.html). Microsoft Patterns and Practices verfügt auch über eine hervorragende Beschreibung [Abhängigkeitsinjektion](https://msdn.microsoft.com/library/hh323705.aspx).

> [!NOTE]
> Dieser Artikel behandelt die Abhängigkeitsinjektion anwenden auf alle ASP.NET-Anwendungen. Abhängigkeitsinjektion in MVC-Controller wird in behandelt [Abhängigkeiteneinschleusung und Controllern](../mvc/controllers/dependency-injection.md).

### <a name="constructor-injection-behavior"></a>Konstruktor-Injection-Verhalten

Konstruktoreinfügung erfordert, dass der betreffende Konstruktor *öffentlichen*. Andernfalls löst die app eine `InvalidOperationException`:

> Ein geeigneter Konstruktor für Typ "YourType" konnte nicht gefunden werden. Sicherstellen, dass der Typ konkrete und Dienste für alle Parameter für einen öffentlichen Konstruktor registriert sind.


Konstruktoreinfügung erfordert, nur über einen entsprechenden Konstruktor vorhanden. Konstruktorüberladungen werden unterstützt, jedoch nur eine Überladung kann vorhanden sein, deren Argumente alle durch Abhängigkeitsinjektion erfüllt werden können. Wenn mehr als eine vorhanden ist, löst die app ein `InvalidOperationException`:

> Im Typ "YourType" wurden mehrere Konstruktoren akzeptieren alle angegebenen Argumenttypen gefunden. Es sollte nur einen entsprechenden Konstruktor vorhanden sein.

Konstruktoren können akzeptieren Argumente, die Abhängigkeitsinjektion nicht enthalten sind, jedoch müssen diese Standardwerte unterstützen. Zum Beispiel:

```csharp
// throws InvalidOperationException: Unable to resolve service for type 'System.String'...
public CharactersController(ICharacterRepository characterRepository, string title)
{
    _characterRepository = characterRepository;
    _title = title;
}

// runs without error
public CharactersController(ICharacterRepository characterRepository, string title = "Characters")
{
    _characterRepository = characterRepository;
    _title = title;
}
```

## <a name="using-framework-provided-services"></a>Framework-Dienste verwenden

Die `ConfigureServices` Methode in der `Startup` -Klasse ist verantwortlich für die Dienste, die die Anwendung, einschließlich Plattformfunktionen wie Entity Framework Core und ASP.NET Core MVC verwendet wird definieren. Zu Beginn der `IServiceCollection` bereitgestellt, um `ConfigureServices` hat die folgenden Dienste definiert (je nach [wie der Host konfiguriert wurde](xref:fundamentals/hosting)):

| Diensttyp | Lebensdauer |
| ----- | ------- |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment) | Singleton |
| [Microsoft.Extensions.Logging.ILoggerFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.iloggerfactory) | Singleton |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) | Singleton |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | Vorübergehend |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.ihttpcontextfactory) | Vorübergehend |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) | Singleton |
| [System.Diagnostics.DiagnosticSource](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticsource) | Singleton |
| [System.Diagnostics.DiagnosticListener](https://docs.microsoft.com/dotnet/core/api/system.diagnostics.diagnosticlistener) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartupfilter) | Vorübergehend |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.objectpool.objectpoolprovider) | Singleton |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.iconfigureoptions-1) | Vorübergehend |
| [Microsoft.AspNetCore.Hosting.Server.IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartup](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.istartup) | Singleton |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | Singleton |

Im folgenden finden Sie ein Beispiel zum Hinzufügen von zusätzlichen Dienste für den Container über eine Reihe von Erweiterungsmethoden wie `AddDbContext`, `AddIdentity`, und `AddMvc`.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?highlight=5-6,8-10,12&range=39-56)]

Die Funktionen und ASP.NET verwenden, z. B. MVC-Middleware führen Sie eine Konvention für die Verwendung einer einzelnen hinzufügen*ServiceName* Extension-Methode, um alle von dieser Funktion erforderlichen Dienste zu registrieren.

>[!TIP]
> Sie können anfordern, dass bestimmte Framework bereitgestellten Dienste in `Startup` über ihre Parameterlisten - Methoden finden Sie unter [Anwendungsstart](startup.md) Weitere Details.

## <a name="registering-your-own-services"></a>Registrieren Ihre eigenen Dienste

Sie können eigene Anwendungsdienste wie folgt registrieren. Der erste generischen Typ darstellt, den Typ (in der Regel eine Schnittstelle), der aus dem Container angefordert werden. Die zweiten generischen Typs darstellt, den konkreten Typ, der vom Container instanziiert und verwendet, um solche Anforderungen erfüllt werden.

[!code-csharp[Main](../common/samples/WebApplication1/Startup.cs?range=53-54)]

> [!NOTE]
> Jede `services.Add<ServiceName>` Erweiterungsmethode Services fügt (und möglicherweise konfiguriert). Beispielsweise `services.AddMvc()` fügt die Dienste MVC erfordert. Es wird empfohlen, dass Sie diese Konvention befolgen, platzieren die Erweiterungsmethoden in den `Microsoft.Extensions.DependencyInjection` -Namespace, um Gruppen von Dienst-Registrierungen zu kapseln.

Die `AddTransient` Methode wird verwendet, um die konkrete Services abstrakte Typen zuordnen, die separat für jedes Objekt instanziiert, der dies erfordert. Dies bezeichnet man des Diensts *Lebensdauer*, und zusätzliche Lebensdauer Optionen sind im folgenden beschrieben. Es ist wichtig, eine entsprechende Lebensdauer für jeden der Dienste auszuwählen, die Sie registrieren. Sollte eine neue Instanz des Diensts für die einzelnen Klassen werden bereitgestellt, die diese anfordert? Eine Instanz in einer bestimmten webanforderung verwendet werden soll? Oder sollte für die Lebensdauer der Anwendung eine einzelne Instanz verwendet werden?

Im Beispiel für diesen Artikel ein einfachen Controller, der Zeichennamen aufgerufen zeigt besteht `CharactersController`. Die `Index` Methode zeigt die aktuelle Liste von Zeichen, die in der Anwendung gespeichert worden sind, und initialisiert die Auflistung mit einer Reihe von Zeichen, wenn kein Wert vorhanden ist. Beachten Sie, dass, obwohl diese Anwendung verwendet die Entity Framework Core und die `ApplicationDbContext` für die Beibehaltung, keine Klasse, die im Controller offensichtlich ist. Stattdessen wurde die spezifischen Daten Zugriffsmechanismus hinter einer Schnittstelle abstrahiert `ICharacterRepository`, folgt die [Repositorymusters](http://deviq.com/repository-pattern/). Eine Instanz von `ICharacterRepository` angefordert wird, über den Konstruktor und die dann verwendet wird, Zeichen nach Bedarf den Zugriff auf ein privates Feld zugewiesen wird.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Controllers/CharactersController.cs?highlight=3,5,6,7,8,14,21-27&range=8-36)]

Die `ICharacterRepository` definiert die zwei Methoden, die der Controller zur Bearbeitung muss `Character` Instanzen.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/ICharacterRepository.cs?highlight=8,9)]

Diese Schnittstelle wird wiederum von einem konkreten Typ implementiert `CharacterRepository`, die zur Laufzeit verwendet wird.

> [!NOTE]
> Die Möglichkeit DI wird verwendet, mit der `CharacterRepository` Klasse ist ein allgemeines Modell können Sie für alle Anwendungsdienste, nicht nur in "Repositorys" oder Datenzugriffsklassen befolgen.

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Models/CharacterRepository.cs?highlight=9,11,12,13,14)]

Beachten Sie, dass `CharacterRepository` Anforderungen ein `ApplicationDbContext` in seinem Konstruktor. Es ist nicht ungewöhnlich, dass Abhängigkeitsinjektion in einen verketteten Weise wie folgt, mit jeder angeforderte Abhängigkeit, die wiederum anfordern eigene Abhängigkeiten verwendet werden. Der Container ist verantwortlich für alle Abhängigkeiten im Diagramm zu beheben und Zurückgeben des Diensts vollständig aufgelösten.

> [!NOTE]
> Erstellen des angeforderten Objekts und aller Objekte, die dafür sowie alle Objekte, die erforderlich ist, wird manchmal als bezeichnet ein *Objektdiagramm*. Ebenso die Gesamtheit der Abhängigkeiten, die behoben werden müssen ist in der Regel als bezeichnet eine *Abhängigkeitsstruktur* oder *Abhängigkeitsdiagramm*.

In diesem Fall beide `ICharacterRepository` und im Gegenzug `ApplicationDbContext` muss registriert werden, mit der Dienstecontainer in `ConfigureServices` in `Startup`. `ApplicationDbContext`ist mit dem Aufruf der Erweiterungsmethode konfiguriert `AddDbContext<T>`. Der folgende Code zeigt die Registrierung der `CharacterRepository` Typ.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?highlight=3-5,11&range=16-32)]

Entity Framework Kontexten hinzugefügt werden sollen, die Dienste Container über die `Scoped` Lebensdauer. Dies wird automatisch durchgeführt, wenn die Hilfemethoden verwenden, wie oben gezeigt. Repositorys werden, die von Entity Framework nutzen, sollten dieselbe Lebensdauer verwenden.

>[!WARNING]
> Die wichtigsten Gefahr vorsichtig sein behebt ein `Scoped` -Dienst von einem Singleton. Es ist wahrscheinlich in einem solchen Fall, dass der Dienst bei der Verarbeitung der nachfolgender Anforderungen falschen Status aufweist.

Dienste mit Abhängigkeiten, sollten sie im Container registrieren. Wenn ein Dienst-Konstruktor wie z. B. ein primitiver erfordert eine `string`, dies kann eingegeben werden, mithilfe von [Konfiguration](xref:fundamentals/configuration/index) und [Optionen Muster](xref:fundamentals/configuration/options).

## <a name="service-lifetimes-and-registration-options"></a>Dienst-Lebensdauer und Registrierungsoptionen

ASP.NET-Dienste können mit den folgenden Lebensdauer konfiguriert werden:

**Transient**

Vorübergehender Lebensdauerdienste werden jedes Mal erstellt, die sie angefordert haben. Diese Lebensdauer funktioniert am besten geeignet für einfache, zustandslose Dienste.

**Im Bereich**

Bereichsbezogene Lebensdauerdienste werden einmal pro Anforderung erstellt.

**Singleton**

Singleton-Lebensdauerdienste sind zum ersten Mal, die sie angefordert haben erstellt (oder wenn `ConfigureServices` ausgeführt wird, wenn Sie angeben, dass es eine Instanz) und verwenden Sie dann jede nachfolgende Anforderung wird dieselbe Instanz. Wenn Ihre Anwendung Laufzeitverhalten von Singleton erfordert, wird ermöglicht den Container zum Verwalten der Lebensdauer des Diensts empfohlen, statt Implementieren des Entwurfsmusters Singleton und Objektlebensdauer in der Klasse selbst verwalten.

Dienste können mit dem Container auf verschiedene Arten registriert werden. Wir haben bereits gesehen, wie eine dienstimplementierung mit einem angegebenen Typ registrieren, indem der konkrete Typ verwenden. Darüber hinaus kann eine Factory angegeben werden, die zum Erstellen der Instanz bei Bedarf verwendet werden soll. Der dritte Ansatz darin ist die Instanz des Typs zu verwenden, direkt angeben, in dem Fall Container versucht nie zum Erstellen einer Instanz (noch dispose der Instanz wird).

Um den Unterschied zwischen diesen Optionen Lebensdauer und Registrierung zu veranschaulichen, betrachten Sie eine einfache Oberfläche, die eine oder mehrere Aufgaben als darstellt ein *Vorgang* mit einem eindeutigen Bezeichner `OperationId`. Je nachdem, wie wir die Lebensdauer für diesen Dienst konfigurieren gebe der Container die gleichen oder unterschiedliche Instanzen des Diensts, der die anfordernde-Klasse. Um unmissverständlich welche Lebensdauer angefordert wird, erstellen wir einen Typ pro Lifetime-Option:

[!code-csharp[Main](../fundamentals/dependency-injection/sample/DependencyInjectionSample/Interfaces/IOperation.cs?highlight=5-8)]

Wir implementieren diese Schnittstellen, die über eine einzelne Klasse `Operation`, nimmt ein `Guid` in seinem Konstruktor oder verwendet eine neue `Guid` Wenn kein.

Im nächsten Schritt in `ConfigureServices`, jeden Typ des Containers entsprechend benannten Lebensdauer hinzugefügt:

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Startup.cs?range=26-32)]

Beachten Sie, dass die `IOperationSingletonInstance` Service verwendet eine bestimmte Instanz mit einer bekannten ID `Guid.Empty` so klar sein wird, wenn dieser Typ verwendet wird (eine Guid werden ausschließlich Nullen enthält). Wir haben auch registriert eine `OperationService` , abhängig ist, auf jedem der anderen `Operation` Argumenttypen, das, damit er eindeutig innerhalb einer Anforderung kann, ob dieser Dienst die gleiche Instanz wie der Controller oder eine neue, für jeden Vorgangstyp abruft. Alles, was dieses Diensts wird die abhängigen Elemente als Eigenschaften verfügbar machen, damit sie in der Ansicht angezeigt werden können.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Services/OperationService.cs)]

Zur Veranschaulichung der Objektlebensdauer innerhalb und zwischen separaten einzelne Anforderungen an die Anwendung das Beispiel umfasst ein `OperationsController` , die Anforderungen jeder Art von `IOperation` Typ sowie einen `OperationService`. Die `Index` Aktion werden dann zeigt alle des Controllers und des Diensts `OperationId` Werte.

[!code-csharp[Main](dependency-injection/sample/DependencyInjectionSample/Controllers/OperationsController.cs)]

Jetzt sind zwei separate Anforderungen an diese Controlleraktion vorgenommen:

![Die Operations-Ansicht der Dependency Injection-Beispiel-Webanwendung in Microsoft Edge mit der Vorgangs-ID-Werten (GUID) für vorübergehend, ausgelegte, Singleton, Instanz-Controller und Dienstvorgänge Vorgang, bei der ersten Anforderung ausgeführt werden soll.](dependency-injection/_static/lifetimes_request1.png)

![Die Vorgänge anzeigen, in dem die Vorgangs-ID-Werte für eine zweite Anforderung.](dependency-injection/_static/lifetimes_request2.png)

Beachten Sie die von der `OperationId` verändern Sie die Werte in einer Anforderung und zwischen den Anforderungen.

* *Vorübergehender* Objekte sind immer unterschiedliche; eine neue Instanz wird an jedem Controller und jeden Dienst bereitgestellt.

* *Im Bereich einer* -Objekte innerhalb einer Anforderung, jedoch nicht für andere Anforderungen identisch sind

* *Singleton* -Objekte für jedes Objekt und jede Anforderung identisch sind (unabhängig davon, ob eine Instanz, im bereitgestellt wird `ConfigureServices`)

## <a name="request-services"></a>Dienste anfordern

Fordern Sie die Dienste innerhalb einer ASP.NET-Anwendung zur Verfügung, von `HttpContext` werden verfügbar gemacht, durch die `RequestServices` Auflistung.

![Kontextabhängige HttpContext Anforderung Services Intellisense-Dialogfeld angezeigt, die besagt, dass Anforderung-Dienste Ruft ab oder legt ihn fest, die Zugriff auf die Anforderung Dienstcontainer bietet IServiceProvider.](dependency-injection/_static/request-services.png)

Anforderung Dienste darstellen, die Dienste, die Sie konfigurieren und als Teil Ihrer Anwendung anfordern. Wenn die Objekte Abhängigkeiten angeben, werden diese Typen in erfüllt `RequestServices`, nicht `ApplicationServices`.

Sie sollten nicht im Allgemeinen verwenden diese Eigenschaften direkt, stattdessen den Typen Ihrer Klassen anfordern, die Sie über Ihre-Klassenkonstruktor benötigen ausgehandelt, und lassen das Framework diese Abhängigkeiten einfügen. Dadurch wird die Klassen, die leichter zu testen sind (finden Sie unter [Test](../testing/index.md)) und sind lose gekoppelt.

> [!NOTE]
> Es vorziehen, Anfordern von Abhängigkeiten als Konstruktorparameter für den Zugriff auf die `RequestServices` Auflistung.

## <a name="designing-your-services-for-dependency-injection"></a>Entwerfen Ihre Dienste für Zielabhängigkeit

Entwerfen Sie Ihre Dienste Abhängigkeitsinjektion verwenden, um ihre Mitarbeiter zu erhalten. Dies bedeutet, dass die Verwendung von zustandsbehafteten statische Methodenaufrufe vermeiden (die in anderen Code genannt führen [Fensterdekorationen](http://deviq.com/static-cling/)) und die direkte Instanziierung von abhängige Klassen in Ihre Dienste. Es kann nützlich sein, den Ausdruck zu merken [Gerätekontenverzeichnisses neue ist](https://ardalis.com/new-is-glue), bei der Auswahl, ob Sie einen Typ zu instanziieren oder es über die Abhängigkeitsinjektion angefordert werden. Anhand der [EINFARBIG Prinzipien der Object Oriented Design](http://deviq.com/solid/), Ihren Klassen werden natürlich tendenziell klein, gut ausgearbeitete und einfache Weise getestet werden.

Was geschieht, wenn Sie feststellen, dass Ihre Klassen haben meist Weise zu viele Abhängigkeiten eingefügt wird? Dies ist im Allgemeinen ein Zeichen dafür, dass Ihre Klasse versucht, zu viele Vorgänge und wird wahrscheinlich SRP - Verletzung der [Prinzip einzigen Verantwortung](http://deviq.com/single-responsibility-principle/). Angezeigt, wenn Sie die Klasse Umgestalten können, indem Sie einige der entsprechenden Aufgaben in eine neue Klasse verschieben. Beachten Sie, dass Ihre `Controller` Klassen sollten mit Schwerpunkt auf UI bedenken, damit Unternehmen und Zugriff Implementierungsdetails in Klassen, die diese beibehalten werden soll [trennen Bedenken](http://deviq.com/separation-of-concerns/).

Im Hinblick auf den Datenzugriff insbesondere können Sie Einfügen der `DbContext` in Ihren Domänencontrollern (vorausgesetzt, Sie auf den Dienstecontainer im EF hinzugefügt haben `ConfigureServices`). Einige Entwickler eine Repository-Schnittstelle, um die Datenbank verwenden möchten, anstatt Räumen der `DbContext` direkt. Über eine Schnittstelle zum Kapseln von Daten kann Zugriffslogik an einem Ort wie vielen Stellen minimieren, wird geändert, wenn die Datenbank geändert werden müssen.

### <a name="disposing-of-services"></a>Freigeben von Diensten

Ruft der Container `Dispose` für `IDisposable` Typen erstellt. Jedoch wenn Sie eine Instanz der Container selbst hinzugefügt haben, wird er nicht gelöscht.

Beispiel:

```csharp
// Services implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}


public void ConfigureServices(IServiceCollection services)
{
    // container will create the instance(s) of these types and will dispose them
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // container didn't create instance so it will NOT dispose it
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

> [!NOTE]
> In Version 1.0 der Container Dispose für aufgerufen *alle* `IDisposable` Objekte, einschließlich der nicht erstellen.

## <a name="replacing-the-default-services-container"></a>Ersetzen der Standardcontainer für Dienste

Der integrierte Dienstecontainer soll die grundlegenden Anforderungen von Framework und die meisten Consumer-Anwendungen, die darauf aufgebauten dienen. Allerdings können Entwickler den integrierten Container durch ihre bevorzugte Container ersetzen. Die `ConfigureServices` in der Regel Methodenrückgabe `void`, aber wenn die Signatur geändert wird, um zurückzukehren `IServiceProvider`, ein anderen Container konfiguriert und zurückgegeben werden kann. Es sind viele IOC-Container für .NET verfügbar. In diesem Beispiel wird die [Autofac](https://autofac.org/) Paket verwendet wird.

Installieren Sie zunächst die entsprechenden Container Pakete:

* `Autofac`
* `Autofac.Extensions.DependencyInjection`

Als Nächstes konfigurieren Sie den Container in `ConfigureServices` und Zurückgeben einer `IServiceProvider`:

```csharp
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add other framework services

    // Add Autofac
    var containerBuilder = new ContainerBuilder();
    containerBuilder.RegisterModule<DefaultModule>();
    containerBuilder.Populate(services);
    var container = containerBuilder.Build();
    return new AutofacServiceProvider(container);
}
```

> [!NOTE]
> Wenn Sie einen Drittanbieter-DI-Container verwenden, müssen Sie ändern `ConfigureServices` sodass Rückgabe `IServiceProvider` anstelle von `void`.

Konfigurieren Sie abschließend Autofac wie gewohnt in `DefaultModule`:

```csharp
public class DefaultModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
    }
}
```

Zur Laufzeit wird Autofac verwendet werden, zum Auflösen von Typen und zum Einfügen von Abhängigkeiten. [Weitere Informationen zur Verwendung von Autofac und ASP.NET Core](http://docs.autofac.org/en/latest/integration/aspnetcore.html).

### <a name="thread-safety"></a>Threadsicherheit

Singleton-Dienste müssen threadsicher sein. Wenn ein Singleton-Dienst eine vorübergehende Dienst abhängt, müssen der vorübergehende Dienst möglicherweise auch threadsicher je nachdem, wie diese verwendet werden, indem Sie den Singleton sein.

## <a name="recommendations"></a>Empfehlungen

Bei der Arbeit mit Abhängigkeitsinjektion Bedenken Sie die folgenden Empfehlungen:

* DI wird für Objekte, die komplexe Abhängigkeiten aufweisen. Domänencontroller, Dienste, Adapter und Repositorys sind Beispiele für Objekte, die zum DI hinzugefügt werden.

* Vermeiden Sie das Speichern von Daten und Konfigurationsinformationen direkt in DI. Beispielsweise darf keine Einkaufswagen eines Benutzers in der Regel auf den Container hinzugefügt werden. Konfiguration verwenden, sollten die [Optionen Muster](xref:fundamentals/configuration/options). Auf ähnliche Weise vermeiden Sie "Daten Inhaber"-Objekte, die nur für den Zugriff auf ein anderes Objekt vorhanden. Es ist besser, Anfordern von dem tatsächlichen Element nach Möglichkeit über DI, erforderlich.

* Vermeiden Sie statische Zugriff auf Dienste.

* Vermeiden Sie die dienstidentifizierung in Ihrem Anwendungscode.

* Vermeiden Sie statische Zugriff auf `HttpContext`.

> [!NOTE]
> Wie alle Sätze von Empfehlungen kann es Situationen, in denen ignorieren einer erforderlich ist. Wir haben Ausnahmen selten--größtenteils sehr spezielle Fälle im Framework selbst gefunden werden.

Beachten Sie, dass Dependency Injection-Angriff wird ein *alternative* , Zugriffsmuster statischen/global-Objekt. Kann nicht auf die Vorzüge des DI Wenn Sie es mit Zugriff auf statische Objekte kombinieren.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Application Startup (Starten von Anwendungen)](startup.md)

* [Testen](../testing/index.md)

* [Schreiben von sauberen Code in ASP.NET Core mit Abhängigkeiteneinschleusung (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)

* [Container-Managed Anwendungsentwurf, Einführung:, In dem Container gehören unterstützt?](https://blogs.msdn.microsoft.com/nblumhardt/2008/12/26/container-managed-application-design-prelude-where-does-the-container-belong/)

* [Expliziten Abhängigkeiten Prinzip](http://deviq.com/explicit-dependencies-principle/)

* [Die Umkehrung der Steuerelementcontainer und dem Dependency Injection Muster](https://www.martinfowler.com/articles/injection.html) (Fowler)
