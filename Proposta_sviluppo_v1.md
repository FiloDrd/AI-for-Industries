\# KuaiRand-1K · Proposta di sviluppo e revisione critica



\*\*Destinatari:\*\* i due autori del progetto e Claude, come revisore tecnico.  

\*\*Contesto:\*\* AI in Industry, prof. Michele Lombardi, Master in Artificial Intelligence, Università di Bologna.



Questo documento propone una direzione a partire dall’abstract del progetto, dal notebook sui tre log e dall’esplorazione dei profili utente e dei video. Le decisioni sono aperte alla discussione. A Claude chiediamo prima un parere sugli obiettivi e poi una revisione del disegno sperimentale. Sono benvenute tecniche ulteriori, anche esterne al corso, se aiutano a rispondere a una domanda precisa e se entrambi gli studenti possono spiegarle, verificarle e discuterne i limiti.



\## Parte I — Quale progetto vale la pena fare?



\### La decisione del feed



Un utente apre l’app e il sistema dispone di più video candidati. Quale mettere prima per aumentare la probabilità di una visione lunga? L’abstract propone di misurare quanto la personalizzazione aggiunga alla popolarità, introducendo abitudini dell’utente, storia recente e affinità con il contenuto. L’estensione da 3 CFU cambia l’obiettivo in like e osserva quanto cambia la classifica.



\*\*Focus proposto per la discussione:\*\* usare i video assenti dallo storico di training come caso principale e mantenere l’intero feed come confronto. Per un video appena pubblicato conosciamo già durata, tag e autore. Possiamo verificare se sapere come \*quel particolare utente\* ha reagito a quei tag migliori l’ordine dei candidati. Il gruppo potrebbe anche mantenere il feed completo come obiettivo principale e trattare i video nuovi come sottogruppo. Le due scelte producono conclusioni con una portata diversa.



\### Come leggere i dati



Una \*impressione\* è un video mostrato a un utente. Il log \*standard\* registra i video scelti dal sistema in uso. Il log \*random\* registra inserimenti estratti casualmente da un insieme prestabilito di video. Il campo `tab` identifica lo scenario dell’app. Il valore `1` si comporta come un feed a colonna singola secondo il notebook, mentre la corrispondenza con la schermata esatta rimane ipotetica. Un \*blocco\* riunisce le righe dello stesso utente con lo stesso timestamp nello scenario studiato. La posizione di visione interna al blocco resta sconosciuta.



| Evidenza esplorativa | Deduzione da discutere |

|---|---|

| Nel test standard del tab 1, l’80,7% delle impressioni riguarda video assenti da tutti i tab del training. Tra queste impressioni, l’85,9% riguarda video pubblicati dopo il 21 aprile. | La scarsità di storia del singolo video è parte del problema industriale. Serve una baseline che funzioni anche al primo incontro. |

| Il catalogo contiene 58 tag individuali. Tra le impressioni standard su video nuovi con tag e autore utilizzabili, nel 97,7% dei casi l’utente aveva già incontrato almeno un tag in cinque blocchi precedenti del tab 1. | La familiarità con il tag è quasi universale in questo sottoinsieme. La quantità da provare è la risposta personale passata al tag e il supporto con cui la stimiamo. |

| Per i 901 utenti con almeno 200 impressioni in ciascuna finestra, il tasso personale di visioni lunghe nelle due finestre ha correlazione 0,93. Nel test 29 utenti sono privi di storia di training nel tab 1. | La propensione personale è una baseline forte per gli utenti attivi. Un modello può stimare meglio chi guarda a lungo pur mantenendo identico l’ordine dei video per quella persona. |

| Nei dati mensili dei video esposizione e tasso di visioni lunghe sono associati anche entro fasce di durata. Per i video fino a 18 s nella fascia più esposta, la metà centrale va da 12,28 a 47,69 visioni lunghe per 100 esposizioni. | Esposizione, risposta per esposizione e durata descrivono aspetti diversi. La dispersione motiva l’esperimento; da sola non dimostra personalizzazione. |

| Nel test del tab 1, le visioni lunghe sono circa il 31,5% nello standard e l’8,5% nel random. Le durate mediane sono rispettivamente 26 e 77 s. Il pool random osservato nel tab 1 comprende video pubblicati dal 9 all’11 aprile. | Lo standard misura il comportamento nel feed osservato, il random verifica se il segnale personale si trasferisce a esposizioni e candidati diversi. Il divario grezzo mescola selezione e composizione del pool. |



