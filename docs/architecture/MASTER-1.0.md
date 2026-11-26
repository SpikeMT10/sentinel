# Sentinel – MASTER 1.0 – souhrnná architektura a závazné požadavky

> Snímek projektové dokumentace k 26. 11. 2026. Zdroj: https://chatgpt.com/space/page_0542512790e081919d47e5e6850c2c0e. Změny architektury vyžadují evidovanou revizi a souhlas vlastníka.

**MASTER 1.0 | 26. 11. 2026 | Schváleno vlastníkem | Závazná dokumentační baseline; nikoli produkční vydání.**

Podklad: [Závěrečná architektonická revize R1](https://chatgpt.com/space/page_68d5eab9a4948191bf3a848bff4bb8bc) a 14 schválených modulů. Účel: jednotné implementační zadání pro první bezpečný Sentinel a následné etapy. Dřívější schválené koncepty zachovány. Vlastník dne 26. 11. 2026 schválil MASTER 1.0 RC1 i výsledky revize R1 (F01–F16). MASTER 1.0 je od tohoto rozhodnutí nadřazeným sjednocujícím dokumentem; podrobné moduly se řídí jeho normativními pravidly. Budoucí změny baseline pouze evidovanou revizí a schválením vlastníka. Schválení architektury není schválením jednotlivých systémových zásahů ani produkčního nasazení.

## 1. Vize a rozsah

Sentinel je opakovatelně nasaditelný, verzovaný správce Debian VPS, který zjišťuje stav, chrání, vysvětluje a postupně navrhuje a vykonává výslovně povolené operace. Vlastník ovládá systém přirozeným jazykem, preferovaně jedním kontaktem v Telegramu. Sentinel nesmí mít neomezený root ani možnost samovolně měnit vlastní bezpečnostní hranice. Ochranné nástroje, zálohy a nouzová správa fungují nezávisle na AI. Sdílené Threat Intelligence je součástí Control Center, nikoli další agent na každém hostu.

## 2. Schválená normativní pravidla

| ID | Požadavek |
|---|---|
| M-01 | AI je nedůvěryhodná z hlediska oprávnění. Model ani jeho paměť neudělují práva. |
| M-02 | Dedikovaný účet sentinel, Rootless Docker, žádné sudo ani Docker socket, žádné zápisy do privilegovaného kódu a konfigurace. |
| M-03 | Lokální Executor je jediná cesta pro delegované privilegované mutace. Default-deny, typovaný katalog, validace parametrů a nezávislé schválení. |
| M-04 | Vlastník má nezávislou konzoli a správu, vlastní klíče a ruční obnovu bez AI, Gateway a centrály. |
| M-05 | Ochranné nástroje, Collector, plánované zálohy a externí dohled fungují bez AI; stav neznámý nikdy není zelený. |
| M-06 | Veškeré změny infrastruktury jsou verzované, opakovatelné, auditované a předvídatelné. Instalace na cizím/existujícím hostu nepřepíše konfiguraci bez souhlasu. |
| M-07 | Schvalování a oponentura se vážou na konkrétní operaci a aktuální stav. Text AI, doporučení centrály ani podpis vydání sám o sobě není autorizace. |
| M-08 | Zálohy mimo host, jedna nezávisle chráněná před smazáním, ověřená obnova a oddělená správa retence. |
| M-09 | Jedinečná identita a politika každého hostu. Centrála neobchází lokální oprávnění; výpadek centrály nezastaví lokální ochranu. |
| M-10 | AI si nesmí přidávat nástroje, upravovat politiku, produkční kód, release pipeline ani schvalovací klíče. Zlepšení přes oddělený vývoj a testy. |
| M-11 | Všechna vydání produktu mají ověřitelný původ, testy, explicitní souhlas s nasazením a kontrolovanou aktualizaci. |
| M-12 | Vyšší autonomie je podmíněna praktickým testem, dokumentovaným rizikem a samostatným schválením vlastníka. |

## 3. Komponenty a odpovědnosti

| Komponenta | Umístění / důvěra | Odpovědnost |
|---|---|---|
| Sentinel Installer / Ansible | Administrátorské prostředí, mimo AI | Deklarativní nasazení a bezpečné změny infrastruktury |
| Sentinel Security | Host, samostatné služby | Nmap, CrowdSec, Lynis, AIDE, Trivy, Debian Security Tracker, Restic |
| Sentinel Collector | Host, oddělené služby s minimálními právy | Deterministické kontroly, sanitizované reporty a inventář |
| Sentinel Agent / Hermes | Rootless Docker, účet sentinel | Diagnostika, konverzace, návrhy; bez privilegovaného přístupu |
| Model Router | V agentní vrstvě + pravidla mimo model | Role primary/expert/reviewer, eskalace a rozpočet; neposkytuje autorizaci |
| Approval Gateway | Oddělená služba bez AI | Důvěryhodné zobrazení a autorizace transakce; R3 samostatné silné ověření |
| Executor | Host, nejmenší nutná privilegia | Ověření politiky a autorizace, omezené operace, kontrola výsledku |
| Update Manager | Host, oddělená důvěryhodná služba | Ověřené vydání, staging, schválení, health check a obnova |
| Lockdown | Host, stav mimo AI + nezávislé ovládání | Zastavení AI iniciovaných změn a zneplatnění nepoužitých autorizací |
| Watchdog | Mimo sledované hosty | Nezávislé sondy, heartbeat a oznámení |
| Backup & DR | Host + oddělené úložiště a správa | Záloha, integrita, skutečná obnova |
| Control Center | Oddělená centrální infrastruktura | Fleet registr, korelace, Threat Intelligence, znalostní báze a pozdější řízená správa |
| Improvement Pipeline | Oddělený vývoj / GitHub | Návrhy, triáž, PR, testy a schválená vydání |

Collector a bezpečnostní nástroje mají vlastní služební identity a přístup jen ke svému účelu; pro operace vyžadující zvýšená práva používat malé fixní hostitelské služby. Agent nesmí měnit žádné soubory, které root následně spouští, importuje nebo čte jako řídicí konfiguraci. Artefakty a konfigurace služeb pod kontrolou vlastníka; agentem zapisovatelná pracovní data, paměť a cache nejsou důvěryhodné. Pozor na závislosti a oprávnění adresářů, nikoli jen vlastníka hlavního souboru.

## 4. Minimální bezpečnostní profil Debianu

Podporovaný Debian stable na první schválené architektuře, konkrétní verzi a podporu VPS ověřit před implementací. Inventura bez změn: OS a podpora, CPU/RAM/disk, cgroups, účty, SSH, síť IPv4/IPv6, DNS, naslouchající služby, kontejnery, firewall, reverse proxy, balíčky, data a konzole. Před každou změnou síťového přístupu ověřit nezávislou konzoli a druhé funkční přihlášení; admin účet, SSH klíč, MFA účtu poskytovatele, bezpečně uchované obnovovací údaje, zákaz běžného vzdáleného root a heslového přihlášení po ověření náhradní cesty. Výchozí zákaz nevyžádaných příchozích spojení s explicitními výjimkami dle inventáře; ověřit reálný průchod IPv4/IPv6, Docker porty a případné Cloudflare origin omezení. Zachovat stávající služby; žádné plošné přepisy firewallu. Důvěryhodné podepsané repozitáře, minimalizace služeb, časová synchronizace, audit a externí kopie, rotace klíčů, přiměřené limity disku a prostředků. Vše jako Ansible role s testem opakovaného běhu a rollbackem tam, kde je proveditelný. Výjimky mají rozsah, vlastníka, důvod a expiraci.

Rootless Docker je bezpečnostní vrstva, ne absolutní sandbox. Před připojením AI ověřit skutečné cgroups v2 a systemd limity celého účtu, CPU, RAM, PID a disk, síťová omezení mimo kontrolu agenta, IPv4/IPv6, zákaz cloud metadata a neautorizovaných interních sítí. Nemountovat celý host, /run, privilegované sockety ani cizí tajemství. Rozpočet modelů vynucovat mimo samotný model; preferovat izolované přístupové klíče nebo broker. Kontroly vyzkoušet při vypnutém agentovi i při simulovaném zneužití jeho nástrojů.

## 5. Schválené bezpečnostní nástroje a plán

| Nástroj | Úloha | Výchozí interval – návrh |
|---|---|---|
| Nmap | Síťová expozice vlastních autorizovaných cílů, lokálně i externě | Denně lehký, týdně rozšířený |
| CrowdSec | Detekce útoků; bouncer až po samostatném schválení a testu | Průběžně; stav každých 5 min |
| Lynis | Audit konfigurace Debianu | Týdně a po významné změně |
| AIDE | Integrita systémových souborů, schválená baseline chráněná mimo host | Denně a po významné změně |
| Trivy | Zranitelnosti obrazů a aplikací, inventář hostu, CI | Denně a při změně |
| Debian Security Tracker | Distribuční CVE a stav oprav | Denně a po změně inventáře |
| Restic | Konzistentní šifrované zálohy a obnova | Dle RPO; obvykle denně a před rizikovou změnou |

Ndiff, firewall bouncer (konkrétní varianta), systemd timers, backend Restic a poskytovatel Watchdog jsou implementační volby k ověření, nikoli nové nezávisle schválené produkty. Schválené nástroje nepředstavují schválení neomezeného skenování, změn firewallu, aktualizací či mazání záloh. Detekce, kontrola a plánovaná údržba jsou oddělené od oprav. AIDE baseline a portový inventář neměnit bez schválení. Debian CVE korelovat dle zdrojového balíčku, verze, vydání a skutečného distribučního stavu opravy. Nepoužívat pouze číslo upstream verze.

## 6. Jednotný model oprávnění

| Třída | Vymezení a příklad | Autorizace |
|---|---|---|
| R0 | Čtení již zpřístupněných stavů/reportů, bez mutace hostu | Automaticky v allowlistu |
| R1 | Fixní bezpečné kontroly se známými vedlejšími efekty, např. Nmap, Lynis, ověření zálohy do testovacího cíle | Předem schválená deterministická politika, limity, audit |
| R2 | Omezený, konkrétně specifikovaný a vratný provozní zásah s nízkým až středním dopadem; první příklad restart předem povolené služby | Individuální potvrzení přes Gateway; předpoklady, ověření a plán návratu |
| R3 | Bezpečnostní hranice, SSH/firewall/síť, reboot, instalace/upgrade OS balíčků a produktu měnícího důvěryhodný kód, oprávnění nebo destruktivní operace | Samostatné silné potvrzení, nezávislá oponentura, záloha, nezávislý přístup, test a plán obnovy; pouze výslovně implementované a schválené operace |

R0/R1 neznamená, že nástroj nemůže vytvářet vlastní reporty či cache. Znamená absenci oprávnění měnit produkční konfiguraci a libovolné spustitelné soubory. Plánované Restic zálohy, aktualizace datových feedů a CrowdSec bouncer běží mimo AI dle samostatně schválené politiky; agentem vyžádaný nový zálohovací zásah má vlastní klasifikaci. Údržba retence mimo spravovaný host. Obecný restart/instalace není automatické oprávnění. V první správcovské etapě R2 pouze nízkorizikový allowlist; OS instalace a upgrade R3. V nejasném případě nejpřísnější odpovídající třída nebo zamítnutí. Riziko přiděluje lokální politika na základě konkrétní operace/parametrů, ne model. Incidentní priority P0–P3 jsou jiná osa než R0–R3. Neexistuje obecná R3 operace „spusť příkaz“.

## 7. Executor, Gateway a stav autorizace

Požadavek přes oddělený lokální socket → ověření identity procesu, protokolu, pevného schématu a allowlistu → kanonický záznam s host ID, ID požadavku, typem operace, přesnými parametry, revizí politiky, stavem předpokladů, expirací, rizikem a epochou Lockdown → karta vytvořená Gateway z tohoto záznamu → nezávislé autorizace → kontrola aktuálního stavu těsně před provedením → trvalý audit, spotřeba jednorázového souhlasu, konkrétní vykonavatel a ověření výsledku. Oddělit návrh, autorizaci, provádění a reportování. Každá operace má vlastní typované schéma, dovolené cíle, pevné argumenty, časový limit, limity frekvence, test předpokladů, způsob kontroly výsledku a návratový postup. Žádný shell, neznámé parametry nebo libovolné cesty. Go je kandidát, nikoli definitivní jazyk.

Telegram bot token mimo Hermes. R2 tlačítko pouze z důvěryhodného kanonického záznamu a od předem registrovaného uživatele v soukromém chatu; běžná zpráva není souhlas. R3 nezávislé WebAuthn s odděleným autorizačním oprávněním/klíčem, kontrolou origin/RP ID, user verification, výzvou svázanou s konkrétní transakcí a explicitním potvrzením. Samotná kompromitace Telegram vrstvy nesmí umožnit vydání R3 autorizace. Oponentura R3 je nezávisle evidovaný a ověřitelný výsledek svázaný s totožným hashem operace, nikoli přeposlané tvrzení AI. Vlastník vždy schvaluje. Oponentura a schválení jsou dvě různé brány. Pokud implementace nezávislé kontroly není ověřená, R3 delegace zůstává zakázána; vlastník může spravovat server ručně nezávisle na Sentinelu.

Stavy: proposed, awaiting_approval, denied/expired, authorized, running, succeeded, failed, unknown, recovery_required. Serverový záznam má jednoznačnou identitu, durable ledger a atomickou spotřebu autorizace. Změna stavu hostu, parametrů, politiky nebo Lockdown epochy vyžaduje nové posouzení. Po pádu při nejasném výsledku neprovádět slepý retry; skutečné vedlejší efekty nelze obecně garantovat exactly-once. Zkontrolovat stav a eskalovat. Při nedostupnosti ověřovače, politiky, důkazů, nezbytného auditu nebo obnovy platí fail-closed pro mutace.

## 8. Collector a společný datový kontrakt

Každá kontrola zapisuje strukturovaný výstup s host ID, schématem a verzí nástroje, časem UTC pozorování, časem přijetí, ID kontroly, výsledkem, stavem ok/degraded/failed/unknown/not_applicable, časem posledního úspěchu, důkazy a relevantními odkazy, očekávaným a skutečným stavem, případným CVE, závažností, nejistotou a ID incidentu. Přidat schválený profil a revizi baseline, pokud je relevantní. Deterministický sběr a validace před AI; žádné heslo, klíč nebo neomezený raw log. Limity velikosti, timeout, zámky, retence a externí kopie auditu. Zastaralý výstup ani timeout se nezobrazují jako úspěch. Přesné JSON schéma, způsob úložiště a retence jsou první technické zadání, nikoli již existující API.

## 9. Modely, ochrana AI a Threat Intelligence

Modelové role primary, expert, reviewer jsou oddělené. Hermes a konkrétní modely jsou kandidáti; před nasazením ověřit oficiální model ID, skutečný přístup, náklady, podmínky zpracování dat a tool calling. Směrování podle typu úlohy, závažnosti, nejistoty, rozpočtu a dostupnosti; povinné revize nesmí být vypnuty modelem. MVP může použít jeden ověřený primární model a testovací stub pro deterministické scénáře; R3 není povoleno bez nezávislé kontroly. Volání AI na událost/souhrn, nikoli každý vzorek. Nedůvěryhodné vstupy z logů, webu, reportů a paměti nejsou instrukce. Fyzicky oddělit tajemství, nástroje, bezpečnostní politiku a dlouhodobou paměť. Selhání AI nesmí zastavit sběr či ochranu.

Control Center později sjednotí bezpečnostní zpravodajství, deduplikaci, obecnou analýzu jednou a korelaci na každý host; zdroje dle SENT-007 (KEV, NVD, Debian, CERT-EU, NÚKIB, GitHub advisories, Wordfence, EPSS a výrobci). Lokální Trivy/Tracker mají ověřený fallback nezávislý na centrále. Kurátorovaná znalostní báze v SENT-007 sdílí sanitizované, verzované a revidované bezpečnostní poznatky. Improvement Pipeline v SENT-010 přijímá návrhy změn produktu. Žádný ze zdrojů nemění lokální politiku ani nepovoluje operaci. Centrála nejprve pouze čtení; později řízené zásahy s per-host autorizací a oddělenou identitou. Žádný univerzální privilegovaný SSH účet, klíč, shell ani hromadné oprávnění.

## 10. Aktualizace, vývoj a dodavatelský řetězec

| Typ | Výchozí pravidlo |
|---|---|
| Data bezpečnostních nástrojů (CVE, feedy) | Automaticky z povolených ověřených zdrojů; bez spouštění externího kódu; hlásit stáří a chyby |
| Balíčky Debianu a aplikací | Kontrola dostupnosti automatická, instalace z důvěryhodných repo po schválení; v první etapě žádné automatické upgrady/rebooty |
| Vydání Sentinel | Podepsaný manifest, digest, kompatibilita, schválení konkrétního hostu/verze, staging, canary a health check, rollback/obnova |
| Politika a privilegované komponenty | Změna bezpečnostní hranice pouze řízenou cestou vlastníka, R3 pokud delegována; žádná AI změna vlastního oprávnění |

Provozní návrh: P0 a doložené akutní riziko hlásit ihned a vyžadovat lidské posouzení; vysoké riziko posoudit bez zbytečného odkladu, navržený cíl do 24 h u akutních kritických nálezů, do 72 h u vysokých; nižší rizika v plánovaném okně. Jde o pracovní cíle k odsouhlasení, nikoli garanci nebo povolení automatické opravy. Pokud oprava není dostupná, zdokumentovat mitigaci a termín přezkumu. Automatické aktualizace OS lze později posoudit jako oddělenou, předem schválenou deterministickou politiku; nejsou součástí schválené baseline 1.0.

Privátní GitHub, chráněná hlavní větev či ekvivalentní vynucený proces, PR, povinné CI, testovací VM bez produkčních klíčů, skutečná odborná lidská revize bezpečnostně citlivého kódu, oddělené vydání a nasazení. Prověřit tarif privátního repo a dostupnost reviewerů; AI oponentura nenahrazuje nezávislou odbornou kontrolu. SemVer, SBOM, pin/digest, podepsaný manifest, oddělené podpisové a distribuční identity, bezpečný počáteční trust anchor a revokace/rotace. Agent nikdy nemá přístup k release klíčům nebo write tokenu produkčního kódu. Aktualizaci vlastního produktu provádí Update Manager mimo AI a výhradně autorizovanou cestou. Nevratné migrace vyžadují plán skutečné obnovy. Jeden host nesmí rozesílat neověřený kód jiným hostům.

## 11. Obnova, Watchdog a Lockdown

Restic podle SENT-011: inventura dat a konzistentní databázové exporty, šifrované kopie, 3-2-1, nejméně jedna nezávisle chráněná proti smazání, oddělená správa retence a klíčů, ověřená ruční obnova. Restic append-only či jiná ověřená ochrana úložiště je kandidát; nepředstavuje ochranu proti všem typům kompromitace ani důkaz čistoty zálohy. Výchozí cíle RPO/RTO z SENT-011 jsou návrhy; schválit dle skutečné aplikace a měřených obnov. Externí Watchdog vzniká před AI, samostatné sondy a heartbeat, vlastní identita, záložní oznamovací cesta nezávislá na monitorovaném hostu i Control Center, test doručení. Nelze garantovat detekci kompromitace root pouhým heartbeat.

Lockdown: hostitelský persistentní stav mimo agent, kontrolovaný Executorem i Update Managerem. Aktivace nezávisle vlastníkem; automatické spouštěče až po zvláštním schválení. Zablokovat nové AI iniciované mutace, invalidovat dosud nepoužité autorizace, pozastavit instalace produktu a bezpečně řešit běžící operaci podle runbooku. Nezávislé bezpečné kontroly a zálohy pokračují, dokud je to podle incidentu vhodné. Agent nemůže režim vypnout. Návrat vlastníkem po ověření. Podezření na kompromitaci hostu/root: nedůvěřovat lokálnímu stavu, nezávisle izolovat, uchovat potřebné důkazy, rotovat klíče a obnovit čistý host. Lockdown není automatická síťová izolace.

## 12. Realizační etapy a minimální funkční produkt

| Etapa | Výstup | Brána |
|---|---|---|
| E0 – příprava | Privátní repo, role, CI, dokumentace, testovací Debian VM/VPS | Vlastník repo, dostupný test, žádná produkční tajemství |
| E1 – Core 0.0.x | Preflight, Ansible, účet sentinel, Collector, deterministický reporting | Dva běhy instalace, žádná nečekaná změna, ověřené hranice |
| E2 – bezpečnostní Core | Nmap, CrowdSec detekce, Lynis, AIDE, Trivy, Tracker, Restic, externí Watchdog | Kontroly a nezávislá obnova/hlášení fungují bez AI |
| E3 – MVP 0.1 | Rootless Agent, ověřený primární model, bezpečná komunikace, čtení a diagnostika | Žádné delegované R2/R3; izolace, modelové limity a testy |
| E4 – Management | Úzký Executor R2 + Gateway, lokální Lockdown | Výslovné schválení, test opakování, podvržení, výpadku a rollbacku |
| E5 – Release 1.0 | Ověřené aktualizace, obnovitelnost, R3 jen jednotlivě prověřené, produkční instalace | Nezávislá revize, testovací a produkční akceptace, samostatný souhlas |
| E6 – Multi-server | Control Center read-only, Threat Intelligence, společné znalosti; později autorizované zásahy | Izolace hostů, výpadek/kompromitace centrály, per-host souhlas |

MVP 0.1 = první funkční pozorovatel: všechny schválené základní bezpečnostní nástroje, bezpečná opakovatelná instalace, Collector, funkční záloha a externí hlášení, AI pouze čtení reportů, jednoduchá komunikace. Nevyžaduje plný Executor, R2/R3, všechny tři modely, automatizované aktualizace, vlastní webovou schvalovací bránu, bouncer ani Control Center. CrowdSec detekční režim je explicitně degradovaný z pohledu aktivního blokování; bouncer zapnout po schválení a úspěšných testech. Jednotlivé nástroje lze implementovat postupně v E2, nikoli čekat s ověřováním všech až na MVP.

## 13. Schválené akceptační brány

| Test ID | Scénář | Kritérium úspěchu |
|---|---|---|
| AT-01 | Instalace dvakrát na čisté VM a na kontrolované použité VM | Druhý běh žádná neočekávaná změna; žádný přepsaný cizí firewall/služba; známé rozdíly zdokumentovány |
| AT-02 | Účet sentinel a kontejner | Bez sudo/socketů/cizích dat; neúspěšný zápis do politiky, kódu a auditů |
| AT-03 | Egress a zdroje | Testované síťové zákazy a funkční CPU/RAM/PID/disk/API limity; nezávislé služby pokračují |
| AT-04 | Vypnutý agent / AI API | Plánované kontroly, CrowdSec, zálohy a externí hlášení pokračují |
| AT-05 | Kontrolovaný nový port, změna souboru, neaktuální CVE data | Detekce rozdílu nebo unknown, žádná automatická oprava nebo přijetí nové baseline |
| AT-06 | Záloha a obnova | Restore souboru/DB i úplného hostu do izolovaného cíle; měřená doba, žádné přepsání produkce; ověřené externí omezení mazání |
| AT-07 | Výpadek hostu a Telegramu | Externí watchdog a záložní kanál skutečně upozorní vlastníka |
| AT-08 | Podvržený report, prompt injection, neplatný tool call | Žádná nepovolená akce nebo únik testovacího tajemství; audit zamítnutí |
| AT-09 | R2 schválení, nesprávný uživatel/host/parametr, replay/expirace | Všechny negativní scénáře zamítnuty; legitimní operace ověřena |
| AT-10 | R3 bez nezávislé oponentury, s podvrženým Telegram souhlasem, změnou stavu | Žádná delegovaná mutace; při povolené R3 ověřeno silné schválení a obnova |
| AT-11 | Pád procesu uprostřed operace | Trvalý stav, žádný slepý duplicitní zásah, ruční vyšetření unknown |
| AT-12 | Lockdown, reboot a dřívější autorizace | Stav přetrvá, mutace a instalace zastaveny; starý souhlas neplatí; nezávislé služby podle politiky pokračují |
| AT-13 | Podvržené/pozměněné vydání a nepovolený downgrade | Odmítnuto; povolené vydání ověřeno, canary neúspěch zastaví další vlny |
| AT-14 | Dva hosty a výpadek/kompromitace simulované centrály | Bez cross-host autorizace a sdíleného root; lokální ochrana pokračuje |
| AT-15 | Nové bezpečnostní upozornění v Threat Intelligence | Jedna obecná analýza, správné rozlišení dotčených hostů, zdroj a stáří, žádná automatická oprava |

Všechny relevantní testy musí být pro danou etapu doložené výsledkem, verzí, testovacím prostředím, časem a odpovědnou osobou. Kritický neúspěch znamená NO-GO pro příslušnou etapu. Nulová tolerance nepovolených mutací a úniků v definovaných testovacích scénářích není záruka nulového rizika. Produkční nasazení až po nezávislé technické kontrole a souhlasu vlastníka; architektonická revize sama není bezpečnostní certifikace.

## 14. Řízení otevřených otázek a další krok

Nejsou potvrzeny: přesný Debian release a architektura, poskytovatel a náklady testovacího VPS, GitHub účet/tarif/reviewer, volba konkrétních modelů, Hermes integrace, zálohovací backend a RPO/RTO, Watchdog a jeho kanál, finální protokol Executor/Gateway, podpisové řešení vydání, hosting Control Center a feed limity. Před každou etapou vyřešit pouze nutné blokéry. Návrh bez časového odhadu, protože rozsah implementace a prostředí dosud nejsou známy.

Schválení MASTER 1.0 uzavírá koncepční fázi. Následuje E0: určit vlastníka privátního repozitáře, ověřit tarif a přístup, zajistit nezávislou technickou revizi a izolovanou Debian VM. Poté E1: Ansible preflight a Collector bez systémových změn vyvolávaných AI. Akceptační testy zůstávají otevřené do doložení skutečných výsledků. Žádné repozitáře, účty, služby, tokeny ani VPS nebyly vytvořeny tímto dokumentem.

## 15. Evidence schválení a předání do realizace

26. 11. 2026 – vlastník výslovně schválil MASTER 1.0 RC1 a výsledky R1. MASTER 1.0 je dokumentační baseline; F01–F16 jsou zapracované jako požadavky a upřesnění v modulech SENT-001–014. Akceptační testy zůstávají otevřené; změna architektury vyžaduje evidovanou revizi a souhlas vlastníka.

- [Revize R1 a uzavření dokumentace](https://chatgpt.com/space/page_68d5eab9a4948191bf3a848bff4bb8bc)

- [Implementation Roadmap – E0 až E6](https://chatgpt.com/space/page_2cdc66702c5c819183899d8845edbb6b)

- [Development Backlog – 21 úkolů E0/E1](https://chatgpt.com/space/page_30fe3ccf32d48191a0ead821438982a1)

Nejbližší úkoly: DEV-001 – určit vlastníka GitHub repozitáře a ověřit tarif/přístup/review; DEV-006 – potvrdit izolované testovací prostředí. Po nich založit privátní repozitář a začít read-only preflight. Produkční nasazení, R2/R3 a automatické změny nejsou povoleny tímto schválením.
