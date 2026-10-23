---
name: session-handoff
description: Chiusura sicura di una sessione di lavoro con contesto saturo e delega verificata alla sessione successiva. Usa questa skill OGNI VOLTA che l'utente dice che il contesto è pieno o quasi, che sta per chiudere, riavviare o compattare la sessione, che vuole "passare il lavoro" a una nuova sessione, a un altro agente o a un collega, o che chiede un riepilogo per riprendere dopo — e attivala anche di tua iniziativa quando durante un task lungo ti accorgi che il contesto si sta saturando e la qualità del lavoro sta per degradare. Usala anche all'inizio di una sessione che riprende da un handoff precedente, per validarlo prima di fidartene.
---

# Session Handoff

Questa skill definisce come chiudere una sessione di lavoro quando il contesto è saturo, e come consegnare lo stato a chi lo riprende — una nuova sessione, un altro agente, o la stessa persona lunedì mattina.

Una sessione che si esaurisce non è un problema. Una sessione che si esaurisce **in silenzio** sì: il codice sopravvive nei file, il contesto no. Tutto ciò che è stato capito leggendo il repo, ogni vicolo cieco esplorato, ogni ragione dietro una scelta non ovvia evapora al riavvio, e chi riprende lo ricostruisce da zero — o peggio, non lo ricostruisce e ci lavora contro.

**Prerequisiti.** Questa skill si appoggia a tre skill che governano aspetti che qui non vengono ripetuti:

- `dev-discipline` — la Definition of Done rispetto a cui si misura il lavoro incompleto (scorciatoie dichiarate, qualità implicita, impatto sui chiamanti).
- `dev-communication` — quando procedere in autonomia e quando fermarsi. Vale anche durante la chiusura.
- `design-docs` — la documentazione di design che il task poteva aver lasciato indietro.

Leggile se non sono già in contesto. Questa skill aggiunge solo ciò che è specifico della consegna.

## Le tre regole che governano tutto

In caso di tensione tra qualsiasi indicazione di questa skill, vincono queste tre, in quest'ordine:

1. **Si scrive rileggendo, non ricordando.** Ogni affermazione dell'handoff — cosa è fatto, cosa è rotto, dove si era arrivati — va ri-verificata contro il repo, `git`, i test. Il contesto saturo è precisamente la condizione in cui la memoria della sessione è meno affidabile, ed è precisamente il momento in cui scrivi l'handoff. Un documento scritto a memoria è fluente e sottilmente falso: chi lo eredita gli dà fiducia senza avere le evidenze per verificarlo, e costruisce sopra un'assunzione sbagliata.
2. **Niente sintesi che nasconde.** Lavoro a metà, scorciatoie prese, test rossi, dubbi irrisolti vanno dichiarati con la stessa precisione dei successi. Un handoff che racconta solo la parte finita è la versione a fine sessione della scorciatoia non dichiarata: fa sembrare il lavoro più solido di quanto sia, e il conto lo paga chi riprende.
3. **Prima si mette in sicurezza, poi si documenta.** Nell'ordine: stato reale → verifica → documento. Scrivere il documento prima di aver guardato lo stato significa documentare la propria memoria; e mettere in sicurezza dopo aver scritto significa che il documento descrive uno stato già superato.

## Quando attivarla: la soglia

Segnali che è il momento: il contesto è vicino al limite; stai rileggendo file che avevi già letto; ti accorgi di aver perso dettagli stabiliti prima nella sessione; il task ha ancora fasi davanti e il budget non le copre.

**Il momento giusto è prima del degrado, non dopo.** Un handoff scritto con il 5% di contesto residuo è scritto male esattamente per la ragione per cui serviva: non c'è spazio per rileggere il diff, girare i test, verificare. La regola pratica: apri la chiusura quando resta abbastanza contesto per fare la Fase 0 per intero. Se te ne accorgi tu prima dell'utente, **dillo subito** in una riga invece di aspettare — è una delle poche segnalazioni che vale la pena anticipare, perché il suo valore scade.

## Workflow

### Fase 0 — Stato reale, non ricordato

Ricostruisci lo stato con comandi, non con la memoria:

- **Git**: branch corrente e upstream (avanti/indietro di quanti commit), `git status`, `git diff` e `git diff --staged` completi, i commit fatti in questa sessione, `git stash list`.
- **Verifiche**: gira la suite di test e il linter/analisi statica. Se non è possibile (troppo lenti, ambiente non pronto), dichiaralo nel documento come "non verificato" — mai come "a posto".
- **Baseline**: distingui i test rossi ereditati da quelli causati dal lavoro di questa sessione. Consegnare un rosso senza dire di chi è la colpa costringe chi riprende a rifare l'indagine da capo.
- **Diff completo**: rileggi il diff del lavoro fatto. È la fonte di verità su "cosa ho fatto"; la conversazione è un ricordo di seconda mano.

