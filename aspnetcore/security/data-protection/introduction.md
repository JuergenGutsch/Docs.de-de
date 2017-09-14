---
title: "Einführung in Data Protection"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4542cd37-b47c-454c-be19-d1b5810d67fe
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/introduction
ms.openlocfilehash: b7391fffd5d512c01af5d709755a925f739b59ba
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-data-protection"></a><span data-ttu-id="7a2a7-103">Einführung in Data Protection</span><span class="sxs-lookup"><span data-stu-id="7a2a7-103">Introduction to Data Protection</span></span>

<span data-ttu-id="7a2a7-104">Webanwendungen müssen häufig sicherheitsrelevante Daten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-104">Web applications often need to store security-sensitive data.</span></span> <span data-ttu-id="7a2a7-105">Windows DPAPI für desktopanwendungen bietet, aber dies ist nicht für Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-105">Windows provides DPAPI for desktop applications but this is unsuitable for web applications.</span></span> <span data-ttu-id="7a2a7-106">ASP.NET Core Data Protection Stapel bieten eine einfache, einfach zu verwendende cryptographic API, die ein Entwickler zum Schützen von Daten, einschließlich der schlüsselverwaltung und Drehung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-106">The ASP.NET Core data protection stack provide a simple, easy to use cryptographic API a developer can use to protect data, including key management and rotation.</span></span>

<span data-ttu-id="7a2a7-107">ASP.NET Core Data Protection Stapel dient als der langfristige Ersatz für dienen der <machineKey> Element in ASP.NET 1.x - 4.x.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-107">The ASP.NET Core data protection stack is designed to serve as the long-term replacement for the <machineKey> element in ASP.NET 1.x - 4.x.</span></span> <span data-ttu-id="7a2a7-108">Es wurde für viele der Mängel bei der ALTER cryptographic Stapel und gleichzeitig eine Out-of-Box-Lösung für die meisten modernen Anwendungen auftretenden sind Anwendungsfälle behandeln ausgelegt.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-108">It was designed to address many of the shortcomings of the old cryptographic stack while providing an out-of-the-box solution for the majority of use cases modern applications are likely to encounter.</span></span>

## <a name="problem-statement"></a><span data-ttu-id="7a2a7-109">Problemerläuterung</span><span class="sxs-lookup"><span data-stu-id="7a2a7-109">Problem statement</span></span>

<span data-ttu-id="7a2a7-110">Das allgemeine Problem-Anweisung kann in einem einzigen Satz kurz angegeben werden: Ich benötige um vertrauenswürdige Informationen für den späteren Abruf persistent zu speichern, aber ich die Dauerhaftigkeit nicht vertrauen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-110">The overall problem statement can be succinctly stated in a single sentence: I need to persist trusted information for later retrieval, but I do not trust the persistence mechanism.</span></span> <span data-ttu-id="7a2a7-111">Web ausgedrückt könnte dies geschrieben sein, wie "Round-Trip vertrauenswürdigen Zustand über einen nicht vertrauenswürdigen Client muss ich."</span><span class="sxs-lookup"><span data-stu-id="7a2a7-111">In web terms, this might be written as "I need to round-trip trusted state via an untrusted client."</span></span>

<span data-ttu-id="7a2a7-112">Das kanonische Beispiel hierfür ist ein Authentifizierungscookie bzw. Bearer Sicherheitstoken.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-112">The canonical example of this is an authentication cookie or bearer token.</span></span> <span data-ttu-id="7a2a7-113">Der Server generiert eine "Ich habe Groot und Xyz berechtigt" token und übergibt diesen an dem Client.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-113">The server generates an "I am Groot and have xyz permissions" token and hands it to the client.</span></span> <span data-ttu-id="7a2a7-114">Irgendwann wird der Client dieses Token an den Server vorhanden, aber der Server benötigt eine Art von Gewissheit, dass der Client das Token gefälscht wurde nicht.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-114">At some future date the client will present that token back to the server, but the server needs some kind of assurance that the client hasn't forged the token.</span></span> <span data-ttu-id="7a2a7-115">Daher die erste Anforderung: Echtheit (auch als)</span><span class="sxs-lookup"><span data-stu-id="7a2a7-115">Thus the first requirement: authenticity (a.k.a.</span></span> <span data-ttu-id="7a2a7-116">Integrität, vor unbefugtem Zugriff-Strategien).</span><span class="sxs-lookup"><span data-stu-id="7a2a7-116">integrity, tamper-proofing).</span></span>

