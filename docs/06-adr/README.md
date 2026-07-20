# 06 — Architecture Decision Records (ADR)

Decisioni architetturali di STORIES, motivate dallo studio di fattibilità (`../03`).
Ogni ADR distingue la scelta **MVP** dalla scelta **Target** e resta **reversibile**
grazie ai layer di astrazione (RNF-17).

| ADR | Titolo | Stato |
|---|---|---|
| [ADR-0001](./ADR-0001-stt-strategy.md) | Strategia STT: server EU (MVP) → on-device (target) | Accettato |
| [ADR-0002](./ADR-0002-llm-provider.md) | LLM: Claude Sonnet 5 via Bedrock/Vertex EU | Accettato |
| [ADR-0003](./ADR-0003-client-architecture.md) | Client anziano: PWA (MVP) → nativo Flutter (target) | Accettato |
| [ADR-0004](./ADR-0004-memory-vector-db.md) | Memoria: Postgres+pgvector → Qdrant; embeddings EU | Accettato |
| [ADR-0005](./ADR-0005-eu-infrastructure.md) | Infrastruttura e storage in UE | Accettato |
| [ADR-0006](./ADR-0006-voice-pipeline.md) | Voce: pipeline modulare STT→LLM→TTS | Accettato |
| [ADR-0007](./ADR-0007-family-channel.md) | Canale famiglia: Telegram Bot + Mini App | Accettato |

> **Convenzione**: "Accettato per l'MVP" significa che la decisione è valida per il primo
> semilavorato e verrà **rivalidata** alla feasibility approfondita e alle review
> trimestrali. Nessun ID di modello è hard-coded nel codice applicativo.
