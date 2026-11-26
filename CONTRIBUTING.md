# Pravidla vývoje Sentinel

1. Vyber schválený DEV-xxx úkol a ověř závislosti.
2. Používej samostatnou větev, například `dev/DEV-010-readonly-preflight`.
3. Commit popisuje konkrétní změnu a ID úkolu.
4. PR obsahuje účel, rozsah, výsledky testů, bezpečnostní dopady, návratový postup a odkazy na relevantní požadavky MASTER/AT.
5. Před merge musí být splněny požadované kontroly a revize. Citlivý kód vyžaduje nezávislou odbornou lidskou revizi; AI review ji nenahrazuje.
6. Vydání, podpis, distribuce a produkční nasazení jsou samostatné schvalované kroky.

Vlastník potvrdil tarif GitHub Free a aktivaci 2FA, Passkey i ověřené přihlášení dne 26. 11. 2026; nastavení účtu nebylo nezávisle kontrolováno. Privátní repozitář byl ověřen. Technicky vynutitelná ochrana hlavní větve (nebo ověřený ekvivalent), nezávislá odborná revize a CI zůstávají otevřené. Organizační pravidlo PR samo nenahrazuje technickou ochranu. Přípravný dokumentační PR smí být sloučen pouze po výslovném schválení vlastníka; tím se neuzavírá E0. Před uzavřením E0 musí být odpovídající bezpečnostní brány doloženy. Žádné produkční vydání bez splnění dalších příslušných bran.

## Definition of Done

Doložený rozsah, PR, skutečné pozitivní a negativní testy, bezpečnostní dopady, žádné tajemství, relevantní akceptace a revize dle rizika. Stav issue nesmí nahrazovat výsledky testů.
