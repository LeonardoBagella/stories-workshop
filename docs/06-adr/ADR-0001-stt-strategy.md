# ADR-0001 — Strategia STT: server EU (MVP) → on-device (target)

**Stato**: Accettato (MVP) · **Data**: 2026-07 · **Rif.**: RNF-01, `03` §3

## Contesto

Il documento funzionale chiede che **l'audio non transiti/persista sui server** (dalla app
dovrebbe arrivare già la trascrizione → STT on-device). Per l'MVP, Boosha ha proposto una
**trascrizione lato server con audio scartato immediatamente** (punto segnato come
"parliamone"). Il committente ha confermato questa soluzione per ora, con riserva di uno
studio di fattibilità più approfondito. La ricerca (`03` §3) mostra che lo **streaming STT
on-device su Android mid-range è oggi problematico** e che i WER pubblicati non coprono il
parlato anziano/dialettale reale.

## Decisione

- **MVP**: STT **lato server in regione UE** con **audio scartato subito** dopo la
  trascrizione. Provider primario **Deepgram Nova-3** (endpoint EU, streaming,
  zero-retention default, miglioramenti su IT dialettale); alternativa **ElevenLabs
  Scribe v2** (EU + Zero Retention).
- **Target**: STT **on-device** — Apple SpeechAnalyzer su iOS, Whisper/Voxtral su Android
  (modalità batch/chunked finché lo streaming on-device non è affidabile).
- L'audio **non è mai persistito** (RNF-01) in nessuna fase.
- Il layer STT è dietro un'**interfaccia astratta** per cambiare provider senza refactoring.

## Alternative considerate

- **STT on-device puro subito**: scartato per l'MVP — inaffidabile su PWA iOS e Android
  mid-range; rallenterebbe il time-to-test.
- **OpenAI transcribe**: scartato come ancora dell'MVP — cicli di dismissione rapidi
  (gpt-4o-transcribe già in retirement).

## Conseguenze

- ✅ Time-to-test rapido, qualità IT buona, compliance gestibile (audio scartato, EU).
- ⚠️ Nell'MVP l'audio transita (transitoriamente) su server EU: **scostamento** dal
  requisito on-device → documentato, con audio non persistito e informativa chiara.
- ⚠️ WER reale su anziani ignoto → **raccogliere dataset di validazione** con consenso.
- 🔁 Reversibile: l'interfaccia STT consente la migrazione on-device nel target.
