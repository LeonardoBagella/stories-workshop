# ADR-0005 — Infrastruttura e storage in UE

**Stato**: Accettato (MVP) · **Data**: 2026-07 · **Rif.**: RNF-04/05/07, `03` §7

## Contesto

I dati personali devono risiedere in UE (RNF-05) e il prodotto tratta soggetti fragili. La
ricerca (`03` §7) confronta i provider cloud UE e le opzioni di sovranità.

## Decisione

- **MVP**: orchestrazione, DB, object storage e log su **provider UE** —
  **OVHcloud / Scaleway / Aruba / IONOS** (scelta finale al workshop in base a
  certificazioni e costi). Object storage **S3-compatibile in regione UE**.
- Cifratura **TLS 1.3** in transito; cifratura dello storage a riposo (obiettivo AES-256);
  **chiavi gestite in UE**.
- **Target**: valutare **AWS European Sovereign Cloud** e certificazione **ISO 27701**;
  **AI Decision Log** immutabile e auditabile.
- L'unico componente extra-UE ammesso è l'**inferenza LLM**, minimizzata a testo
  pseudonimizzato via Bedrock/Vertex EU con ZDR (ADR-0002).

## Alternative considerate

- **Azure EU Data Boundary**: confine **logico**, con eccezioni operative globali → meno
  forte della sovranità piena per un caso su soggetti fragili.
- **Provider US con regione UE** (senza governance UE): rischio CLOUD Act.

## Conseguenze

- ✅ Residenza UE, certificazioni (es. SecNumCloud/HDS su OVHcloud) utili per settore
  healthcare-adjacent e bandi pubblici.
- ⚠️ Alcuni provider UE hanno DX/servizi gestiti meno ricchi degli hyperscaler → mitigato
  con stack self-managed semplice (Postgres, S3-compatibile).
- 🔁 La scelta del provider specifico è rinviabile al workshop senza impatti sul design.