<span data-ttu-id="7a2a7-117">Da der persistente Zustand vom Server als vertrauenswürdig eingestuft wird, erwarten wir, dass dieser Status möglicherweise Informationen enthalten, die für das Betriebssystem spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-117">Since the persisted state is trusted by the server, we anticipate that this state might contain information that is specific to the operating environment.</span></span> <span data-ttu-id="7a2a7-118">Dies kann in Form von einem Dateipfad, eine Berechtigung, ein Handle oder andere indirekten Verweis oder andere Datenfragmente serverspezifische sein.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-118">This could be in the form of a file path, a permission, a handle or other indirect reference, or some other piece of server-specific data.</span></span> <span data-ttu-id="7a2a7-119">Diese Informationen sollten im Allgemeinen nicht auf ein nicht vertrauenswürdiger Client offengelegt werden.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-119">Such information should generally not be disclosed to an untrusted client.</span></span> <span data-ttu-id="7a2a7-120">Daher die zweite Anforderung: Vertraulichkeit.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-120">Thus the second requirement: confidentiality.</span></span>

<span data-ttu-id="7a2a7-121">Schließlich, da moderne Anwendungen componentized sind, was wir gesehen haben ist, dass Einzelkomponenten dieses System unabhängig von der anderen Komponenten im System nutzen möchten.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-121">Finally, since modern applications are componentized, what we've seen is that individual components will want to take advantage of this system without regard to other components in the system.</span></span> <span data-ttu-id="7a2a7-122">Für die Instanz, wenn eine Bearer-token-Komponente auf diesem Stapel verwendet wird, sollte es ohne Störung möglich sind, aus einem Anti-CSRF-Mechanismus arbeiten, die u. u. auch den gleichen Stapel verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-122">For instance, if a bearer token component is using this stack, it should operate without interference from an anti-CSRF mechanism that might also be using the same stack.</span></span> <span data-ttu-id="7a2a7-123">Daher die letzte Anforderung: Isolation.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-123">Thus the final requirement: isolation.</span></span>

<span data-ttu-id="7a2a7-124">Es können weitere Einschränkungen bereitstellen, um den Überwachungsumfang unsere Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-124">We can provide further constraints in order to narrow the scope of our requirements.</span></span> <span data-ttu-id="7a2a7-125">Es wird vorausgesetzt, dass alle Dienste, die innerhalb der Kryptografiesystem gleichermaßen vertraut sind und, dass die Daten nicht generiert oder außerhalb der Dienste unter einem unserer direkte Kontrolle genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-125">We assume that all services operating within the cryptosystem are equally trusted and that the data does not need to be generated or consumed outside of the services under our direct control.</span></span> <span data-ttu-id="7a2a7-126">Darüber hinaus müssen Vorgänge sind so schnell wie möglich, da für jede Anforderung an den Webdienst die Kryptografiesystem ein- oder mehrmals durchlaufen kann.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-126">Furthermore, we require that operations are as fast as possible since each request to the web service might go through the cryptosystem one or more times.</span></span> <span data-ttu-id="7a2a7-127">Auf diese Weise symmetrische Kryptografie ideal für unser Szenario, und wir können asymmetrische Kryptografie Rabatt, bis etwa eine Zeit, die es benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-127">This makes symmetric cryptography ideal for our scenario, and we can discount asymmetric cryptography until such a time that it is needed.</span></span>

## <a name="design-philosophy"></a><span data-ttu-id="7a2a7-128">Entwurfsphilosophie von</span><span class="sxs-lookup"><span data-stu-id="7a2a7-128">Design philosophy</span></span>

