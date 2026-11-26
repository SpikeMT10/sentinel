# Sentinel – Implementation Roadmap

> Snímek projektové dokumentace k 26. 11. 2026. Zdroj: https://chatgpt.com/space/page_2cdc66702c5c819183899d8845edbb6b. Změny architektury vyžadují evidovanou revizi a souhlas vlastníka.

**Verze 1.0 | 26. 11. 2026 | Realizační plán podle schváleného MASTER 1.0 | Stav: E0 připraveno k zahájení.**

Řídicí specifikace: [MASTER 1.0](https://chatgpt.com/space/page_0542512790e081919d47e5e6850c2c0e); podklad: [Revize R1, F01–F16](https://chatgpt.com/space/page_68d5eab9a4948191bf3a848bff4bb8bc).

## 1. Zásady realizace

Nejprve izolované vývojové a testovací prostředí. Žádné změny produkčních VPS, žádné delegované R2/R3. Git je autoritativní pro skutečný kód a vydání, projektové stránky pro rozhodnutí a specifikaci. Každá změna má úkol, závislosti, test a schválení odpovídající riziku. Etapy jsou závislosti a brány, nikoli slíbený časový harmonogram. Konkrétní termíny a náklady stanovit po výběru prostředí a ověření rozsahu.

## 2. Etapy a výstupy

| Etapa | Hlavní výstupy | Závislost a výstupní brána |
|---|---|---|
| E0 – vývojové prostředí | Privátní GitHub; README, ADR, složky, pravidla PR a CI; izolovaný testovací Debian | Určit vlastníka, tarif, přístupy, revizora; ověřit oddělení od produkce a CI. |
| E1 – Core 0.0.x | Ansible preflight, inventář, bezpečný setup účtů a izolace, Collector, datový kontrakt, health/status/doctor | E0; AT-01/02 a relevantní části AT-03; test čisté a kontrolované použité VM. |
| E2 – Security | Nmap, CrowdSec detekce, Lynis, AIDE, Trivy, Debian Security Tracker, Restic, nezávislý Watchdog | E1; AT-04/05/06/07; schválené skenovací cíle, backend a obnova. Bouncer až zvlášť po testu. |
| E3 – MVP 0.1 | Rootless AI agent, ověřený primární model, sanitizovaný vstup, bezpečná komunikace, diagnostika | E2; AT-02/03/04/08. Bez R2/R3 a bez potřeby všech tří modelů. |
| E4 – Management | Úzký katalog R2, Executor, Approval Gateway, Lockdown, audit | E3; AT-09/11/12, nezávislá odborná revize. Jen konkrétně schválené operace. |
| E5 – Release 1.0 | Ověřené vydání, Update Manager, instalace/obnova, případné jednotlivé R3 | E4; AT-06/10/12/13, relevantní regresní testy; samostatný souhlas s produkčním nasazením. R3 lze ponechat vypnuté. |
| E6 – Multi-server | Control Center read-only, centrální Threat Intelligence, společná znalostní báze, později řízená správa a Improvement Pipeline | E5; AT-14/15, izolace hostů a test výpadku centrály. |

## 3. Co bude hotovo v první funkční verzi

MVP 0.1 je jeden instalovatelný pozorovatel na laboratorním VPS, který poskytuje skutečné deterministické výsledky všech schválených nástrojů (CrowdSec pouze detekce), ověřenou zálohu, nezávislé upozornění a AI diagnostiku pouze nad omezenými daty. Není to ještě plně autonomní správce. Nevyžaduje Control Center, samostatný web R3, vlastní updater, více modelů ani privilegované mutace. E1/E2 průběžně poskytují použitelné CLI výsledky bez AI.

## 4. Rozhodnutí a jejich termín

| Rozhodnutí | Před čím | Stav |
|---|---|---|
| GitHub vlastník (osobní účet / organizace), jméno privátního repozitáře, tarif, přístup a nezávislý reviewer | E0 | Otevřeno |
| Testovací VM/VPS, poskytovatel, podporovaný Debian a architektura, rozpočet | E0/E1 | Otevřeno |
| Schéma Collector, inventář, preflight a Ansible role, síťová politika | E1 | Návrh k ověření |
| Restic backend a RPO/RTO, Watchdog a záložní kanál, autorizované skenovací cíle | E2 | Otevřeno |
| Hermes integrace, konkrétní model ID a API, limity a ochrana dat | E3 | Otevřeno |
| Protokol Executor/Gateway a nezávislá identita, R2 allowlist | E4 | Otevřeno |
| Podpis vydání, důvěryhodný klíč, canary a rollback | E5 (návrh již E0) | Otevřeno |
| Hosting centrály a zdroje Threat Intelligence | E6 | Otevřeno |

## 5. Odpovědnosti

Vlastník schvaluje zásadní rozhodnutí, rozpočet, přístupy a produkční nasazení. ChatGPT připravuje zadání a revize; vývojový agent nebo vývojář implementuje v izolovaném prostředí a předkládá PR. Skutečná odborná lidská kontrola je povinná před vydáním bezpečnostně citlivého kódu. CI ověřuje testy, nikoli bezpečnost sama o sobě. Nikdo z vývojových agentů nesmí obdržet produkční klíče nebo neomezený přístup k hostům.

## 6. Nejbližší postup

1. Potvrdit vlastníka GitHub repozitáře a izolované testovací prostředí.

2. Založit privátní repo se schváleným názvem; prověřit možnosti ochrany větví.

3. Založit prvotní strukturu, dokumentaci a CI bez produkčních tajemství.

4. Připravit a otestovat read-only Ansible preflight, následně kontrolovaný Core.

5. Po úspěchu E1 zahájit samostatné integrace bezpečnostních nástrojů E2.

Nejsou vytvořeny žádné GitHub repozitáře, VPS ani přístupové klíče. Všechny realizační brány jsou dosud neověřené.
