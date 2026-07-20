# STORIES — Implementation Plan

> Piano di implementazione **MVP-first**, funzionale allo sviluppo completo
> dell'applicazione. Sequenzia fasi, workstream, deliverable e criteri di uscita, partendo
> dallo stato attuale (PoC pre-workshop) fino alla roadmap R1/R2/R3.
>
> Riferimento normativo-tecnico: [`spec.md`](spec.md) e `docs/00`–`06`.
> Versione: **1.0 (luglio 2026)**.

---

## 1. Approccio

- **MVP-first**: prima un semilavorato funzionante e testabile (con anziani reali e
  investitori), poi estensione per release.
- **Reversibilità**: ogni scelta MVP è dietro un **layer di astrazione** (RNF-17) per
  poter migrare verso le opzioni target senza refactoring ampio.
- **Compliance & sicurezza come workstream trasversale**, non come fase finale.
- **Validazione continua**: metriche oggettive + feedback dei tester a ogni iterazione.

---

## 2. Stato attuale (punto di partenza)

Il PoC pre-workshop esiste già ed è online:

- Web app (Next.js/Vercel) + **bot Telegram** per il canale famiglia.
- Conversazione basata su **Anthropic Claude**.
- **Segregazione a livello di dispositivo** (zero dati personali sul server).
- **Guardrail sui temi sensibili già implementati**; **sunti per topic** generati a ogni
  conversazione; metriche base ("Come sta andando").
- Codice condiviso su repository Git (gestito da Boosha); accessi tester predisposti.

> Questo piano **estende** il PoC verso la visione completa; non riparte da zero.

---

## 3. Fasi

### Fase 0 — Fondamenta & sblocchi (in corso / immediata)

**Obiettivo**: rimuovere i blocchi e consolidare le fondamenta di prodotto e compliance.

| Workstream | Attività | Deliverable |
|---|---|---|
| Sblocchi tecnici | Chiave API Anthropic (con spending cap); accessi GitHub del team | Ambiente operativo |
| Decisioni MVP | Conferma STT server EU (audio scartato), canale Telegram, provider cloud UE | ADR chiusi (`docs/06`) |
| Documentazione | spec + documenti `00`–`06` (questo repo) | Base condivisa |
| Compliance (avvio) | Bozza consenso GDPR + Emergenza; avvio DPIA; classificazione AI Act | Bozze col legale |

**Criteri di uscita**: chiave API attiva; decisioni architetturali confermate; team con
accesso; bozza consenso disponibile.

### Fase 1 — MVP (semilavorato testabile) — *workshop + immediati dintorni*

**Obiettivo**: conversazione empatica voce-first robusta, guardrail, sunti alla famiglia,
avvisi/emergenza, metriche — testabile con 3-4 famiglie tester.

| Workstream | Attività | Rif. |
|---|---|---|
| Voce | Pipeline STT→LLM→TTS (Deepgram EU → Claude Sonnet 5 via Bedrock/Vertex EU → Azure HD it-IT); push-to-talk; audio scartato | ADR-0001/0002/0006 |
| Guardrail | Prompt di sistema a strati; **filtro di memorizzazione**; **classificatore di rischio** | `docs/02`, `docs/04` |
| Sunti & famiglia | Generazione sunto per topic; vista famiglia (lista topic, **non** contenuti); notifiche Telegram gestibili | RF-05/24/33 |
| Emergenza | "Richiesta di aiuto": frasi trigger, testo di conferma, destinatari (da definire al workshop) | RF-26 |
| Adattività | Velocità del parlato + parametri base impostabili in onboarding | RF-09/10 |
| Metriche | Tempo in conversazione, richieste di ripetere, incomprensioni; vista "Come sta andando" | RF-36..39 |
| Segregazione | Codice di accesso per famiglia; identificazione per notifiche | RF-28/31 |
| Compliance | Disclosure AI (Art. 50); consenso all'onboarding; audio non persistito | `docs/05` |
| Validazione | Test con 3-4 famiglie; 3 domande secche (ingaggio/voce/ritorno) | `docs/04` §9 |

**Criteri di uscita**: un anziano nuovo conduce conversazioni ingaggianti; la famiglia
vede i sunti dei topic; emergenza e avvisi funzionano senza falsi positivi; metriche
raccolte; feedback tester positivo.

### Fase 2 — R1: Famiglia & Caregiver (Q4 2026)

**Obiettivo**: memoria affettiva persistente e funzioni famiglia complete.

- **Memoria persistente lato server UE**: Postgres + pgvector, RLS per `family_id`,
  embeddings BGE-M3/Mistral; migrazione dalla segregazione device → contenitore famiglia
  (ADR-0004/0005).
