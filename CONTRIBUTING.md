# Pravidla vývoje Sentinel

1. Vyber schválený DEV-xxx úkol a ověř závislosti.
2. Používej samostatnou větev, například `dev/DEV-010-readonly-preflight`.
3. Commit popisuje konkrétní změnu a ID úkolu.
4. PR obsahuje účel, rozsah, výsledky testů, bezpečnostní dopady, návratový postup a odkazy na relevantní požadavky MASTER/AT.
5. Před merge musí být splněny požadované kontroly a revize. Citlivý kód vyžaduje nezávislou odbornou lidskou revizi; AI review ji nenahrazuje.
6. Vydání, podpis, distribuce a produkční nasazení jsou samostatné schvalované kroky.

Nastavení GitHub ochrany větví, nezávislý reviewer, CI a tarif zatím nebyly ověřeny. Dokud nejsou nastaveny odpovídající brány, žádné produkční vydání.

## Definition of Done

Doložený rozsah, PR, skutečné pozitivní a negativní testy, bezpečnostní dopady, žádné tajemství, relevantní akceptace a revize dle rizika. Stav issue nesmí nahrazovat výsledky testů.
