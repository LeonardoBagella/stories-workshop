# 01 — Requirements Register

> Registro dei requisiti **funzionali (RF)** e **non funzionali (RNF)**, de-duplicati,
> con ID stabile, priorità (MoSCoW), flag MVP e tracciabilità alle fonti.
>
> **Legenda priorità**: `M` = Must, `S` = Should, `C` = Could, `W` = Won't (per ora).
> **MVP**: ✅ incluso nel primo semilavorato testabile · 🔜 release successiva.
> **Fonti**: US#n = user story della tabella del documento Boosha · PITCH = pitch deck ·
> THREAD = thread email "Re: Workshop AI" · WORD = corpo del documento Boosha.

---

## 1. Requisiti Funzionali (RF)

### 1.1 Conversazione & Amico virtuale

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-01 | L'anziano conversa **a voce** con un amico virtuale AI in italiano. | M | ✅ | PITCH, THREAD |
| RF-02 | La conversazione intrattiene l'anziano **sui suoi interessi** (topic IN), risultando ingaggiante e piacevole. | M | ✅ | US#1 |
| RF-03 | La comunicazione è **fluida ed empatica**, come con qualcuno interessato all'anziano. | M | ✅ | US#2 |
| RF-04 | L'amico virtuale **dichiara di essere un'AI** all'inizio e periodicamente ("ogni tanto"). | M | ✅ | PITCH, THREAD, AI-Act |
| RF-05 | L'AI genera a **ogni conversazione** un **sunto strutturato per topic**, filtrato dai guardrail. | M | ✅ | THREAD |
| RF-06 | L'AI attinge alla **memoria affettiva** per contestualizzare (recupero per argomento: es. "se si parla di calcio, il contesto si ricostruisce sui topic di calcio"). | M | 🔜 | THREAD, US#3 |
| RF-07 | L'AI riconosce lo **stato emotivo solo se dichiarato a parole** dall'anziano (nessuna inferenza biometrica). | M | ✅ | WORD, THREAD |
| RF-08 | L'AI gestisce i **temi sensibili** secondo la policy dei guardrail (cautela, frasi di rifiuto, regole di memoria). | M | ✅ | WORD, THREAD |

### 1.2 Adattività

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-09 | L'app **adegua il modo di interagire** all'anziano (parametri di adattività), per farne recepire i contenuti senza difficoltà. | M | ✅ | US#5 |
| RF-10 | La **velocità del parlato** (TTS) è regolabile (RNF-9 nel doc originale, qui trattata come funzione pilotabile). | M | ✅ | THREAD (RNF-9) |
| RF-11 | Sono pilotabili ulteriori parametri di adattività (lessico, lunghezza frasi, tono, pause) — set di valori da definire. | S | 🔜 | THREAD |

### 1.3 Memoria affettiva & contenuti condivisi

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-12 | La famiglia invia all'anziano una **foto di un momento felice** con **aiuti all'interpretazione**, per stimolare la memoria e capire cosa ricorda dell'evento. | M | 🔜 (primo caso concreto al workshop) | US#3, THREAD |
| RF-13 | STORIES propone all'anziano, **in differita** e in vari momenti della giornata, le **foto condivise** dalla famiglia, stimolando il ricordo **senza generare frustrazione**. | S | 🔜 | WORD, THREAD |
| RF-14 | Il **nipote** invia alla nonna/nonno una **foto di un momento speciale** per renderlo/a partecipe. | S | 🔜 | US (Nipote) |
| RF-15 | I contenuti condivisi (foto/video/audio/testo) alimentano la **memoria affettiva** classificata per topic. | M | 🔜 | WORD, THREAD |

### 1.4 Comunicazione asincrona anziano ↔ famiglia

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-16 | L'anziano invia un **audio** alla famiglia per condividere informazioni ed emozioni. | S | 🔜 | US (Anziano) |
| RF-17 | La famiglia invia un **audio** all'anziano, riprodotto da STORIES; possibile risposta. | S | 🔜 | US (Figlia) |
| RF-18 | La famiglia scrive un **messaggio di testo** in conversazione, letto da STORIES all'anziano. | S | 🔜 | US (Figlia) |
| RF-19 | La famiglia abilita la **trascrizione degli audio in ricezione** per leggerli quando non può ascoltarli. | C | 🔜 | US (Figlia) |
| RF-20 | Il nipote riceve **audio** dalla nonna/nonno (feedback sui contenuti condivisi). | C | 🔜 | US (Nipote) |