<span data-ttu-id="7a2a7-129">Wir Erkennen von Problemen mit dem vorhandenen Stapel gestartet.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-129">We started by identifying problems with the existing stack.</span></span> <span data-ttu-id="7a2a7-130">Sobald wir hatten, wir die Landscape vorhandenen Lösungen befragt und führten zu dem Schluss, dass keine vorhandenen Lösung sehr Funktionen zugewiesen waren, die wir gesucht.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-130">Once we had that, we surveyed the landscape of existing solutions and concluded that no existing solution quite had the capabilities we sought.</span></span> <span data-ttu-id="7a2a7-131">Wir Engineering klicken Sie dann eine Lösung basierend auf mehrere grundlegende Prinzipien.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-131">We then engineered a solution based on several guiding principles.</span></span>

* <span data-ttu-id="7a2a7-132">Das System sollte Vereinfachung der Konfiguration bieten.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-132">The system should offer simplicity of configuration.</span></span> <span data-ttu-id="7a2a7-133">Im Idealfall würde das System Konfigurationsfreie und Entwickler konnte den Boden ausgeführt erreicht.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-133">Ideally the system would be zero-configuration and developers could hit the ground running.</span></span> <span data-ttu-id="7a2a7-134">In Situationen, in denen Entwickler ein bestimmtes Aspekts (z. B. das Key-Repository) konfigurieren müssen, sollten abgewogen werden diese bestimmten Konfigurationen einfacher zu machen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-134">In situations where developers need to configure a specific aspect (such as the key repository), consideration should be given to making those specific configurations simple.</span></span>

* <span data-ttu-id="7a2a7-135">Bieten Sie eine einfache kundenorientierten-API.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-135">Offer a simple consumer-facing API.</span></span> <span data-ttu-id="7a2a7-136">Die APIs sollte einfach der richtigen Verwendung und schwierig zu verwenden, nicht ordnungsgemäß.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-136">The APIs should be easy to use correctly and difficult to use incorrectly.</span></span>

* <span data-ttu-id="7a2a7-137">Entwickler sollten nicht schlüsselverwaltung Prinzipien lernen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-137">Developers should not learn key management principles.</span></span> <span data-ttu-id="7a2a7-138">Das System sollte die Auswahl des Algorithmus und Schlüssel Lebensdauer im Auftrag des Entwicklers behandeln.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-138">The system should handle algorithm selection and key lifetime on the developer's behalf.</span></span> <span data-ttu-id="7a2a7-139">Im Idealfall sollte der Entwickler Zugriff auf die unformatierten Schlüsselmaterial nie sogar sein.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-139">Ideally the developer should never even have access to the raw key material.</span></span>

* <span data-ttu-id="7a2a7-140">Im Ruhezustand nach Möglichkeit sollten Schlüssel geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-140">Keys should be protected at rest when possible.</span></span> <span data-ttu-id="7a2a7-141">Das System sollte eine entsprechende Standardeinstellung Schutzmechanismus herausfinden und automatisch angewendet.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-141">The system should figure out an appropriate default protection mechanism and apply it automatically.</span></span>

<span data-ttu-id="7a2a7-142">Diese Grundsätze Bedenken wir eine einfache entwickelt [einfach zu verwendende](using-data-protection.md) Data Protection Stapel.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-142">With these principles in mind we developed a simple, [easy to use](using-data-protection.md) data protection stack.</span></span>

