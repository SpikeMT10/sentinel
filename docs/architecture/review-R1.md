# Sentinel – závěrečná architektonická revize 0.9.1

> Snímek projektové dokumentace k 26. 11. 2026. Zdroj: https://chatgpt.com/space/page_68d5eab9a4948191bf3a848bff4bb8bc. Změny architektury vyžadují evidovanou revizi a souhlas vlastníka.

**Revize R1 | 26. 11. 2026 | Dokumentační revize všech 14 modulů | Výsledek: podmíněně připraveno k vývoji v izolované laboratoři.**

Nejde o penetrační test, audit běžícího serveru, ověření dostupnosti účtů ani potvrzení bezpečnosti software. Posouzeny aktuální stránky uvedené níže, jejich schválené záměry, konflikty a realizovatelnost. Na serverech nebyly provedeny změny. Schválené komponenty zachovat; návrhy zpřesnění níže předložit ke schválení v MASTER 1.0 RC1. Původní dokumentace se touto revizí nepřepisuje.

## 1. Výsledek

Architektura má kvalitní principy: oddělení AI a privilegovaných funkcí, default-deny, nezávislá ochrana a obnova, nezávislé autorizace, opakovatelnost, verzovaná distribuce a izolace jednotlivých hostů. Není důvod měnit základní koncepci ani přidávat další bezpečnostní aplikace. Největší rizika jsou implementační: skryté cesty k privilegiím, nedostatečně vymahatelné schvalování, společné body selhání, špatně zabezpečené aktualizace a příliš široký první release. Doporučení: zahájit přípravu vývojové infrastruktury a R0/R1 laboratoře, nikoli zapínat R2/R3 či nasazovat produkci.

## 2. Revize všech modulů

