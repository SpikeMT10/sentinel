# Bezpečnostní pravidla

Sentinel je ve vývoji. Zatím nejsou vydány žádné podporované produkční verze.

## Hlášení

Bezpečnostní nálezy oznamuj soukromě vlastníkovi repozitáře (SpikeMT10). Do issue nebo PR nevkládej skutečné klíče, hesla, neveřejné IP adresy, citlivá data ani reprodukci s produkčními údaji. Zveřejnění nálezu vyžaduje posouzení vlastníka. Konkrétní soukromý kanál bude potvrzen v E0.

## Bezpečnostní hranice

Závazná pravidla jsou v [MASTER 1.0](docs/architecture/MASTER-1.0.md). Agent není autorizační autorita. Testování probíhá v izolované VM. Bez úspěšných příslušných AT testů a souhlasu vlastníka se nesmí nasadit produkční vydání nebo aktivovat R2/R3.

## Tajemství

Nepřidávej skutečné inventáře, vault hesla, tokeny, SSH klíče, release klíče, kopie záloh ani surové logy. `.gitignore` je pomocná ochrana, nikoli bezpečnostní hranice. Při úniku klíče okamžitě informuj vlastníka; odvolání/rotace a vyšetření jsou řízené kroky.