Se il contesto residuo non basta nemmeno per questo, dillo e riduci l'ambizione dell'handoff (vedi *Casi edge*): tre righe vere valgono più di due pagine ricostruite.

### Fase 1 — Messa in sicurezza

Il presupposto da correggere: **il lavoro non committato non è perso, è solo non tracciato.** Ciò che si perde davvero è il contesto, e a salvarlo è il documento, non un commit. Da qui la scala di autonomia:

- **Sempre, in autonomia** — catturare lo stato (comandi di sola lettura della Fase 0) e riportarlo nel documento. Non modifica nulla, non richiede conferma.
- **Default: non committare.** Il working tree sporco si consegna descrivendolo con precisione — quali file, che natura di modifica, perché è a metà — non impacchettandolo in un commit di comodo che sporca la history del branch con codice incompleto.
- **Proponi e aspetta** — commit WIP su branch dedicato (`wip/<slug>`), solo quando c'è un rischio reale di perdita: cambio di macchina, working tree molto ampio, ambiente effimero. Il messaggio dichiara che lo stato è incompleto e segue le convenzioni del repo; niente trailer di co-autore.
- **Mai in autonomia** — push, amend, rebase, merge, `reset --hard`, `checkout` che scarta modifiche, `stash drop`. Sono irreversibili, visibili ad altri, o entrambi.

`git stash` non è un meccanismo di consegna: è invisibile in `git status`, non descrive sé stesso e si dimentica. Se ne esiste già uno rilevante, censiscilo nel documento con il suo indice e cosa contiene.

**File temporanei e di debug** creati durante la sessione: rimuovili se sono spazzatura, censiscili se servono. Un file anonimo trovato nel working tree dalla sessione dopo è puro attrito.

### Fase 2 — Verifica di coerenza

Passa il lavoro con le lenti di `dev-discipline`, ma **senza tentare di completarlo**:

- Scorciatoie, hardcoding, TODO a metà, log di debug residui: o li togli, se è questione di un minuto, o li censisci. Mai lasciarli muti — è il caso in cui il principio 4 di `dev-discipline` conta di più, perché nessuno li rivedrà con il contesto che hai adesso.
- **Propagazione incompleta**: se una modifica ha cambiato un contratto e non tutti i chiamanti sono aggiornati, l'elenco dei punti mancanti è la parte più preziosa dell'intero handoff. Ricavalo con un grep sistematico, non a memoria.
- **Documentazione di design**: se il task ha toccato dominio, flussi, UI o architettura, `design-docs` dice che l'aggiornamento fa parte del task. Se non c'è più contesto per farlo, diventa una voce di residuo con puntamento preciso — quale diagramma, quale ADR — non un generico "aggiornare la doc".

**Non iniziare lavoro nuovo in questa fase.** È l'errore classico: verificando trovi una cosa da sistemare, la sistemi, e bruci il contesto che serviva a scrivere l'handoff — chiudendo peggio di come avresti chiuso senza toccare niente. Se non è una riga, va nel documento.

### Fase 3 — Il documento di delega

Usa `assets/handoff-template.md` come struttura di partenza; rimuovi le sezioni vuote invece di riempirle con "N/A".

Regole di scrittura, in ordine di impatto:

- **Verificabile**: percorsi `file:riga`, comandi eseguibili così come sono, nomi esatti di branch e commit. Chi riprende non deve indovinare né cercare.
- **Vicoli ciechi**: i tentativi falliti e il perché sono la sezione con il miglior rapporto valore/lunghezza. Un vicolo cieco non documentato viene ripercorso per intero.
- **Decisioni prese, con la ragione**: altrimenti la sessione dopo le "corregge" senza conoscerne il motivo (è lo stesso principio di `dev-communication` sulle scelte non ovvie, applicato nel tempo invece che tra persone).
- **Decisioni aperte restano aperte**: riportale con le opzioni e il trade-off reale. Risolverle d'ufficio nel documento per farlo sembrare più netto significa prendere in autonomia una decisione che avevi correttamente lasciato all'utente.
- **Stato, non cronaca**: il documento descrive il repo e il task, non la conversazione. "Abbiamo provato X, poi tu hai chiesto Y" non serve a nessuno; serve com'è il codice adesso e cosa manca.
- **Lunghezza**: quanto basta a ripartire senza accedere a questa sessione. Se supera le due pagine, quasi sicuramente contiene cronaca.

### Fase 4 — Dove va il documento

In ordine, il primo caso che si applica:

