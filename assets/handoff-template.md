# Template del documento di handoff

Struttura di partenza, non un modulo da riempire. **Rimuovi le sezioni che non hanno contenuto** invece di scriverci "N/A": una sezione vuota costringe chi legge a verificare che sia davvero vuota. Le uniche sezioni sempre presenti sono *Stato in una riga*, *Stato del repo* e *Prossimo passo*.

L'ordine non è casuale: chi riprende legge dall'alto e deve incontrare per prime le informazioni che gli servono nei primi trenta secondi.

---

```markdown
# Handoff — <titolo del task> (<YYYY-MM-DD>)

## Stato in una riga
<Dove siamo e cosa manca, in una frase. Se non riesci a scriverla in una frase,
probabilmente non hai ancora chiaro lo stato: torna alla Fase 0.>

## ⚠️ Da sapere subito
<SOLO se c'è qualcosa che può fare danno prima ancora che il lavoro riprenda:
migration applicata a metà, rebase in corso, servizio o container lasciato attivo,
dipendenze installate ma non dichiarate, credenziali temporanee in un .env.
Se non c'è nulla, cancella questa sezione.>

## Obiettivo del task
<Cosa si sta facendo e perché. Due o quattro righe. Serve a chi riprende senza
memoria della richiesta iniziale — non è la cronaca di come ci siamo arrivati.>

## Stato del repo
- **Branch**: `<branch>` — upstream `<remote/branch>`, avanti di N / indietro di M
- **Commit di questa sessione**: `<sha breve>` <messaggio> (uno per riga)
- **Working tree**: pulito | sporco:
  - `percorso/file.ext` — <natura della modifica e perché è a metà>
- **Stash rilevanti**: nessuno | `stash@{N}` — <cosa contiene>

## Verifiche eseguite

| Comando | Esito | Note |
|---|---|---|
| `<comando test>` | <N pass, M fail> | <baseline o regressione> |
| `<linter / analisi statica>` | <esito> | |

- **Rossi ereditati** (baseline, non causati da questo lavoro): <elenco o "nessuno">
- **Rossi causati da questo lavoro**: <elenco o "nessuno">
- **Non verificato**: <cosa non è stato girato e perché>

## Fatto
- <cosa, con `percorso/file.ext:righe`>

## Residuo
- [ ] <voce concreta: quale file, cosa manca, non "finire la feature">
- [ ] <propagazione incompleta: chiamanti/consumatori non ancora aggiornati, con percorsi>
- [ ] <documentazione di design da aggiornare: quale diagramma, quale ADR>

## Scorciatoie e debito dichiarato
<Hardcoding, TODO, workaround presenti nel codice consegnato, con posizione precisa
e perché sono stati presi. "Nessuna" è una risposta valida, ma va scritta: il silenzio
qui viene letto come assenza di debito.>

## Decisioni prese (e perché)
- <decisione> — <ragione, alternative scartate>. <ADR-NNNN se scritto>

## Decisioni aperte
- <domanda> — Opzioni: **A** <...> / **B** <...>. Trade-off: <...>.
  Serve conferma dell'utente prima di procedere.

## Vicoli ciechi — già esplorati, non ripercorrerli
- <tentativo> → <perché non funziona / dove si rompe>

## Prossimo passo
<Il primo comando da eseguire o la prima modifica da fare, concreto abbastanza da
essere eseguito senza altre decisioni. "Continuare l'implementazione" non è un
prossimo passo.>

## Come tornare operativi

```bash
git checkout <branch>
<comando di install / setup se serve>
<comando che gira i test dell'area toccata>
```
```
