# ADR-0006 — Voce: pipeline modulare STT→LLM→TTS

**Stato**: Accettato (MVP) · **Data**: 2026-07 · **Rif.**: RF-01/05/10, RNF-14, `03` §4

## Contesto

L'esperienza è **voice-first** e servono: guardrail sul testo, sunti per topic dal testo,
controllo della velocità del parlato (adattività), bassa latenza. Due paradigmi possibili:
**speech-to-speech nativo** (OpenAI Realtime, Gemini Live) o **pipeline modulare**
STT→LLM→TTS. La ricerca (`03` §4) evidenzia il trade-off latenza vs ispezionabilità.

## Decisione

- **Pipeline modulare** STT→LLM→TTS orchestrata (**Pipecat** o **LiveKit Agents**), con
  **testo intermedio ispezionabile**.
- **TTS**: **Azure Neural/HD it-IT** per l'MVP (SSML completo: `prosody rate`/`break` →
  requisito adattività RF-10); **target** ElevenLabs v3 (EU Data Residency) per il calore
  vocale.
- **Turn-taking, VAD, barge-in** gestiti dall'orchestratore; streaming su tutte le fasi
  per stare nel budget ~0,8–1,5 s (`03` §8).

## Alternative considerate

- **Speech-to-speech nativo**: latenza inferiore (~400–800 ms) ma **perde l'ispezionabilità
  testuale** necessaria per guardrail (`02`) e sunti-topic (RF-05) → **scartato** per un
  utente fragile sotto AI Act.
- **TTS on-device** (Piper/XTTS): qualità inferiore ai cloud premium → solo fallback offline.

## Conseguenze

- ✅ Guardrail e sunti-topic nativi dal testo; controllo pieno su velocità/prosodia; scelta
  best-of-breed del TTS italiano indipendente da STT/LLM.
- ⚠️ Latenza leggermente maggiore dello speech-to-speech → mitigata con streaming aggressivo.
- 🔁 Ogni componente (STT/LLM/TTS) è sostituibile indipendentemente.