Le cifre sul catalogo provengono dalle analisi dei metadati e, per le distribuzioni mensili, dal campione di 21.866 video. Gli incroci su novità e familiarità sono elaborazioni successive al notebook sui log. Riprodurli nel notebook condiviso, indicando denominatori, finestra e quota di metadati mancanti. Nel calcolo del 97,7% sono stati esclusi i casi senza tag utilizzabile o con codice autore 0, trattato prudenzialmente come ambiguo. \[Documentazione KuaiRand](https://github.com/chongminggao/KuaiRand) · \[Distribuzione dei dati](https://zenodo.org/records/10439422)



\### Che cosa renderebbe informativo l’esperimento?



Immaginiamo due video nuovi della stessa durata. In media il tag di A ottiene più visioni lunghe. Per questa persona, invece, lo storico favorisce il tag di B. L’esperimento deve mostrare se invertire coppie come A e B porta più spesso un video con esito positivo davanti a uno con esito negativo, sui candidati effettivamente osservati.



Un miglioramento della sola probabilità media suggerirebbe un’utilità per la previsione individuale. Un miglioramento dell’ordine entro utente, soprattutto nei blocchi con esiti diversi, sosterrebbe una conclusione sul ranking. Un vantaggio limitato agli utenti con molta storia indicherebbe quando utilizzare il segnale personale. Un guadagno trascurabile, con incertezza abbastanza contenuta, sarebbe già un risultato da discutere.



\*\*Prima richiesta a Claude e al gruppo.\*\* Formulate l’obiezione più forte a questa domanda di ricerca. Confrontate il focus sui video nuovi con l’obiettivo originario sull’intero feed. Per ciascuna opzione indicate quale decisione industriale chiarisce, quale conclusione i log consentono e quale osservazione farebbe cambiare direzione. Proponete poi un obiettivo principale, un’estensione e una domanda che conviene accantonare.



\## Parte II — Come tradurre la domanda nel notebook finale



\### 1. Definire l’esperimento prima del fitting



\- \*\*Unità e target.\*\* Una riga per impressione del tab 1, con il flag ufficiale `long\_view`. A durata positiva, la soglia corrisponde alla durata intera fino a 18 s e a 18 s per i video più lunghi. Usare la durata disponibile prima della visione. Il tempo guardato e le reazioni della riga corrente rivelano l’esito.

\- \*\*Tempo.\*\* Usare l’8–21 aprile 2022 per lo sviluppo, riservando gli ultimi giorni alla validazione cronologica. Rifare il fit sull’intera finestra dopo la scelta delle impostazioni. Valutare dal 22 aprile all’8 maggio, separatamente su standard e random. Una prima versione può congelare le storie al 21 aprile durante il test. Nel training ogni riga deve comunque usare solo eventi anteriori al proprio blocco. Applicare lo stesso principio nella validazione.

\- \*\*Join e coorti.\*\* Collegare `video\_features\_basic` tramite `video\_id` con un join molti-a-uno e controllare numero di righe e copertura prima e dopo. Distinguere video mai osservato nel training, video pubblicato dopo il training e video mai visto da quello specifico utente. Tenere separati i profili mancanti, le durate assenti e le durate zero nei log. Verificare i duplicati sulla chiave utente–video–timestamp prima di scegliere come trattarli. Eventi simultanei condividono lo stesso passato.

\- \*\*Disponibilità delle feature.\*\* Le statistiche video mensili includono il periodo di test: usarle per capire i fenomeni e ricostruire le corrispondenti misure predittive dai log passati. I profili utente senza data certa sono utili per descrivere popolazione e sottogruppi. Per usarli nella predizione occorre stabilire che l’informazione fosse disponibile al tempo della decisione.



\### 2. Costruire confronti che isolino il contributo delle feature



| Versione | Informazioni disponibili | Confronto da leggere |

|---|---|---|

| \*\*A · Contenuto e popolarità\*\* | Durata, formato, frequenza di esposizione e tasso storico di long view del tag nella popolazione. Per i video già osservati, anche conteggio e tasso del video con ripiego sul tag. | Quanto si sa del candidato prima di conoscere l’utente? |

| \*\*B · Abitudine personale\*\* | A più tasso complessivo di long view dell’utente e numerosità dello storico. | B − A misura quanto aggiunge sapere che una persona guarda generalmente a lungo. |

| \*\*C · Preferenza utente–tag\*\* | B più tasso personale per i tag del candidato, numerosità e scostamento dalla propensione individuale. | C − B è il confronto centrale per il ranking personalizzato. |

| \*\*D · Estensioni dell’abstract\*\* | Aggiungere a C, una alla volta, affinità utente–autore e storia recente su blocchi precedenti. Registrare la copertura di ciascun segnale. | Quale aggiunta aiuta, per quali utenti e con quanta storia? |



Partire dalla \*\*regressione logistica con regolarizzazione L2\*\* del corso. Usare le stesse impressioni nelle diverse versioni e apprendere trasformazioni e coefficienti sui soli dati di training. Nella logistica additiva, un termine personale uguale per tutti i video dello stesso blocco cambia i punteggi ma ne conserva l’ordine. Questa proprietà rende B un controllo interpretabile.



Per le frequenze rare provare lo \*\*smoothing\*\*. Con `k` visioni lunghe su `n` impressioni passate, usare `(k + m × p) / (n + m)`, dove `p` è un tasso di riferimento calcolato sul passato e `m` il suo peso. Dichiarare quale riferimento si usa a ogni livello e scegliere il peso in validazione. Per un video con più tag, una prima opzione è mediare i tassi dei tag distinti dopo lo smoothing e conservare il supporto. Il video riceve un solo punteggio e una sola etichetta. Per un utente senza storia si torna al riferimento generale.



\### 3. Valutare la previsione e l’ordine dei candidati



\- \*\*Probabilità.\*\* Riportare log-loss e prevalenza per ciascun insieme di test. Una curva di calibrazione permette di verificare, per esempio, quanto spesso si osservi una visione lunga tra le impressioni a cui il modello assegna circa il 30%.

\- \*\*Ranking.\*\* Riportare AUC media entro utente, numero di utenti con entrambi gli esiti e loro copertura. Sullo standard aggiungere un confronto più vicino alla decisione reale: nei blocchi con almeno un positivo e un negativo, misurare la quota di coppie in cui il positivo riceve uno score maggiore, contando le parità per metà. Dare uguale peso ai blocchi oppure dichiarare la ponderazione scelta. L’AUC entro utente confronta anche impressioni di giorni diversi; il confronto entro blocco mantiene fisso il momento.

\- \*\*Incertezza.\*\* Per C − B stimare un intervallo ricampionando gli utenti e mantenendo insieme tutte le loro impressioni. Usare gli stessi utenti estratti per entrambi i modelli. Riportare il guadagno su standard e random, su video nuovi e già osservati, e per quantità di storia utente–tag.

\- \*\***Like e top 10 per i 3 CFU**.\*\* Ripetere il confronto con `is\_like` e aggiungere average precision e prevalenza. Sul random del tab 1 ci sono 239 like da 86 utenti e quasi sempre un solo inserimento random per blocco. Usare quel campione per metriche sulle impressioni con intervalli. Per la top 10 scegliere sullo standard un insieme esplicito, per esempio i video distinti esposti a uno stesso utente in un giorno con almeno dieci candidati. Fissare storia e punteggi a un istante comune, verificare che i video fossero già pubblicati e definire quale impressione rappresenta ogni video. Confrontare sovrapposizione e reazioni osservate al variare del peso tra i due score. È un riordino retrospettivo di candidati osservati; il suo effetto su un feed reale richiederebbe un esperimento online.



\### 4. Dare spessore al notebook



Scrivere in inglese Markdown, figure e commenti destinati alla consegna. Ogni sezione dovrebbe avere una domanda, un confronto verificabile e una breve interpretazione con il suo limite. Rendere il notebook eseguibile dall’inizio con percorso dati, finestre, seed e parametri in una sola cella. Leggere per blocchi i CSV grandi e proiettare solo le colonne necessarie.



Tre risultati meritano spazio nella presentazione: \*\*una tabella A/B/C\*\* con copertura e incertezza, \*\*un grafico di C − B per quantità di storia personale\*\* e \*\*due casi concreti di riordinamento\*\*, uno utile e uno sbagliato. Per ciascun caso mostrare candidati, informazioni disponibili prima del blocco, punteggi ed esiti. Definire il criterio di scelta dei casi per ridurre la tentazione di mostrare soltanto esempi favorevoli.



Le finestre mobili, XGBoost e SHAP offrono approfondimenti coerenti con il corso. XGBoost ha senso se durata e affidabilità dello storico sembrano interagire; va confrontato con la logistica sulle stesse feature. SHAP può spiegare perché il modello ha invertito due video, mentre l’ablazione misura se una famiglia di feature migliora il test. Come alternativa oltre il corso, un \*\*modello additivo generalizzato (GAM)\*\* potrebbe stimare una curva leggibile per la durata e per il supporto storico. Valutarlo se emerge una relazione non lineare che la logistica descrive male. Per qualsiasi tecnica nuova, esplicitare l’ipotesi, il costo, il confronto più semplice e la spiegazione che gli studenti saprebbero dare all’orale.



\*\*Seconda richiesta a Claude.\*\* Restituisci una revisione tecnica ordinata per impatto. Per ogni modifica proposta indica quale errore di interpretazione evita, come verificarla sui dati e quale parte del notebook cambierebbe. Concludi con le decisioni da discutere tra compagni, l’esperimento minimo che consiglieresti e il risultato che giustificherebbe passare a un metodo più complesso.

CV