- **Foto con aiuti all'interpretazione** (RF-12) — primo caso concreto co-progettato.
- **Proposta foto in differita** (RF-13); memoria per topic (RAG) attiva (RF-06).
- **Comunicazione asincrona** anziano↔famiglia (audio/testo, trascrizione) (RF-16..20).
- **Reminder** con frequenza e feedback; silenziamento orari (RF-21..23).
- **Multi-dispositivo** con dati segregati per famiglia (RNF-18).
- **Consenso/informativa definitivi**; DPIA completata; AI Decision Log (RNF-09).

**Criteri di uscita**: base utenti coinvolta e soddisfatta; memoria affettiva funzionante
end-to-end; conformità consolidata.

### Fase 3 — R2: RSA e Case di Cura (2027)

- Dashboard dedicata per strutture; report per familiari.
- Modello multi-struttura; ruoli operatori; nomina DPO; ISO 27701 (target).

### Fase 4 — R3: Integrazione Medicale & VAD (2028)

- Connessione con dispositivi di monitoraggio sanitario.
- **Voice Assistant Device** (Alexa-like) per non possessori di smartphone.
- Rivalutazione **alto rischio AI Act** se emergono funzioni sanitarie/di eligibilità.

---

## 4. Workstream trasversali (tutte le fasi)

| Workstream | Presidio |
|---|---|
| **Compliance & privacy** | Disclosure AI, consenso, DPIA, retention, DPA/ZDR, data breach runbook |
| **Sicurezza dati** | Isolamento per famiglia (RLS + test), cifratura, gestione chiavi UE |
| **Guardrail & safety** | Manutenzione tassonomia temi, filtro memoria, classificatore rischio, audit |
| **Qualità conversazionale** | Set di valutazione interno, A/B test, feedback tester |
| **Osservabilità** | Metriche di successo, vista "Come sta andando", AI Decision Log |
| **Astrazione tecnica** | Layer LLM/STT/TTS/memoria/canale sostituibili; review trimestrale |

---

## 5. Milestone (indicative)

| Milestone | Quando | Contenuto |
|---|---|---|
| Sessioni workshop | slot 20/24 luglio + 11/12 agosto 2026 | Co-progettazione MVP, casi concreti, architettura persistenza |
| MVP testabile | Q3 2026 | Fase 1 completa; test con famiglie |
| R1 | Q4 2026 | Memoria affettiva + funzioni famiglia |
| R2 | 2027 | RSA/Case di cura |
| R3 | 2028 | Medicale + VAD |

---

## 6. Dipendenze e blocchi

- **Chiave API Anthropic** (con spending cap) — prerequisito Fase 1.
- **Consenso/informativa col legale** — sblocca la persistenza dei dati personali (Fase 2).
- **Provider cloud UE** scelto — sblocca l'infrastruttura target.
- **Dataset di validazione** del parlato anziano reale — qualità STT.
- **DPA + ZDR** con il provider LLM — trasferimenti extra-UE.

---

## 7. Rischi principali e mitigazioni

Da [`docs/03` §9](docs/03-feasibility-and-tech-study.md):

| Rischio | Mitigazione |
|---|---|
| Residenza dati UE non nativa per LLM/STT | Bedrock/Vertex EU, ZDR, pseudonimizzazione; piano B EU-hosted |
| Safety layer LLM insufficienti sui rischi anziani | Guardrail applicativi obbligatori (liv. 2–3), non delegati al modello |
| WER ignoto su parlato anziano/dialetto | Dataset di validazione reale; Custom Speech/fine-tuning |
| Telegram fuori UE, no DPA | Minimizzazione, niente dati sanitari nel canale; exit strategy |
| PWA iOS senza voce background | Push-to-talk nell'MVP; migrazione a nativo nel target |
| Ciclo modelli rapidissimo | Layer astratto e versionato; review trimestrale |
| Scivolamento verso alto rischio AI Act | Scope control; valutazione Art. 6(4); revisione legale su nuove feature |
| Consenso per utenti con capacità ridotta | Linguaggio semplice, co-consenso familiare, DPIA |

---

## 8. Ruoli (dal contesto)

- **STORIES Srl** — product owner (Leonardo Bagella, Leonardo Anceschi); **Enrico** — CTO.
- **Boosha** — partner di sviluppo del workshop/PoC (Giada, Roberto, Luca).
- **Advisory Board** — neurologia, psicoterapia, geriatria; **DPO** e **Comitato Etico**
  (da nominare, target).
- **Tester** — anziani e famiglie tester (accessi già predisposti).

> Il presente piano è la base per lo sviluppo completo: le decisioni MVP verranno
> rivalidate al workshop e alla feasibility approfondita, mantenendo l'impianto tracciabile
> attraverso `spec.md` e gli ADR.
