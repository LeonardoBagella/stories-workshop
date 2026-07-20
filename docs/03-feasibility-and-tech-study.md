# 03 — Studio di Fattibilità e Stato dell'Arte (luglio 2026)

> Analisi di fattibilità tecnica funzionale alla decisione su **architettura e
> componenti**. Sintetizza una ricerca web condotta a **luglio 2026** su 6 filoni
> paralleli (LLM conversazionale, STT, TTS/voce realtime, memoria/RAG/vector DB,
> client & canale famiglia, compliance & infra UE).
>
> **Metodo e caveat**: la ricerca privilegia fonti 2026 (documentazione vendor, testi
> normativi, blog di settore, paper). Dove un dato non è verificabile è **segnalato
> esplicitamente**. Il ciclo di rilascio dei modelli è rapidissimo (Claude Sonnet 5 e
> GPT-5.6 usciti nelle settimane precedenti): **ogni scelta di modello va rivalidata a
> ogni review trimestrale**. Questo documento **non** è parere legale vincolante:
> classificazione AI Act, base giuridica GDPR e contratti fornitori vanno validati da
> un legale/DPO.

---

## 1. Sintesi esecutiva — Stack raccomandato

Il PoC esistente (web app Next.js/Vercel + bot Telegram + Claude) è **una base valida**.
La feasibility conferma la fattibilità del prodotto e indica un percorso **MVP → target**
che riduce il rischio di compliance e di lock-in.