| Modul a odkaz | Verze | Výsledek revize |
|---|---|---|
| [SENT-001 – Bezpečnostní architektura](https://chatgpt.com/space/page_2426657191548191ad13dfce33fed6d1) | 0.9.1 | Základ dobrý. Obecná R2 formulace pro aktualizace a konfiguraci musí ustoupit přesnějším pravidlům R3. Chybí sjednocený minimální bezpečnostní profil Debianu a přehled odpovědností. F01, F04, F05. |
| [SENT-002 – Účty a oprávnění](https://chatgpt.com/space/page_7cc8eb368ccc81919f2626c71034e23c) | 1.1 | Vyjasnit, že R0/R1 jsou read-only vůči produkční konfiguraci, nikoli absolutně bez zápisů; Agent smí měnit vlastní omezená data. Oddělit všechny služební identity a ověřit, že žádná agentem zapisovatelná cesta není použita privilegovaným procesem. F02, F03. |
| [SENT-003 – Modely a orchestrace](https://chatgpt.com/space/page_19f1948d596c8191a70b2e145e7e34e4) | 1.0 | Role primary/expert/reviewer schváleny; názvy modelů a jejich API nejsou ověřeny. Model Router dosud pouze návrh; definovat deterministické směrování, rozpočet, abstenci při nejistotě a ověřitelnou vazbu oponentury na konkrétní operaci. F06, F13. |
| [SENT-004 – Executor](https://chatgpt.com/space/page_6e23a7b368108191bee6e070c341557e) | 1.1 | Katalog je příliš obecný pro produkci; chybí přesné schemas operací, pravidla preconditions, trvalý stav a důvěryhodná oponentura. Žádný obecný restart služby, install_updates nebo změna firewallu bez individuální specifikace. F01, F02, F06, F07. |
| [SENT-005 – Approval Gateway](https://chatgpt.com/space/page_5a258780a4bc8191b6034609fbfea427) | 1.0 | R2 Telegram a R3 WebAuthn jsou koncepčně konzistentní. Pro R3 oddělit Telegram identitu/token i vydávání autorizací; nezávislý kanál musí skutečně vynucovat ověření. F06, F08. |
| [SENT-006 – Installer](https://chatgpt.com/space/page_9d75046734008191bbce97a83420dd72) | 1.1 | Postup instalace uvádí jen Nmap/Lynis/CrowdSec a neodráží celý schválený bezpečnostní stack. Doplnit Restic, AIDE, Trivy, Tracker, Watchdog a podmíněné profily, instalaci bezpečnou při konfliktu a rozlišení čistého/používaného hostu. F04, F09. |
| [SENT-007 – Control Center](https://chatgpt.com/space/page_146fbb85b21c8191ac1aa324e3ffdcd4) | 1.2 | Threat Intelligence je správně centrální; lokální Trivy/Tracker zůstávají nezávislé. Rozlišit kurátorovanou znalostní bázi od Improvement Pipeline a od autoritativní konfigurace. Zajistit, že centrála nemůže vydávat vlastní privilegovanou autorizaci. F10, F11. |
| [SENT-008 – Vývoj a CI](https://chatgpt.com/space/page_5d5c8ee7f5508191b17b4e41e6a1101d) | 1.1 | Chráněné větve, review a podepsaná vydání jsou vhodné, ale tarif GitHubu a reálná dostupnost nezávislého lidského revizora nejsou vyřešeny. Testovací pipeline nesmí mít produkční přístupy. F12. |
| [SENT-009 – Update Manager](https://chatgpt.com/space/page_0aa21584033481918a6938a7e170e386) | 1.0 | Dobře oddělené vydávání, schvalování a distribuce. Rozlišit normální aktualizaci a nouzovou obnovu; bezpečný bootstrap klíčů a postup jejich rotace, verze/schéma, kontrola downgrade a stav při přerušení. F05, F07, F12. |
| [SENT-010 – Improvement Pipeline](https://chatgpt.com/space/page_82693c51cbb881919c510a18a40e3168) | 1.0 | Konzistentní zákaz samovolné změny kódu. Návrhy i obsah Issues/PR nedůvěryhodné, přidat odpovědnost triáže a oddělit provozní znalosti od změny produktu. Automatizaci GitHub Issues odložit. F11, F12. |
| [SENT-011 – Backup & Disaster Recovery](https://chatgpt.com/space/page_5577a7be1e288191b18025038bb45292) | 1.0 | RPO/RTO a retence jsou správně vedeny jako návrhy. Doložit nezávisle vynucenou ochranu před smazáním, reálný restore databáze a hostu, údržbu retence mimo VPS. F14. |
| [SENT-012 – Watchdog](https://chatgpt.com/space/page_ce52d9bad8ac8191a3cb776df3dec68d) | 1.0 | Externí dohled má vzniknout před AI a nesmí sdílet jediný bod selhání s hostem/Control Center. Heartbeat a externí aktivní sondy jsou různé funkce. F15. |
| [SENT-013 – AI Security](https://chatgpt.com/space/page_2f9a9a978880819189ea71d568dcf444) | 1.0 | Hrozby jsou dobře pojmenované; doplnit technicky ověřitelné síťové hranice, omezení celého účtu, broker modelových přístupů, jednotný režim pro sdílené znalosti a testy úniku. F02, F03, F11. |
| [SENT-014 – Incident Response & Lockdown](https://chatgpt.com/space/page_8d12ec3a5bf88191a1960ab1bb7c7a79) | 1.0 | Lockdown správně není automatická izolace hostu. Vyžaduje durable latch, nezávislé ovládání, sdílenou epochu autorizací a bezpečné zacházení s již běžícími operacemi. F07, F16. |

## 3. Registr zjištění a rozhodnutí

Závažnost K = kritické pro bezpečné privilegované operace nebo produkci, V = vysoké, S = střední. Nejsou to nálezy skutečných zranitelností serveru. Historický stav v okamžiku revize R1: otevřeno; návrhy byly obsaženy v MASTER RC1. Dne 26. 11. 2026 vlastník schválil všech 16 návrhů, byly promítnuty do MASTER 1.0 a sjednoceny v modulech SENT-001–014; jejich praktická implementace a akceptační testy zůstávají otevřené. Uzavření dokumentačního nálezu nenahrazuje implementační test.

| ID | Závažnost | Zjištění | Navržené řešení / brána |
|---|---|---|---|
| F01 | K | R2/R3 se překrývají u aktualizací, instalací, konfigurace a restartů. | Klasifikaci určuje lokální politika operace + parametry; nejpřísnější relevantní třída. V první správcovské etapě R2 jen jednotlivé bezpečně omezené restarty; OS instalace/upgrade, reboot, firewall a změny bezpečnostní hranice R3. Nejasné = zamítnout. |
| F02 | K | Rootless Docker omezuje privilegia, ale ne chrání před agentem zapisovatelným souborem, který později spustí root. | Izolovat servisní účty, žádný sdílený zapisovatelný kód/config/units/plugins/scripts, žádné Docker sockety agentovi; kontrola celé závislostní cesty každé privilegované operace. |
| F03 | V | Egress a cgroup limity jsou zatím pouze deklarace. | Vynucovat mimo agenta na hostu či izolovaném proxy; ověřit IPv4/IPv6, interní síť a metadata; test cgroups v2/systemd, CPU, RAM, PID, disk a modelový rozpočet; AI před produkcí bez únikových cest. |
| F04 | V | Chybí jednotný základní Debian profil, inventarizační kritéria a vlastník konfigurace. | Podporovaný Debian stable, schválené repozitáře, SSH klíče + konzole, MFA účtu poskytovatele, firewall pro IPv4/IPv6, čas, audit, minimální služby, řízené aktualizace; přesnou verzi a parametry ověřit. |
| F05 | K | Aktualizace produktu, OS balíčků a dat bezpečnostních nástrojů se mohou zaměnit. | Oddělené politiky; data/feed/cache pouze z povolených zdrojů bez spouštění kódu, OS změny po schválení, Sentinel signed release + canary; žádné automatické rebooty. |
| F06 | K | Oponentura R3 může být pouhým tvrzením modelu nebo agenta. | Ověřitelný nezávislý výsledek review vázaný na ID/hash operace; R3 silné schválení + důkazy + nezávislá oponentura. Žádná autorizace z textu AI. Selhání = žádná akce. |
| F07 | K | Protokol musí pokrýt replay, souběh, výpadky a nejasné výsledky; Lockdown musí fungovat napříč komponentami. | Verzované typované schéma, kanonický hash, preconditions, revize politiky a epochy, expirace, durable ledger, atomická spotřeba souhlasu; po pádu nejasný stav vyšetřit, ne opakovat. Test závodů a restartů. |
| F08 | K | Kompromitace Telegram tokenu či komunikační vrstvy nesmí umožnit R3. | Telegram jen R2 nízkého dopadu a notifikace; R3 samostatný ověřovací a autorizační kanál s oddělenou identitou, Passkey a potvrzením konkrétní transakce. Bezpečný fallback na ruční správu vlastníka. |
| F09 | V | Installer nezahrnuje všechny později schválené nástroje. | Rozšířit Ansible role a akceptační testy; core nasazovat bez AI, další komponenty až po testu; nepřepisovat existující konfiguraci. |
| F10 | K | Kompromitace Control Center by mohla mít plošný dopad; autorizace se nesmí opírat o samotné centrální oznámení. | První centrála pouze read-only; pozdější správa jen s per-host autorizací a lokálním vynucením. Jedinečné identity, odvolání a oddělené klíče. |
| F11 | V | SENT-013 dovoluje sdílení znalostí jen přes Improvement Pipeline, SENT-007 zavádí Security Knowledge Base. | Oddělit kurátorované provozní poznatky (SENT-007) od návrhů změn kódu (SENT-010). Obě cesty sanitizované a auditované; žádná nemění politiku. |
| F12 | V | Neověřený GitHub tarif, reálný nezávislý reviewer, release podpisy a ochrana CI. | Ověřit tarif a vynutitelnost pravidel pro privátní repo; pokud nestačí, zvolit vhodný tarif nebo ekvivalentní vynucení. Produkční kritický kód bez skutečné odborné lidské revize nevydat. Oddělit testy, podpis a distribuci; ověřit bootstrap/rotaci. |
| F13 | S | Modely a Hermes jsou kandidáti, ne potvrzené API/benchmarky; směrování není přesně vymezené. | Role modelů, fixture testy, kompatibilita Hermes, offline stub, jednoznačná deterministická eskalace a rozpočet. Není blokér pro Core. |
| F14 | K | Záloha není záruka obnovitelnosti ani důkaz čistoty; append-only neřeší všechny formy poškození. | Nezávislá retence, 3-2-1, konzistentní DB export, reálný test obnovy na čisté VM, chráněné klíče a audit. Konkrétní RPO/RTO až po inventuře a měření. |
| F15 | V | Watchdog a Control Center mohou sdílet bod selhání; heartbeat může být podvržen. | Externí dohled a nezávislý záložní komunikační kanál od MVP. Oddělit heartbeat od externí sondy; monitorovat centrálu, nikoli pouze přes ni. |
| F16 | K | Lockdown nepokrývá všechny stavy běžící operace a reboot/obnovu. | Stav mimo AI, persistentní revize/epoch, odvolání dosud nepoužitých schválení; bezpečné zpracování probíhající operace; agent nesmí deaktivovat. Kompromitace root = externí izolace a čistá obnova. |

## 4. Hranice důvěry a reziduální rizika

| Hrozba | Předpokládaná ochrana | Co ochrana nezaručuje |
|---|---|---|
| Útočník ovlivní log/článek/paměť/model | Sanitizace, žádný implicitní tool calling, externí politika | Úplnou odolnost AI proti manipulaci |
| Kompromitace účtu sentinel | Oddělené identity, žádný root socket, host-side limity, omezený Executor | Ochranu před každou chybou kernelu nebo před poškozením vlastních dat agenta |
| Kompromitace Telegramu nebo centrály | Per-host autorizace, samostatné R3 ověření, lokální politika | Automatické rozpoznání zfalšované nízkorizikové konverzace |
| Kompromitace hostitelského root | Externí logy, kopie baseline, zálohy, Watchdog, čistá obnova | Důvěryhodnost lokálních výstupů nebo zaručenou detekci |
| Kompromitace vývoje/release klíče | Izolované CI, revize, podpis, rotace, oddělené schválení nasazení | To, že každý podepsaný artefakt je bezpečný |
| Selhání dodavatele AI, sítě či záloh | Nezávislé nástroje, limity, watchdog, fail-closed pro mutace | Nepřetržitou dostupnost bez praktických testů |

## 5. Doporučené pořadí realizace

A – příprava Git a izolované testovací VM; B – Ansible, předběžná inventura, účty, bezpečný baseline a Collector; C – všechny schválené lokální bezpečnostní nástroje v omezeném režimu, Restic, Watchdog; D – AI pouze čtení, omezení a Telegram komunikace; E – testovaný Executor R2 a Gateway na samostatných scénářích; F – R3, aktualizace produktu, Lockdown, obnova a produkční brány; G – Control Center nejprve read-only, Threat Intelligence, později vícehostové zásahy. Vývojové etapy se mohou iterovat, ale bezpečnostní brány nesmí být přeskočeny.

## 6. Otevřené rozhodnutí a blokéry

| Rozhodnutí | Kdy musí být vyřešeno |
|---|---|
| GitHub vlastník, tarif a reviewer; testovací VM/VPS | Před inicializací vývoje |
| Podporovaný Debian, zdroje balíčků, architektura a profil VPS | Před testem instalace |
| Zálohovací backend, externí Watchdog a cíle obnovy | Před prvním funkčním MVP s reálnými daty |
| Hermes integrace, modelové API, rozpočet a egress | Před připojením AI |
| Přesné schéma Executor/Gateway, důvěryhodný R3 ověřovač | Před delegovaným zásahem |
| Podpisová infrastruktura a canary | Před produkční distribucí |
| Hosting, protokoly a zdroje Control Center | Před centralizací |

## 7. Doplňující ověření

Oficiální dokumentace potvrzuje omezení rootless resource limitů a nutnost ověřit cgroups v2/systemd a delegaci kontrolerů ([Docker – Rootless tips](https://docs.docker.com/engine/security/rootless/tips/)). GitHub ochrana privátních větví závisí na tarifu ([Protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)). Debian bezpečnostní opravy se často přenášejí do stávajících verzí ([Debian Security FAQ](https://www.debian.org/security/faq)); upstream číselná verze sama nestačí. Ověřeno 26. 11. 2026 na úrovni dokumentace; kompatibilita konkrétních verzí, prostředí, tarifů a služeb se ještě netestovala.

## 8. Verdikt a další krok

Podmíněně GO pro vytvoření privátního vývojového repozitáře a bezpečný laboratorní Core po schválení MASTER RC1; NO-GO pro produkční nasazení nebo delegované R2/R3, dokud nejsou splněna příslušná akceptační kritéria. Rozhodnutí vlastníka 26. 11. 2026: MASTER 1.0 RC1 schválen. Následuje E0 dle realizačního plánu; vlastnictví GitHub repozitáře, nezávislá technická revize a izolované testovací prostředí jsou dosud neověřené. Revize nezavádí nové oprávnění a nezvyšuje autonomii Sentinelu.

## 9. Uzavření dokumentační revize – 26. 11. 2026

Vlastník schválil výsledky R1 a MASTER 1.0 RC1. [MASTER 1.0](https://chatgpt.com/space/page_0542512790e081919d47e5e6850c2c0e) je nyní závazná dokumentační baseline; moduly SENT-001–014 byly sjednoceny s revizními závěry F01–F16, včetně zvýšení verzí a revizních poznámek. Všechna zjištění mají schválený návrh řešení; implementace a praktické testy zůstávají neprovedené a otevřené. Stav GO pro přípravu izolované laboratoře, NO-GO pro produkční nasazení a delegované R2/R3 do splnění příslušných bran. [Roadmap](https://chatgpt.com/space/page_2cdc66702c5c819183899d8845edbb6b); [Backlog E0/E1](https://chatgpt.com/space/page_30fe3ccf32d48191a0ead821438982a1).
