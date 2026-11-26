# Sentinel – Development Backlog – E0 a E1

> Snímek projektové dokumentace k 26. 11. 2026. Zdroj: https://chatgpt.com/space/page_30fe3ccf32d48191a0ead821438982a1. Změny architektury vyžadují evidovanou revizi a souhlas vlastníka.

**Verze 1.0 | 26. 11. 2026 | Připravené zadání pro první vývojové úkoly; realizace nezahájena.**

Řídicí specifikace: [MASTER 1.0](https://chatgpt.com/space/page_0542512790e081919d47e5e6850c2c0e); podklad: [Revize R1](https://chatgpt.com/space/page_68d5eab9a4948191bf3a848bff4bb8bc).

## 1. Pravidla backlogu

ID DEV-xxx je stabilní. Stav O = otevřeno, B = blokováno rozhodnutím/závislostí. Uzavření až po skutečném důkazu (PR/commit, testovací výstup a lidské schválení tam, kde je vyžadováno). P0 = nutné pro bezpečnost či zahájení etapy, P1 = důležité pro dokončení etapy. Všechny úkoly jsou otevřené nebo blokované, žádný není implementován. Rozdělení na Issues až po vytvoření správného repozitáře.

## 2. E0 – příprava vývojového prostředí

| ID | Úkol | Závislost / stav | Akceptace |
|---|---|---|---|
| DEV-001 | Určit vlastníka GitHubu, název repo, tarif a správu přístupů | — / O P0 | Vlastník potvrzen, MFA, ověřená možnost chránit privátní větev nebo ekvivalent. |
| DEV-002 | Založit privátní repo sentinel | 001 / B P0 | Repo privátní, správný vlastník, žádná tajemství; ověřená práva. |
| DEV-003 | Základ monorepa a dokumentace | 002 / B P0 | README, CONTRIBUTING, SECURITY, CHANGELOG, docs/adr, odkaz MASTER; oddělené vývojové komponenty. |
| DEV-004 | Nastavit Git workflow a nezávislou revizi | 002 / B P0 | Chráněná main či ověřený ekvivalent, PR, minimální práva, identifikovaný reviewer pro citlivé změny, ne automatické nasazení. |
| DEV-005 | CI pro lint a testy | 003,004 / B P0 | Validace Ansible, schémat, secret scan, unit testy; nepřístupné produkční údaje, omezené workflow permissions. |
| DEV-006 | Vybrat a připravit izolovanou Debian VM/VPS | — / O P0 | Schválený rozpočet, podporovaná verze/architektura, konzole, snapshot/obnova, žádné produkční aplikace. |
| DEV-007 | Definovat testovací scénáře a fixture data | 003,006 / B P0 | Čistá i kontrolovaná použitá VM; syntetické logy, identita, změna portu a souboru, bez reálných tajemství. |
| DEV-008 | Navrhnout release a konfigurační kontrakt | 003,004 / B P1 | SemVer, pinning, manifest, SBOM, ADR k podpisu; žádný release klíč v repo. |

## 3. E1 – Sentinel Core

| ID | Úkol | Závislost / stav | Akceptace |
|---|---|---|---|
| DEV-009 | Datový kontrakt reportu a host inventory v1 | 003 / B P0 | Verzované JSON schema, host ID, čas, stav, stáří, zdroj a důkaz; validní i nevalidní fixture. |
| DEV-010 | Read-only Ansible preflight | 005,006,009 / B P0 | Zjistí OS, účty, SSH, cgroups, firewall, IPv4/IPv6, porty, Docker, disky, konzoli jako potvrzení vlastníka; neprovádí změny; neznámý stav explicitní. |
| DEV-011 | Konfigurační profil a inventory | 009,010 / B P0 | Bezpečné výchozí hodnoty, schéma, unikátní host ID, žádné klíče v Git; chybné konfigurace odmítnuty. |
| DEV-012 | Ansible role pro uživatele a izolaci | 010,011 / B P0 | Samostatný účet sentinel bez sudo/docker skupiny, unikátní subuid/subgid; nepřepíše admin ani SSH. AT-02. |
| DEV-013 | Minimální bezpečnostní baseline Debianu | 010,011 / B P0 | Návrh změn, konflikty a rollback; v laboratoři ověřená konzole a klíčový přístup, žádné plošné přepsání firewallu. |
| DEV-014 | Rootless Docker runtime a limity | 012,013 / B P0 | Ověřená instalace/izolace bez root socketu, cgroups v2 a limity účtu; test CPU/RAM/PID, disk a mounty. AT-02/03. |
| DEV-015 | Collector – základní metriky a reporty | 009,011,012 / B P0 | Deterministický sběr CPU/RAM/disk/uptime/služby, timeout, stav unknown/failed, schéma a sanitizace. Žádné AI. |
| DEV-016 | Chráněné úložiště a audit | 012,015 / B P0 | Autoritativní data mimo zápis agenta, omezený read-only export, rotace/limity logů, čas a test přístupů. |
| DEV-017 | Plánování a stav služeb | 015,016 / B P0 | Nezávislý timer/health check, zámky, timeout, timestamp posledního úspěchu, detekce vynechaného běhu. |
| DEV-018 | CLI install/status/doctor – laboratorní MVP rozhraní | 010,011,015,017 / B P1 | Plán/diagnostika a řízený instalační vstup vlastníka, žádné libovolné AI příkazy ani sudo. |
| DEV-019 | Test opakované instalace a konfliktů | 012–018 / B P0 | Dvakrát čistá VM a použitá testovací VM; druhý běh bez neočekávaných změn, zachované služby a firewall. AT-01. |
| DEV-020 | Test bezpečnostních hranic a výpadků | 014–019 / B P0 | Pokusy z účtu sentinel o zápis do politiky, auditů a cizích dat odmítnuty; sběr pokračuje bez agenta; AT-02/03/04 relevantní části. |
| DEV-021 | První laboratorní tag Core 0.0.x | 005,019,020 / B P1 | Výsledky testů, changelog, manifest závislostí, dokumentace instalace a ruční obnovy, samostatný souhlas před dalším krokem. |

## 4. Návrh minimálního repozitáře

```text
sentinel/
  README.md
  CONTRIBUTING.md
  SECURITY.md
  CHANGELOG.md
  .gitignore
  .github/workflows/
  docs/adr/
  schemas/
  ansible/{roles,playbooks,inventories}/
  installer/
  collector/
  security/
  agent/
  model-router/
  executor/
  approval-gateway/
  update-manager/
  control-center/
  config/
  tests/{unit,integration,fixtures}/
  releases/
```

Skutečné soubory a adresářové členění vzniknou až v repozitáři. Inventáře a tajemství se vedou bezpečně mimo zdrojový kód. Vývojářské nástroje a knihovny zvolit postupně podle ADR a testů.

## 5. První konkrétní programátorské zadání po E0

DEV-009 + DEV-010: připravit verzované JSON schéma a neprivilegovaný/read-only preflight, který pro každý údaj uvede hodnotu nebo unknown, čas a zdroj. Práce na samostatné větvi, syntetické fixtures, automatizované testy. Zákaz instalace balíčků, změny účtů, restartu služeb, změny sítě, spuštění skenu mimo schválený inventář a získávání tajemství. Výstupem PR, testy a krátká dokumentace pro vlastníka, nikoli změny na VPS.

## 6. Definition of Done

Přesný rozsah a závislosti, kód v PR, testy pozitivní i negativní, bezpečnostní dopady a změny konfigurace zdokumentované, žádná tajemství, relevantní akceptační kritéria MASTER doložena, revize dle rizika. Neprovádět produkční nasazení jen proto, že je úkol v GitHubu uzavřený.

Nejdříve DEV-001 a DEV-006. Žádný GitHub účet, repozitář, VPS ani test nebyl vytvořen či ověřen tímto zadáním.
