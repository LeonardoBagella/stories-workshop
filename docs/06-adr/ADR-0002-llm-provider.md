# ADR-0002 — LLM: Claude Sonnet 5 via Bedrock/Vertex EU

**Stato**: Accettato (MVP) · **Data**: 2026-07 · **Rif.**: RF-01..08, RNF-05/17, `03` §2

## Contesto

Il PoC usa **Anthropic Claude**. Serve un LLM con ottima qualità in italiano, forte
steerability (per i guardrail), output strutturato nativo (per i sunti-topic), bassa
latenza e **residenza dati UE**. La ricerca (`03` §2) evidenzia che nessun provider
proprietario offre residenza UE nativa sull'API diretta; per Claude `inference_geo`
supporta solo `us`/`global`. Il benchmark GrandGuard mostra che i safety layer nativi
falliscono su >50% dei rischi anziani.

## Decisione

- **MVP**: **Claude Sonnet 5** come modello primario, servito via **AWS Bedrock EU**
  (Irlanda `eu-west-1` / Stoccolma `eu-north-1`) o **Google Vertex AI EU** (Francoforte)
  per garantire residenza dati UE. **Fallback**: Claude Haiku 4.5 (stessa infra, per
  costo/latenza).
- **Target**: routing a due modelli — Sonnet 5 per il traffico ordinario, **Opus 4.8**
  per conversazioni delicate (disagio/escalation); monitorare **Mistral Large 3** come
  opzione di sovranità totale UE.
- Al provider LLM va **solo testo pseudonimizzato**, con **DPA+SCC** e richiesta di **Zero
  Data Retention**; nessun opt-in al training.
- Il layer LLM è **astratto e versionato**: nessun ID di modello hard-coded (RNF-17).

## Alternative considerate

- **OpenAI GPT-5.6** (EU residency per progetto): valida, ma discontinuità col PoC.
- **Gemini 3.x**: buono, ma manca endpoint mono-regione UE per i modelli più recenti.
- **Mistral (UE-nativo)**: miglior residenza dati, ma guardrail/qualità empatica IT non
  documentati al livello dei big → richiede validazione dedicata (candidato target).
- **Llama self-host**: **non utilizzabile** (licenza esclude soggetti UE).

## Conseguenze

- ✅ Continuità col PoC, qualità/costo ottimi, output strutturato per i sunti.
- ✅ Residenza UE via hyperscaler senza attendere una regione UE nativa di Anthropic.
- ⚠️ Dipendenza da guardrail applicativi (`02` liv. 2–3): la sicurezza **non** è delegata
  al modello.
- 🔁 Reversibile: astrazione + review trimestrale per seguire il ciclo modelli rapido.
