# STORIES — Specifiche & Implementation Plan

> **Ricordi che uniscono.** App adattiva per supportare gli anziani e le loro famiglie,
> rafforzando memoria, connessione emotiva e benessere quotidiano tramite conversazioni
> empatiche con un amico virtuale AI e una memoria affettiva alimentata dalla famiglia.

Questo repository contiene le **specifiche** e il **piano di implementazione** di STORIES,
prodotti a partire dal pitch deck, dal documento funzionale del workshop Boosha e dal
thread di lavoro con Boosha (giu–lug 2026), integrati con uno **studio di fattibilità**
basato su ricerca web aggiornata a luglio 2026.

## Come leggere questa documentazione

Percorso consigliato — dal generale al dettaglio:

1. **[`spec.md`](spec.md)** — specifica consolidata (visione completa). **Punto di partenza.**
2. **[`IMPLEMENTATION_PLAN.md`](IMPLEMENTATION_PLAN.md)** — piano MVP-first + roadmap.

Documenti di dettaglio (fondamenta → decisioni), in `docs/`:

| # | Documento | Contenuto |
|---|---|---|
| 00 | [Glossario e Domain Model](docs/00-glossario-e-domain-model.md) | Linguaggio condiviso, personas, entità, invarianti |
| 01 | [Requirements Register](docs/01-requirements-register.md) | RF/RNF de-duplicati, tracciabili, flag MVP |
| 02 | [Guardrail & Safety Policy](docs/02-guardrail-safety-policy.md) | Temi sensibili, emergenza, safeguarding, trasparenza |
| 03 | [Studio di Fattibilità](docs/03-feasibility-and-tech-study.md) | Stato dell'arte lug 2026: LLM, STT, TTS, memoria, client, compliance |
| 04 | [Conversation & AI Design](docs/04-conversation-ai-design.md) | Prompt, adattività, memoria, sunti per topic |
| 05 | [Data & Privacy Architecture](docs/05-data-privacy-architecture.md) | Data-flow, retention, GDPR/AI-Act |
| 06 | [ADR](docs/06-adr/README.md) | 7 Architecture Decision Records |

## Sequenza logica di produzione

```
00 Glossario → 01 Requisiti → 02 Guardrail → 03 Fattibilità
   → 04 AI Design → 05 Data/Privacy → 06 ADR → spec.md → IMPLEMENTATION_PLAN.md
```

Lo studio di fattibilità (03) è collocato **dopo** i requisiti e i guardrail e **prima**
delle decisioni architetturali, così che le scelte tecniche (ADR) siano valutate contro
requisiti già formalizzati e restino tracciabili.

## Nota

I documenti riflettono lo stato **pre-workshop** (luglio 2026). Diverse decisioni MVP sono
esplicitamente **reversibili** e verranno rivalidate al workshop e in una feasibility
tecnica più approfondita. I contenuti di compliance non costituiscono parere legale e vanno
validati da legale/DPO.
