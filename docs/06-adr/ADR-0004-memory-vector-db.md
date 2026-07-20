# ADR-0004 — Memoria: Postgres+pgvector → Qdrant; embeddings EU

**Stato**: Accettato (MVP) · **Data**: 2026-07 · **Rif.**: RF-05/06/15, RNF-03/18, `03` §5

## Contesto

Serve una **memoria affettiva** a lungo termine, recuperabile **per topic**, con
**segregazione rigorosa per famiglia** e **residenza UE**. Il PoC genera già i sunti
strutturati per topic; manca la persistenza server (tenuta a zero per prudenza GDPR). La
ricerca (`03` §5) indica un pattern episodica→sunto→topic e opzioni di vector DB con
hosting UE.

## Decisione

- **MVP**: **Postgres + pgvector** self-host in UE. `family_id` come chiave di tenancy con
  **Row-Level Security** (`FORCE ROW LEVEL SECURITY`); colonna `topic` + `JSONB` per i
  sunti; indice **HNSW**. Un solo motore riduce la complessità operativa.
- **Embeddings**: **BGE-M3** (MIT, self-host UE, multilingue IT) o **Mistral Embed**
  (gestito EU-native) come fallback. **Evitare OpenAI embeddings** (Data Zone UE non li
  copre in modo confermato).
- **Object storage**: S3-compatibile UE (ADR-0005) per foto/video.
- **Target**: se il volume cresce, parte vettoriale su **Qdrant** (Hybrid Cloud UE su
  OVHcloud/Scaleway), Postgres per i dati relazionali. Il pattern applicativo è
  indipendente dal DB → migrazione a basso rischio.
- **Filtro pre-scrittura obbligatorio** (`02` §9 liv. 2): applica le "Azioni sulla memoria"
  prima di chunking/embedding; mai indicizzare audio; log immutabile degli scarti.

## Alternative considerate

- **Pinecone**: buono ma **società US → rischio CLOUD Act** anche con dati in UE.
- **Weaviate/Milvus**: validi, ma pgvector è sufficiente e più semplice per l'MVP.
- **Framework agent-memory chiusi** (Mem0/Zep/Letta): utili come ispirazione del pattern,
  non adottati 1:1 (nessuno è pensato per multi-tenant famiglia IT con guardrail sanitari).

## Conseguenze

- ✅ Isolamento "a livello DB" (RLS), semplicità operativa, costi bassi, UE.
- ⚠️ pgvector scala bene fino a ~10M vettori → oltre, migrare a Qdrant.
- ⚠️ Isolamento cross-tenant va **testato** con test automatici dedicati.
- 🔁 Reversibile grazie all'astrazione del layer di memoria.