| Layer | Raccomandazione **MVP** (0–6 mesi) | Raccomandazione **Target** (12–24 mesi) | ADR |
|---|---|---|---|
| **LLM conversazionale** | **Claude Sonnet 5** via **AWS Bedrock EU** (Irlanda/Stoccolma) o **Vertex AI EU** (Francoforte) per residenza dati; fallback **Claude Haiku 4.5** | Routing a 2 modelli (Opus 4.8 per casi delicati); valutare **Mistral Large 3** per sovranità totale UE | ADR-0002 |
| **STT** | **Deepgram Nova-3** endpoint EU (streaming, zero-retention), audio scartato subito; alt. **ElevenLabs Scribe v2** (EU + Zero Retention) | **On-device**: Apple SpeechAnalyzer (iOS) + Whisper/**Voxtral** (Android) | ADR-0001 |
| **TTS** | **Azure Neural/HD it-IT** (SSML per velocità/pause: requisito adattività) | **ElevenLabs v3** (EU Data Residency) per calore emotivo | ADR-0006 |
| **Architettura voce** | **Pipeline modulare** STT→LLM→TTS (Pipecat/LiveKit) — testo ispezionabile per guardrail e sunti | Idem (mai speech-to-speech nativo: perde ispezionabilità) | ADR-0006 |
| **Memoria** | **Postgres + pgvector** self-host UE (RLS per `family_id`); embeddings **BGE-M3** self-host o **Mistral Embed** | **Qdrant** (Hybrid Cloud UE) se scala; pattern episodica→sunto→topic | ADR-0004 |
| **Object storage** | S3-compatibile UE (OVHcloud/Scaleway) | Idem | ADR-0005 |
| **Client anziano** | **PWA Next.js** (riuso PoC) con push-to-talk + STT cloud EU | **Nativo (Flutter)** per STT on-device, wake-word, multi-utente | ADR-0003 |
| **Canale famiglia** | **Telegram Bot** (+ Mini App) | Idem, con exit strategy verso canale UE-hosted se scala | ADR-0007 |
| **Infra** | Provider UE (**OVHcloud / Scaleway / Aruba / IONOS**), TLS 1.3, chiavi in UE | + AWS European Sovereign Cloud / ISO 27701 | ADR-0005 |

---

## 2. LLM conversazionale

**Stato dell'arte (lug 2026).** Famiglia Anthropic Claude: `claude-opus-4-8` (top-tier
affidabile), `claude-sonnet-5` (nuovo default, qualità quasi-Opus a costo Sonnet),
`claude-haiku-4-5` (fast/economico), `claude-fable-5` (ragionamento estremo,
sovradimensionato per questo caso). Alternative: OpenAI **GPT-5.6** (Sol/Terra/Luna),
Google **Gemini 3.1 Pro / 3.5 Flash**, **Mistral Large 3** (opzione UE-sovrana).
**Llama non è utilizzabile in UE** (licenza Llama 4 esclude soggetti UE).

| Modello | Qualità IT | Steerability | Output strutt. | Costo $/Mtok (in/out) | Residenza UE |
|---|---|---|---|---|---|
| Claude Sonnet 5 | Alta | Alta | Nativo | ~2/10 (promo→3/15) | via Bedrock/Vertex EU |
| Claude Haiku 4.5 | Media-alta | Alta | Nativo | ~1/5 | via Bedrock/Vertex EU |
| Claude Opus 4.8 | Alta | Molto alta | Nativo | ~5/25 | via Bedrock/Vertex EU |
| GPT-5.6 Terra | Alta | Buona | Nativo | ~2.5/15 | EU residency (+~10%) |
| Gemini 3.1 Pro | Alta | Buona | Nativo | ~2/12 | EU multi-region (no mono-region recente) |
| Mistral Large 3 | Media (IT non verif.) | Media | Sì | ~0.5/1.5 | **Nativa (Parigi)** |

**Verdetto.** Fattibile. **Vincolo chiave**: nessun provider proprietario offre residenza
UE nativa sull'API diretta; per Claude, `inference_geo` supporta solo `us`/`global` → la
via UE verificabile è **passare da AWS Bedrock o Google Vertex in regione UE**.
**Raccomandazione MVP**: mantenere **Claude Sonnet 5** (continuità col PoC, ottimo
qualità/costo, forte steerability e output strutturato per i sunti-topic) servito via
Bedrock/Vertex EU; fallback Haiku 4.5. **Target**: routing a due modelli (Opus per
disagio/escalation); tenere d'occhio Mistral per sovranità totale.

**Rischio critico.** Il benchmark **GrandGuard (2026)** mostra che i safety layer nativi
degli LLM leader gestiscono male **>50%** dei rischi specifici degli anziani. → I
guardrail applicativi (`02`, livelli 2 e 3) sono **obbligatori**, non delegabili al modello.

---

## 3. Speech-to-Text (STT)

**Stato dell'arte (lug 2026).** Salto qualitativo on-device: **Apple SpeechAnalyzer**
(iOS 26, WER 2,12% EN pulito, ~2× Whisper turbo). **Whisper large-v3-turbo** resta lo
standard open on-device. **Voxtral** (Mistral, Apache 2.0, mar 2026) multilingue nativo
IT con variante edge "Realtime 4B". Lato API: **Deepgram Nova-3** (endpoint EU GA,
zero-retention default, miglioramenti IT dialettale), **ElevenLabs Scribe v2** (WER IT
3,1% FLEURS / 5,5% Common Voice, EU + Zero Retention), AssemblyAI, Azure, Google.

**Caveat forte.** Tutti i WER pubblicati sono su parlato **pulito/standard**, non su
**parlato anziano reale** (voce lenta, tremore, dialetto, rumore domestico). Nessun dato
IT specifico su popolazione anziana reperibile. Inoltre lo **streaming realtime
on-device su Android mid-range è oggi problematico** (latenza crescente, crash) → nel
breve la modalità realistica è **batch/chunked** (frasi di pochi secondi).

**Verdetto.** **MVP**: **Deepgram Nova-3** endpoint EU (streaming maturo, zero-retention,
IT dialettale), audio scartato subito dopo la trascrizione; alternativa **ElevenLabs
Scribe v2**. **Evitare OpenAI** come ancora dell'MVP (cicli di dismissione rapidi:
gpt-4o-transcribe già in retirement). **Target on-device**: Apple SpeechAnalyzer su iOS,
Whisper/Voxtral su Android. Coerente con RNF-01 (audio mai persistito) e con l'ADR STT.

**Mitigazione prioritaria.** Raccogliere (con consenso) un **dataset di validazione di
voci anziane italiane reali** prima del lancio; valutare Custom Speech / fine-tuning.

---

## 4. TTS e architettura voce realtime

**Stato dell'arte (lug 2026).** TTS streaming a bassissima latenza (sub-100ms TTFB su più
vendor). Per l'italiano: **Azure Neural/HD it-IT** (SSML completo: rate/pitch/pause →
critico per adattività), **ElevenLabs** (voci IT native, molto espressive, EU Data
Residency enterprise), Google Chirp 3 HD, OpenAI gpt-4o-mini-tts, Cartesia (latenza
leader ma IT non confermato), Piper/XTTS (on-device open).

**Architettura voce — decisione chiave.**

| | Speech-to-speech nativo (OpenAI Realtime, Gemini Live) | **Pipeline modulare** STT→LLM→TTS (Pipecat/LiveKit) |
|---|---|---|
| Latenza | Più bassa (~400–800ms) | 800–1500ms, ottimizzabile con streaming |
| **Guardrail testuali** | Difficile (audio non ispezionabile) | **Testo intermedio ispezionabile** ✅ |
| **Sunti per topic** | Complicato | **Nativi** dal testo LLM ✅ |
| Controllo velocità TTS | Limitato | **Pieno** (SSML) ✅ |
| Best-of-breed IT | Lock-in | TTS IT migliore indipendente ✅ |

**Verdetto.** **Pipeline modulare** (Pipecat o LiveKit Agents) con **Azure Neural/HD
it-IT** per l'MVP: il controllo SSML su velocità/pause è un requisito di adattività
(RF-10) e il testo intermedio è **indispensabile** per guardrail (`02`) e sunti-topic
(RF-05). **Speech-to-speech nativo sconsigliato** per STORIES: la minor latenza non
compensa la perdita di ispezionabilità testuale, prioritaria per un utente fragile sotto
AI Act. **Target**: valutare ElevenLabs v3 per innalzare il calore vocale, mantenendo la
pipeline. On-device (Piper/XTTS) solo come fallback offline.

**Nota UX.** Rallentare il parlato aiuta l'intelligibilità negli anziani (confermato in
letteratura) ma un rate troppo basso degrada la prosodia → tarare la soglia con utenti reali.

---

## 5. Memoria affettiva / RAG / Vector DB / Embeddings

**Stato dell'arte (lug 2026).** L'"agent memory" si è consolidata (Mem0, Zep, Letta/MemGPT,
LangMem). La letteratura converge su un pattern a livelli: **episodica** (conversazioni
grezze timestampate) → **distillazione/summary** → **semantica** (fatti stabili) con
**granularità gerarchica** (turno → sessione → topic). **È esattamente il pattern
STORIES**: i sunti per topic sono la memoria semantica derivata dagli episodi. Nessun
framework è pensato per "famiglia condivisa multi-tenant IT con guardrail sanitari" → il
valore sta nel **pattern**, non nell'adozione 1:1 di un framework chiuso.

| Vector DB | Hosting UE | Multi-tenancy | Filtro topic | Note |
|---|---|---|---|---|
| **pgvector (Postgres)** | Self-host UE | **RLS nativa** (`family_id`) | SQL/JSONB + HNSW | Un solo motore; ottimo fino a ~10M vettori |
| Qdrant | Cloud EU / Hybrid su OVH/Scaleway | Nativa (tiered) | Payload potente | Migrazione target se scala |
| Weaviate | Cloud / BYOC | Nativa (shard/tenant) | Buono | GDPR-delete per shard |
| Pinecone | Frankfurt (mag 2026) | Namespace | Sì | **Rischio CLOUD Act (società US)** |

**Embeddings.** Per vincolo "UE stringente": **BGE-M3** (MIT, self-host UE, 100+ lingue
incl. IT) o **Mistral Embed** (gestito EU-native). **Evitare OpenAI embeddings** finché
la Data Zone UE non li copre esplicitamente (segnalato come limite nel 2026).

**Verdetto.** **MVP**: **Postgres + pgvector** self-host UE (RLS per `family_id`, colonna
`topic` + JSONB per i sunti, indice HNSW) + **BGE-M3** self-host (o Mistral Embed
fallback). Un solo motore riduce la complessità. **Target**: se il volume cresce, parte
vettoriale su **Qdrant** UE, Postgres per i dati relazionali, object storage S3 UE per le
foto. Il pattern applicativo è indipendente dal DB → migrazione a basso rischio.

**Filtro pre-scrittura (obbligatorio).** Applicare le "Azioni sulla memoria" di `02`
**prima** di chunking/embedding: mai indicizzare audio, classificare il topic del sunto
prima di scriverlo, bloccare/anonimizzare le categorie sensibili, loggare in modo
immutabile ogni scarto per audit.

---

## 6. Client anziano & canale famiglia

**Stato dell'arte (lug 2026).** Il divario PWA/nativo si è ridotto su UI, ma resta
strutturale su **audio always-on e STT on-device**: la Web Speech API **non funziona
nelle PWA installate su iOS** (solo in Safari browser) e l'audio in background è sospeso;
su Android/Chrome funziona ma è **cloud-based, non on-device**. STT on-device reale nel
2026 è maturo **solo** in contesto nativo (whisper.cpp/WhisperKit iOS, sherpa-onnx/TFLite
Android). **European Accessibility Act** pienamente applicabile dal 28/6/2025 (WCAG 2.2,
sanzioni fino a €40.000): accessibilità **non opzionale**.

**Telegram (canale famiglia).** Bot API: foto/video/audio/voce fino a **50MB**, notifiche
push native gratuite, **Mini Apps** per interfacce ricche (sunti, reminder, gestione
multi-famiglia). **Limiti**: nessuna garanzia di residenza dati UE, sede a Dubai, **nessun
DPA pubblico** → problema di compliance da gestire con misure organizzative (minimizzazione,
niente dati sanitari nel canale, informativa chiara). **Vs WhatsApp**: Telegram è gratuito,
senza pre-approvazione template; WhatsApp introduce billing su repliche di servizio dal
1/10/2026 e richiede approvazione Meta → Telegram nettamente più semplice per l'MVP.

**Verdetto.** **MVP**: mantenere **PWA Next.js/Vercel** (riuso 80–90% del PoC) con UX
**push-to-talk** (non wake-word continuo) e STT cloud EU; **Telegram** confermato per la
famiglia, con policy che escluda contenuti sensibili e informativa GDPR chiara sul
trattamento extra-UE. **Target**: migrare il client anziano a **nativo (Flutter
consigliato** per maturità dei binding Whisper/audio background e team unico) quando serve
STT on-device, wake-word e riconoscimento multi-utente stabile.

**Multi-utente (RF-28).** MVP realistico: **un anziano = un dispositivo/codice** o
selezione manuale "chi sono" a icone (non speaker recognition automatico). Target:
valutare SDK on-device (es. Picovoice Eagle) solo se un device è condiviso da più anziani.

---

## 7. Compliance (EU AI Act + GDPR) & infrastruttura UE

**AI Act (lug 2026).** Divieti Art. 5 (incl. sfruttamento vulnerabilità) applicabili dal
2/2/2025. **Obblighi di trasparenza Art. 50 (dichiarare che è un'IA) pienamente
applicabili dal 2 agosto 2026.** Il pacchetto **"Digital Omnibus"** (via libera Consiglio
29/6/2026) ha **posticipato al 2/12/2027** gli obblighi per l'alto rischio Allegato III;
Art. 5 e Art. 50 **non** toccati.

**Classificazione STORIES.** Nella funzione attuale (compagnia, monitoraggio benessere
dichiarato, alert famiglia) **non** rientra automaticamente nell'alto rischio (Allegato
III) — **salvo** che faccia **profilazione** (Art. 6(3): un sistema che profila è sempre
alto rischio) o condizioni **decisioni su servizi essenziali**. → Mantenere **profilazione
limitata a segnali dichiarati, non inferiti**; conservare una **valutazione documentata
ex Art. 6(4)** "limited risk", da rivedere se il prodotto evolve verso triage sanitario.
**Emotion recognition**: la scelta di STORIES di non farlo (solo disagio dichiarato) è la
via più prudente.

**GDPR.** Consenso in **linguaggio semplice** + possibile **co-consenso familiare** (utenti
con capacità ridotta: in Italia rileva l'amministrazione di sostegno). **Art. 9**: rischio
che l'LLM **inferisca** dati particolari → mitigazione tecnica (guardrail, non persistenza
attributi inferiti). **Minori (nipoti)**: in Italia consenso da **14 anni**. **DPIA quasi
certamente obbligatoria** (soggetti vulnerabili + monitoraggio + IA innovativa). Safeguarding
e avvisi vanno **dichiarati in onboarding**, non a sorpresa. **Audio mai conservato**.

**Infra UE.**

| Provider | Sovranità UE | Certificazioni | Note |
|---|---|---|---|
| OVHcloud (FR) | 30+ DC UE | ISO 27001/17/18, **SecNumCloud**, HDS | Buona per healthcare-adjacent |
| Scaleway (FR) | DC FR/PL | — | Bando UE sovereign cloud 2026 |
| Aruba (IT) | DC IT/UE | ISO 27001 | Allineamento ACN Italia |
| IONOS (DE) | DC DE/UE | ISO 27001 | Forte posizionamento GDPR |
| AWS Eu. Sovereign Cloud | Entità DE, CA propria | AWS + governance indip. | ~90 servizi (gen 2026) |

**LLM extra-UE.** Anthropic **non** offre residenza UE per l'inferenza API diretta
(elaborazione US; DPA+SCC+DPF; retention log ridotta a 7 giorni; **ZDR su richiesta
commerciale**). OpenAI offre **EU data residency "per progetto"** (sales-gated). →
Mitigazioni: **pseudonimizzare/redigere PII prima di inviare all'LLM**; richiedere **ZDR**;
escludere training; instradare via **Bedrock/Vertex EU**; piano B verso modello EU-hosted.

---

## 8. Budget di latenza end-to-end (pipeline modulare)

Obiettivo conversazione fluida (RNF-14). Budget indicativo da validare:

| Fase | Target | Note |
|---|---|---|
| STT (chunk) | 60–300 ms | streaming/chunked EU |
| LLM first-token | 100–700 ms | Sonnet 5 / Haiku per ridurre |
| TTS first-chunk | 40–400 ms | Azure HD streaming |
| **Totale percepito** | **~0,8–1,5 s** | ottimizzabile con streaming aggressivo e barge-in |

Turn-taking, VAD e **barge-in** (interruzione) gestiti dall'orchestratore (LiveKit/Pipecat).

---

## 9. Verdetto di fattibilità complessivo

Il prodotto è **tecnicamente fattibile** con tecnologie mature disponibili a luglio 2026.
Il PoC è una base solida. I **rischi principali** non sono tecnologici ma di **compliance**
e di **qualità sul parlato anziano reale**:

| # | Rischio trasversale | Mitigazione |
|---|---|---|
| R1 | Residenza dati UE non nativa per LLM/STT/embeddings US | Bedrock/Vertex EU, ZDR, pseudonimizzazione, provider/embedding EU; piano B EU-hosted |
| R2 | Safety layer LLM inadeguati sui rischi anziani (GrandGuard) | Guardrail applicativi obbligatori (`02` liv. 2–3), no delega al modello |
| R3 | WER ignoto su parlato anziano/dialetto | Dataset di validazione reale, Custom Speech/fine-tuning |
| R4 | Telegram fuori UE, no DPA | Minimizzazione, niente dati sanitari nel canale, informativa; exit strategy |
| R5 | PWA iOS senza voce background / STT on-device | Push-to-talk nell'MVP; migrazione a nativo nel target |
| R6 | Ciclo modelli rapidissimo | Layer LLM/voce astratto e versionato (RNF-17); review trimestrale |
| R7 | Scivolamento verso alto rischio AI Act (profilazione/sanitario) | Scope control, valutazione Art. 6(4), revisione legale su nuove feature |
| R8 | Consenso per utenti con capacità ridotta | Linguaggio semplice, co-consenso familiare, DPIA |

---

## 10. Decisioni che alimentano gli ADR

Questo studio motiva i seguenti Architecture Decision Records (vedi `06-adr/`):

- **ADR-0001** — STT: server EU audio-scartato (MVP) → on-device (target).
- **ADR-0002** — LLM: Claude Sonnet 5 via Bedrock/Vertex EU + fallback Haiku.
- **ADR-0003** — Client: PWA (MVP) → nativo Flutter (target).
- **ADR-0004** — Memoria: Postgres+pgvector (MVP) → Qdrant (target); embeddings EU.
- **ADR-0005** — Infra: cloud UE + object storage S3 UE; cifratura; chiavi UE.
- **ADR-0006** — Voce: pipeline modulare STT→LLM→TTS; TTS Azure HD it-IT.
- **ADR-0007** — Canale famiglia: Telegram Bot + Mini App con misure di minimizzazione.

> Ogni scelta MVP è **reversibile** grazie a layer di astrazione (RNF-17): la feasibility
> definitiva e uno studio comparativo più approfondito (in particolare su STT on-device e
> residenza LLM) sono previsti come attività dedicata, come da indicazione del committente.
