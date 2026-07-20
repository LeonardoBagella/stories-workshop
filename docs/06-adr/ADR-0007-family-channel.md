# ADR-0007 — Canale famiglia: Telegram Bot + Mini App

**Stato**: Accettato (MVP) · **Data**: 2026-07 · **Rif.**: RF-32/33, `03` §6, `05` §8

## Contesto

La famiglia deve comunicare con STORIES tramite un'**app familiare**, senza aggiungere
gradini d'accesso. Nel thread è stato **confermato Telegram** (più semplice di WhatsApp).
Il PoC usa già un bot Telegram. La ricerca (`03` §6) conferma capacità e limiti.

## Decisione

- **MVP e oltre**: **Telegram Bot API** come canale famiglia, con **Mini Apps** per
  interfacce ricche (sunti per topic, reminder, gestione multi-famiglia). Notifiche push
  native; **spente di default per i profili tester** (RF-33).
- **Misure di compliance** (`05` §8): niente dati sanitari/sensibili nel canale;
  informativa GDPR chiara sul trattamento extra-UE; contenuti sensibili solo
  nell'infrastruttura UE.
- **Exit strategy**: predisporre la sostituibilità del canale (astrazione) verso una
  soluzione UE-hosted se il prodotto scala.

## Alternative considerate

- **WhatsApp Business API**: pre-approvazione template Meta e **billing su repliche di
  servizio dal 1/10/2026** → più costoso/complesso, giustificato solo a scala enterprise.
- **App proprietaria per la famiglia**: aggiunge un gradino d'accesso (contro US-TECH).

## Conseguenze

- ✅ Gratuito, veloce da integrare, familiare per gli utenti, riuso del PoC.
- ⚠️ Telegram **fuori UE, senza DPA pubblico** (limite 50MB file) → mitigato con
  minimizzazione dei dati e informativa; contenuti sensibili fuori dal canale.
- 🔁 Canale astratto → sostituibile nel target.