<span data-ttu-id="7a2a7-143">Die ASP.NET Core Datenschutz-APIs sind nicht in erster Linie für unbestimmte Persistenz des vertraulichen Nutzlasten gedacht.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-143">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="7a2a7-144">Andere Technologien wie [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) und [Azure Rights Management](https://docs.microsoft.com/rights-management/) eignen sich besser auf das Szenario der unbegrenzten Speicher und Verwaltungsfunktionen für entsprechend starken haben.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-144">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](https://docs.microsoft.com/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="7a2a7-145">Dies bedeutet, dass keine verbietet einen Entwickler mithilfe der ASP.NET Core Datenschutz-APIs für den langfristigen Schutz von vertraulichen Daten.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-145">That said, there is nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span>

## <a name="audience"></a><span data-ttu-id="7a2a7-146">Zielgruppe</span><span class="sxs-lookup"><span data-stu-id="7a2a7-146">Audience</span></span>

<span data-ttu-id="7a2a7-147">Die Datenschutzsystem ist in fünf main Pakete unterteilt.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-147">The data protection system is divided into five main packages.</span></span> <span data-ttu-id="7a2a7-148">Verschiedene Aspekte des diese APIs Ziel drei wichtigsten Zielgruppen;</span><span class="sxs-lookup"><span data-stu-id="7a2a7-148">Various aspects of these APIs target three main audiences;</span></span>

1. <span data-ttu-id="7a2a7-149">Die [Consumer APIs Übersicht](consumer-apis/overview.md) Anwendung und Framework-Entwickler ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-149">The [Consumer APIs Overview](consumer-apis/overview.md) target application and framework developers.</span></span>

   <span data-ttu-id="7a2a7-150">"Ich möchte nicht Weitere Informationen zur Funktionsweise von des Stapels oder darüber, wie es konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-150">"I don't want to learn about how the stack operates or about how it is configured.</span></span> <span data-ttu-id="7a2a7-151">Ich möchte einfach einigen Vorgängen als einfache Weise mit hoher Wahrscheinlichkeit erfolgreich mit den APIs wie möglich ausführen."</span><span class="sxs-lookup"><span data-stu-id="7a2a7-151">I simply want to perform some operation in as simple a manner as possible with high probability of using the APIs successfully."</span></span>

2. <span data-ttu-id="7a2a7-152">Die [Konfigurations-APIs](configuration/overview.md) ausgelegt, Entwickler und Systemadministratoren.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-152">The [configuration APIs](configuration/overview.md) target application developers and system administrators.</span></span>

   <span data-ttu-id="7a2a7-153">"Ich möchte die Datenschutzsystem Teilen Sie, dass meine Umgebung Nichtstandard-Pfade oder Einstellungen erfordert."</span><span class="sxs-lookup"><span data-stu-id="7a2a7-153">"I need to tell the data protection system that my environment requires non-default paths or settings."</span></span>

3. <span data-ttu-id="7a2a7-154">Die Erweiterbarkeit APIs Ziel Entwickler um die benutzerdefinierte Richtlinie implementieren.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-154">The extensibility APIs target developers in charge of implementing custom policy.</span></span> <span data-ttu-id="7a2a7-155">Verwendung dieser APIs würden auf seltenen Fällen Anwendung beschränkt und Sicherheit bewusst Entwickler-geräteumleitung.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-155">Usage of these APIs would be limited to rare situations and experienced, security aware developers.</span></span>

   <span data-ttu-id="7a2a7-156">"Ich möchte eine gesamte Komponente innerhalb des Systems zu ersetzen, da ich wirklich eindeutige verhaltensanforderungen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-156">"I need to replace an entire component within the system because I have truly unique behavioral requirements.</span></span> <span data-ttu-id="7a2a7-157">Ich bin bereit, erfahren Teile uncommonly verwendet, der die API-Oberfläche, um ein Plug-in zu erstellen, die meine Anforderungen erfüllt."</span><span class="sxs-lookup"><span data-stu-id="7a2a7-157">I am willing to learn uncommonly-used parts of the API surface in order to build a plugin that fulfills my requirements."</span></span>

## <a name="package-layout"></a><span data-ttu-id="7a2a7-158">Paketlayouts</span><span class="sxs-lookup"><span data-stu-id="7a2a7-158">Package Layout</span></span>

<span data-ttu-id="7a2a7-159">Der Data Protection Stapel besteht aus fünf Paketen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-159">The data protection stack consists of five packages.</span></span>

* <span data-ttu-id="7a2a7-160">Microsoft.AspNetCore.DataProtection.Abstractions enthält die grundlegenden IDataProtectionProvider und IDataProtector-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-160">Microsoft.AspNetCore.DataProtection.Abstractions contains the basic IDataProtectionProvider and IDataProtector interfaces.</span></span> <span data-ttu-id="7a2a7-161">Es enthält auch nützliche Erweiterungsmethoden, die beim Arbeiten mit diesen Typen (z. B. Überladungen der IDataProtector.Protect) unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-161">It also contains useful extension methods that can assist working with these types (e.g., overloads of IDataProtector.Protect).</span></span> <span data-ttu-id="7a2a7-162">Finden Sie unter der Consumer Schnittstellen-Abschnitt, um weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-162">See the consumer interfaces section for more information.</span></span> <span data-ttu-id="7a2a7-163">Wenn eine andere Person verantwortlich ist für die Instanziierung der Datenschutzsystem, und Sie einfach die APIs nutzen, sollten Sie Verweis Microsoft.AspNetCore.DataProtection.Abstractions.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-163">If somebody else is responsible for instantiating the data protection system and you are simply consuming the APIs, you'll want to reference Microsoft.AspNetCore.DataProtection.Abstractions.</span></span>

* <span data-ttu-id="7a2a7-164">Microsoft.AspNetCore.DataProtection enthält die basisimplementierung von Datenschutzsystem, einschließlich der wichtigsten kryptografischen Vorgänge, schlüsselverwaltung, Konfiguration und Erweiterbarkeit.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-164">Microsoft.AspNetCore.DataProtection contains the core implementation of the data protection system, including the core cryptographic operations, key management, configuration, and extensibility.</span></span> <span data-ttu-id="7a2a7-165">Wenn Sie zum Instanziieren der Datenschutzsystem verantwortlich sind (z. B. hinzufügen, eine IServiceCollection) oder ändern, oder erweitern das Verhalten, müssen Sie Verweis Microsoft.AspNetCore.DataProtection möchten.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-165">If you're responsible for instantiating the data protection system (e.g., adding it to an IServiceCollection) or modifying or extending its behavior, you'll want to reference Microsoft.AspNetCore.DataProtection.</span></span>

* <span data-ttu-id="7a2a7-166">Microsoft.AspNetCore.DataProtection.Extensions enthält zusätzliche APIs, die Entwickler möglicherweise interessant sein, aber die nicht in das Hauptpaket gehören.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-166">Microsoft.AspNetCore.DataProtection.Extensions contains additional APIs which developers might find useful but which don't belong in the core package.</span></span> <span data-ttu-id="7a2a7-167">Dieses Paket enthält z. B. eine einfache "Instanziieren des Systems, zeigen Sie auf einem bestimmten-Speicherverzeichnis keine Dependency Injection-Setup"-API (Weitere Informationen).</span><span class="sxs-lookup"><span data-stu-id="7a2a7-167">For instance, this package contains a simple "instantiate the system pointing at a specific key storage directory with no dependency injection setup" API (more info).</span></span> <span data-ttu-id="7a2a7-168">Es enthält auch die Erweiterungsmethoden für die Lebensdauer des geschützten Nutzlasten (Weitere Informationen) beschränken.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-168">It also contains extension methods for limiting the lifetime of protected payloads (more info).</span></span>

* <span data-ttu-id="7a2a7-169">Microsoft.AspNetCore.DataProtection.SystemWeb installiert werden kann, in eine vorhandene ASP.NET 4.x-Anwendung zum Umleiten der <machineKey> Vorgänge stattdessen die neuen Data Protection Stapel verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-169">Microsoft.AspNetCore.DataProtection.SystemWeb can be installed into an existing ASP.NET 4.x application to redirect its <machineKey> operations to instead use the new data protection stack.</span></span> <span data-ttu-id="7a2a7-170">Finden Sie unter [Kompatibilität](compatibility/replacing-machinekey.md#compatibility-replacing-machinekey) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-170">See [compatibility](compatibility/replacing-machinekey.md#compatibility-replacing-machinekey) for more information.</span></span>

* <span data-ttu-id="7a2a7-171">Microsoft.AspNetCore.Cryptography.KeyDerivation stellt eine Implementierung von der Routine Kennworthashs PBKDF2 und kann von Systemen, bei denen Benutzerkennwörter sicher behandeln müssen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-171">Microsoft.AspNetCore.Cryptography.KeyDerivation provides an implementation of the PBKDF2 password hashing routine and can be used by systems which need to handle user passwords securely.</span></span> <span data-ttu-id="7a2a7-172">Finden Sie unter [Kennworthashs](consumer-apis/password-hashing.md) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="7a2a7-172">See [Password Hashing](consumer-apis/password-hashing.md) for more information.</span></span>