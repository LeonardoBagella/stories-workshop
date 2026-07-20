# ADR-0003 — Client anziano: PWA (MVP) → nativo Flutter (target)

**Stato**: Accettato (MVP) · **Data**: 2026-07 · **Rif.**: RNF-11/12, RF-28, `03` §6

## Contesto

Il PoC è una **web app Next.js su Vercel**. L'app dell'anziano deve essere **voice-first**,
accessibile e (idealmente) fare STT on-device. La ricerca (`03` §6) mostra che le **PWA su
iOS** non supportano Web Speech API in modalità installata né audio in background, e che lo
**STT on-device reale** è maturo solo in contesto nativo. L'European Accessibility Act
(WCAG 2.2) è obbligatorio.

## Decisione

- **MVP**: mantenere **PWA Next.js/Vercel** (riuso 80–90% del PoC), con UX **push-to-talk**
  (non wake-word continuo) e **STT cloud EU** (ADR-0001). Accessibilità WCAG 2.2 (caratteri
  grandi, alto contrasto, audio-guida).
- **Target**: migrare il client anziano a **nativo — Flutter** (consigliato su React
  Native per maturità dei binding Whisper/audio background e team unico) quando servono STT
  on-device, wake-word always-on e riconoscimento multi-utente stabile.
- **Multi-utente (RF-28)**: MVP = **un anziano = un dispositivo/codice** o selezione
  manuale "chi sono" a icone; target = valutare speaker recognition on-device (es.
  Picovoice Eagle) solo se un device è condiviso.

## Alternative considerate

- **Nativo subito**: scartato per l'MVP — costo/tempo elevati, rallenta il test.
- **React Native**: valida, ma Flutter ha binding audio/Whisper più maturi per il caso.

## Conseguenze

- ✅ Riuso massimo del PoC, nessun costo store, iterazione rapida.
- ⚠️ Su iOS niente voce in background nell'MVP → mitigato con push-to-talk.
- ⚠️ STT cloud nell'MVP (vedi ADR-0001).
- 🔁 Backend/API riusabili alla migrazione nativa; solo la UI va riscritta.