### 1.5 Reminder

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-21 | La famiglia imposta **reminder** per l'anziano (pillole, appuntamenti, orari) con **frequenza** di riproduzione. | S | 🔜 | US#6-area, WORD |
| RF-22 | Il sistema raccoglie un **feedback** sull'avvenuta ricezione/esecuzione del reminder. | S | 🔜 | US (Figlia) |
| RF-23 | La famiglia **disabilita gli avvisi** in determinati giorni/orari (per non disturbare il riposo). | S | 🔜 | US#6 |

### 1.6 Monitoraggio famiglia, avvisi ed emergenza

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-24 | La famiglia riceve un **sunto dei topic** trattati nella giornata e un **log delle interazioni** (lista topic), **non** il contenuto delle conversazioni. | M | ✅ | US#4, THREAD |
| RF-25 | Su **disagio dichiarato persistente** o **segnali di tutela**, il sistema **avvisa la famiglia** (senza indagare oltre). | M | ✅ | WORD, THREAD |
| RF-26 | Una **"Richiesta di aiuto"/emergenza** (frasi/situazioni configurate) attiva una **notifica immediata** ai destinatari e con i canali configurati; è previsto un **testo di conferma** all'anziano. | M | ✅ (da dettagliare frasi/testi/destinatari) | PITCH, THREAD |
| RF-27 | **Safeguarding**: segnali di abuso/trascuratezza/coercizione verso l'anziano **non vengono nascosti** alla famiglia (policy separata). | M | ✅ | WORD |

### 1.7 Identità, accesso e segregazione

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-28 | Il **sistema riconosce l'anziano**, così ogni anziano porta avanti le proprie conversazioni. | M | ✅ (nel PoC: per dispositivo/codice) | US (Anziano) |
| RF-29 | I contenuti restano **confinati alla famiglia** (segregazione/isolamento). | M | ✅ | US (tutti), THREAD |
| RF-30 | Le conversazioni dell'anziano restano **private** (non accessibili alla famiglia). | M | ✅ | US (Anziano), THREAD |
| RF-31 | Ogni famiglia è identificata da un **codice di accesso**; il sistema riconosce con quale codice si è entrati (anche per instradare le notifiche). | M | ✅ | THREAD |

### 1.8 Canale famiglia

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-32 | La famiglia/nipote comunica con STORIES tramite un'**app familiare** (Telegram confermato per l'MVP; WhatsApp alternativa scartata per semplicità). | M | ✅ | US (TECH), THREAD |
| RF-33 | Le **notifiche** al canale famiglia sono associabili/attivabili per singola famiglia; **spente di default** per i profili tester. | S | ✅ | THREAD |

### 1.9 Onboarding & consenso

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-34 | Alla **prima registrazione** gli utenti accettano le **clausole GDPR** e il modulo **Emergenza** (audio/video), con evidenza dell'accettazione. | M | ✅ (bozza; testo definitivo col legale al workshop) | US (Stories), THREAD |
| RF-35 | È previsto l'**opt-out** da determinati moduli AI (es. analisi/condivisione con la famiglia). | S | 🔜 | PITCH, WORD |

### 1.10 Metriche & osservabilità (misure di successo)

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RF-36 | **Anziano** — misurare: quante volte STORIES intercetta domande già fatte/simili in un arco di tempo; tempo speso in conversazione; quante volte l'anziano non viene compreso; quante volte l'anziano non comprende STORIES; feedback sulle interazioni. | M | ✅ | WORD (metriche) |
| RF-37 | **Figlia/caregiver** — misurare: quante volte interagisce autonomamente; quante volte risponde a un sollecito; feedback di soddisfazione. | M | ✅ | WORD (metriche) |
| RF-38 | **Investitore** — misurare: **tempo totale giornaliero** in conversazione sulla piattaforma; **numero utenti giornalieri** attivi. | M | ✅ | WORD (metriche) |
| RF-39 | Esiste una vista "**Come sta andando**" che aggrega le metriche di qualità dell'interazione (tempo in conversazione, richieste di ripetere, incomprensioni). | S | ✅ | THREAD |

---

## 2. Requisiti Non Funzionali (RNF)

