# Sentinel

Opakovatelně nasaditelný AI správce Debian VPS s nezávislým monitoringem, bezpečnostními kontrolami, řízeným schvalováním a obnovou.

**Stav: E0 – inicializace vývoje.** Architektura MASTER 1.0 je schválena; žádná produkční instalace ani funkční Sentinel dosud neexistují.

## Řídicí dokumenty

- [MASTER 1.0](docs/architecture/MASTER-1.0.md) – závazná architektura.
- [Architektonická revize R1](docs/architecture/review-R1.md).
- [Implementation Roadmap](docs/implementation/roadmap.md).
- [Development Backlog E0/E1](docs/implementation/backlog-E0-E1.md).
- [AGENTS.md](AGENTS.md) – zadání a hranice pro vývojové agenty.
- [Projektová centrála Sentinel](https://chatgpt.com/space/page_6b05d3d2940c8191905229a60a82721d).

## První etapa

Nejprve privátní GitHub, CI, nezávislá odborná revize a izolovaný Debian. Poté read-only Ansible preflight a deterministický Collector. V MVP 0.1 nejsou delegované R2/R3 operace povoleny. Žádné spuštění instalačního skriptu ani připojení k produkci.

## Struktura

`ansible/`, `installer/`, `collector/`, `security/`, `agent/`, `model-router/`, `executor/`, `approval-gateway/`, `update-manager/`, `control-center/`, `schemas/`, `config/`, `tests/`, `docs/` a `releases/` odpovídají schváleným etapám. V této inicializaci obsahují pouze podklady a rezervované adresáře, nikoli hotové služby.

Vývoj probíhá v samostatných větvích prostřednictvím pull requestů. Hlavní větev a produkční vydání musí být řízeny vlastníkem. Viz [CONTRIBUTING.md](CONTRIBUTING.md) a [SECURITY.md](SECURITY.md).

Soukromý projekt; zatím bez zvolené veřejné licence.