1. **Il repo ha già una convenzione** (`docs/handoff/`, `NOTES.md`, una cartella di note di sessione, una struttura in `.claude/`): usa quella. Le convenzioni del repo vincono, qui come altrove.
2. **Repo git senza convenzione**: `.handoff/YYYY-MM-DD-<slug>.md`, con `.handoff/` aggiunto a `.git/info/exclude`. L'esclusione locale, invece di `.gitignore`, tiene il file fuori da `git status` senza modificare un file condiviso col team per una necessità personale.
3. **Non è un repo git, o il filesystem non è scrivibile**: consegna il documento in chat, integrale e in un unico blocco copiabile.
4. **L'handoff è destinato a una persona** (collega, passaggio di consegne, PR draft): il contenuto non cambia, la destinazione sì — chiedi se va versionato o incollato altrove, invece di deciderlo tu.

Se il task continua oltre una sola ripresa, **aggiorna il documento esistente** invece di crearne uno nuovo a ogni sessione: diventa il diario di consegna di quel task, e la sua storia è utile. Un file nuovo ogni volta produce solo una pila di snapshot scaduti.

### Fase 5 — Ripresa (handoff-in)

All'inizio di una sessione che eredita un handoff:

- **Leggilo, poi verificalo prima di fidartene.** Il repo può essere cambiato: altri commit, altre sessioni, merge a monte. Ri-esegui la ricognizione minima — branch, `git status`, `git log`, test — e confronta con quanto dichiarato.
- **Se documento e realtà divergono, vince la realtà.** Segnala la divergenza all'utente e correggi il documento prima di lavorarci sopra: un handoff stantio a cui si continua a credere è peggio di nessun handoff.
- **Chiudi il ciclo**: quando il task è finito, il documento va cancellato o archiviato. Un handoff che sopravvive alla fine del lavoro descrive uno stato che non esiste più — è documentazione che mente, esattamente come un diagramma disallineato.

## Casi edge

**Contesto già oltre la soglia, degrado iniziato.** Handoff minimo ma verificato: stato git, esito dei test, prossimo passo concreto — e la dichiarazione esplicita che è minimo e cosa non è stato verificato. Non compensare la mancanza di contesto ricostruendo a memoria: è il caso in cui la regola 1 viene violata più facilmente e con più danno.

**Operazione pericolosa interrotta a metà** (migration applicata solo in parte, rebase in corso, container o servizio lasciato attivo, dipendenze installate ma non dichiarate, credenziali temporanee in un `.env`): va in cima al documento, non in fondo. È l'unica informazione che, se persa, può fare danni prima ancora che qualcuno riprenda il lavoro.

**Più repo o monorepo**: uno stato per area toccata, ognuno con il suo branch e le sue verifiche. Un unico stato aggregato nasconde quale parte è pulita e quale no.

**Sessione interrotta senza handoff, ricostruzione a posteriori**: ricostruisci solo ciò che git e i test dimostrano, e dichiara che le motivazioni dietro le scelte sono andate perse. Non inventare i "perché": un perché plausibile ma falso è peggio di un perché assente, perché blocca l'indagine.

**Lavoro non su codice** (documenti, analisi, ricerca): la sostanza non cambia — stato, residuo, decisioni, prossimo passo. Cambiano solo le verifiche disponibili.

## Cosa NON fare

- Non scrivere il documento a memoria: la memoria a fine sessione è la fonte meno affidabile che hai.
- Non presentare il lavoro più completo di com'è, nemmeno per omissione.
- Non iniziare lavoro nuovo durante la chiusura: il contesto residuo serve a consegnare, non a produrre.
- Non usare `git stash` come meccanismo di consegna.
- Non pushare, amendare o rebasare in autonomia per "sistemare" prima di chiudere.
- Non riassumere la conversazione al posto dello stato del repo.
- Non lasciare file temporanei anonimi nel working tree.
- Non lasciare il documento di handoff nel repo dopo la fine del task.
- Non aspettare che il degrado sia evidente per aprire la chiusura.

## Checklist prima di chiudere

1. Stato git catturato con comandi reali, non a memoria?
2. Test e linter girati — o dichiarato esplicitamente perché no — con esito registrato?
3. Test rossi separati tra baseline ereditata e regressioni di questa sessione?
4. Ogni scorciatoia, TODO, debug residuo e propagazione incompleta è censita?
5. Il prossimo passo è scritto in modo eseguibile, non come intenzione generica?
6. Vicoli ciechi e decisioni prese (con il perché) sono nel documento?
7. Decisioni aperte lasciate aperte, con opzioni e trade-off?
8. Documentazione di design aggiornata, o censita come residuo con puntamento preciso?
9. Percorsi, comandi, branch e commit sono esatti e verificabili da chi legge?
10. Chi riprende può ripartire **senza** accedere a questa sessione?

Se una risposta è "no", sistemala prima di chiudere — o dichiarala esplicitamente nel documento.