### 2.1 Privacy & sicurezza dei dati

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RNF-01 | **L'audio non transita/persiste sui server**: dalla app arriva già la trascrizione (target on-device); per l'MVP è ammessa trascrizione lato server **con audio scartato immediatamente** (vedi ADR STT). | M | ✅ | US (TECH), THREAD |
| RNF-02 | **Minimizzazione dei dati**: non si salvano dati sensibili; si memorizzano **solo testo e immagini condivise**, mai audio, mai le categorie escluse dai guardrail. | M | ✅ | PITCH, THREAD |
| RNF-03 | **Isolamento per famiglia** (multi-tenant): i dati di una famiglia non sono mai accessibili ad altre. | M | ✅ | THREAD, US |
| RNF-04 | **Cifratura** dei dati in transito e a riposo (obiettivo e2e / AES-256; cloud europeo). | M | ✅ (transito) / 🔜 (a riposo target) | PITCH |
| RNF-05 | **Residenza dei dati in UE** (infrastruttura e storage europei). | M | ✅ | PITCH, THREAD |
| RNF-06 | Le conversazioni sono uno **spazio privato e sicuro** per l'anziano (riservatezza verso la famiglia). | M | ✅ | US (Anziano) |

### 2.2 Compliance (GDPR & EU AI Act)

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RNF-07 | Conformità **GDPR**: base giuridica del consenso, registro dei trattamenti, gestione data breach, DPA con i fornitori. | M | Parziale MVP / 🔜 pieno | PITCH |
| RNF-08 | Conformità **EU AI Act** per "utente fragile": trasparenza, **no sfruttamento delle vulnerabilità**, no manipolazione/dipendenza emotiva, supervisione umana sulle segnalazioni. | M | ✅ (principi) | PITCH, WORD |
| RNF-09 | **AI Decision Log** / tracciabilità delle decisioni AI e alerting anomalie. | S | 🔜 | PITCH |
| RNF-10 | Governance: **DPO** e **Comitato Etico**; audit periodici su bias/sicurezza/impatto. | S | 🔜 | PITCH |

### 2.3 Accessibilità & adattività

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RNF-11 | Interfaccia anziano **voice-first**, **priva di elementi che richiedono input manuali** complessi. | M | ✅ | PITCH |
| RNF-12 | Linguaggio **accessibile** per anziani e caregiver; audio-guida, icone chiare, alto contrasto. | M | ✅ | PITCH, WORD |
| RNF-13 | Consensi e comunicazioni privacy **semplificati** (spiegazioni vocali, conferme multiple per azioni sensibili). | S | 🔜 | PITCH, WORD |

### 2.4 Prestazioni & affidabilità

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RNF-14 | **Bassa latenza** conversazionale per un'esperienza fluida (budget end-to-end da definire nel doc di fattibilità). | M | ✅ | THREAD (evitare latenze) |
| RNF-15 | Le notifiche sensibili (es. "non mi sento bene") attivano **allarmi reali, non falsi positivi**. | M | ✅ | PITCH, WORD |
| RNF-16 | Disponibilità di un **fallback** LLM (non ritenuto fondamentale per l'MVP salvo latenze importanti). | C | 🔜 | THREAD |

### 2.5 Portabilità & evoluzione

| ID | Requisito | Prio | MVP | Fonte |
|---|---|---|---|---|
| RNF-17 | Il layer LLM/voce è **astratto e sostituibile** (i modelli evolvono rapidamente; nessun lock-in rigido). | S | ✅ | Fattibilità |
| RNF-18 | Architettura predisposta all'**evoluzione multi-dispositivo** con dati segregati per famiglia (target server EU con DB + DB vettoriale + object storage). | M | 🔜 | THREAD |
| RNF-19 | Predisposizione roadmap: **dashboard RSA** (R2) e **integrazione medicale/VAD** (R3). | C | 🔜 | PITCH |

---

## 3. Note di de-duplicazione e conflitti risolti

- **US "TECH" duplicate** nel documento originale (alcune righe della tabella con celle vuote): consolidate in RF-28/29/30/31/32 e RNF-01.
- **Emozione**: il pitch (analisi vocale) e il Word (solo se dichiarata) confliggono →
  risolto a favore del Word per l'MVP (RF-07); l'analisi vocale resta **roadmap** con
  opt-in esplicito (vedi ADR e `03`).
- **STT on-device vs server**: conflitto US-TECH (on-device) vs proposta MVP Boosha
  (server, audio scartato) → gestito da RNF-01 + ADR dedicato (`06-adr`).
- **Canale famiglia**: WhatsApp vs Telegram → Telegram per l'MVP (RF-32).
- Le **metriche** del documento originale sono state ridotte/ricalibrate come indicato
  nel thread ("metriche ridotte"): mantenute quelle in RF-36..39.
