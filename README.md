# Guida al laboratorio virtuale del corso di Protocolli e Servizi di Rete
Per realizzare gli esercizi è necessario installare sulla propria macchina alcune applicazioni. Sono disponibili per tutti i sistemi operativi, largamente utilizzate, ben documentate.

## Applicazioni da scaricare ed installare sul vostro computer

Sono elencate di seguito due applicazioni con alcune brevi note sull'installazione e l'uso.

### Docker Desktop ([https://www.docker.com/get-started/](https://www.docker.com/get-started/))

E' l'applicazione che ci serve per virtualizzare una rete di computer ed osservare il funzionamento dei protocolli di comunicazione.

Al link indicato sopra trovate le istruzioni per l'installazione, diverse per i vari sistemi operativi: installate **Docker Desktop** (non Docker Hub, disponibile alla stessa pagina). Per svolgere le attività del corso **non** è necessario nessun tipo di sottoscrizione. Al termine dell'installazione dovreste avere tra le applicazioni Docker Desktop, caratterizzato da una icona con una balena su fondo azzurro.

Avviando l'applicazione appariranno una finestra (la **dashboard**) ed una icona nella cosiddetta **task bar**, in alto o in basso sullo schermo, dipendemente dalla configurazione.

Non dovrebbe essere necessario utilizzare la dashboard, ma l'applicazione deve essere avviata per poter eseguire gli esercizi che richiedono i server virtualizzati. Quindi la finestra può essere subito chiusa, ma nella *task bar* resterà presente l'icona. Per chiudere l'applicazione, nel menu collegato all'icona selezionate **Quit Docker Desktop**. Per accedere nuovamente alla dashboard selezionate **Dashboard**.

Per le attività di laboratorio utilizzeremo le funzionalità di **Docker Desktop** da linea di comando utilizzando un emulatore di terminale (*command prompt* per Windows)

Al termine delle attività di laboratorio è opportuno chiudere l'applicazione *Docker Desktop*, perchè impegna risorse del PC e può interferire con altre applicazioni.

Saltuariamente, la dashboard potrà esservi utile per rimuovere **containers** e immagini di disco (**images**), usando il menu a sinistra.

### Git ([https://git-scm.com/downloads](https://git-scm.com/downloads))

Git disponibile per tutti i principali sistemi operativi. Le semplici istruzioni di installazione sono indicate al link indicato sopra.

E' un'applicazione a linea di comando (quindi si usa da emulatore di terminale) per gestire progetti software. E' uno strumento complesso ma prezioso nell'attività di sviluppo. Per l'attività di laboratorio lo utilizzeremo solo per semplificare l'operazione di download del materiale per le esercitazioni.

Dopo averlo installato potete subito usarlo per scaricare in locale il *repository* git che contiene queste istruzioni e il necessario per creare il laboratorio virtuale. Spostatevi in una directory di lavoro. Poi date il comando:

    $ git clone https://github.com/AugustoCiuffoletti/lr6/

> **Attenzione**: il comando da eseguire **non** comprende il $ iniziale, che di norma viene inserito per indicare che si tratta di un comando da terminale da un account utente (invece il simbolo **#** indica un comando da superutente).

Il contenuto del repository verrà collocato in una nuova directory `lr6`. Ora potete procedere alla costruzione del laboratorio virtuale.

## Costruzione delle due macchine del laboratorio e connessione della rete.

Spostatevi nella directory prodotta dal comando precedente, quindi avviate il laboratorio virtuale:

    $ cd lr6
    $ docker compose up -d

La prima volta che eseguite il comando verranno scaricate le immagini delle due macchine virtuali: serve un po' di tempo e una rete efficiente. Verrà visualizzato l'andamento del download, e al termine le righe:

    Starting server  ... done
    Starting desktop ... done

Per arrestare le macchine virtuali utilizzate la dashboard di **Docker Desktop**. Selezionate lo schermo dei containers e premete il tasto quadrato.

Per riavviarlo utilizzate il comando `docker compose up -d`. 

## Verifica dell'installazione 

Avviate il laboratorio omettendo l'opzione `-d`, cioè `docker compose up`. Il comando non termina, ma verrà visualizzata una riga con il contenuto seguente:

    desktop    | 2022-06-29 07:46:53,819 INFO success: novnc entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)

Ogni 30 secondi verrà poi visualizzata una riga che attesta il buon funzionamento della macchina desktop:

    desktop    | 127.0.0.1 - - [2022-06-29 07:50:23] "GET /api/health HTTP/1.1" 200 122 0.137451
    
E' possibile accedere alla macchina desktop tramite il browser alla URL `http://localhost:6080`. Sulla macchina desktop, aprite un terminale (lo trovate nel menu "System tools" collegato all'icona della rondine, in basso a sinistra) e digitate il comando

    $ ping 172.16.1.3

Se compare una elenco di righe, al ritmo di una al secondo, anche la macchina virtuale server e la rete virtuale sono funzionali.

Il laboratorio è disponibile e configurato per svolgere le attività del corso.

## Analisi del laboratorio

Il laboratorio è composto da quattro macchine virtuali (più propriamente *container*):

- desktop: un sistema ubuntu con desktop grafico
- server: un server generico
- dhcp-server: un server DHCP
- web-server: un server web (nginx)

Per esaminare la struttura del laboratorio utilizziamo la dashboard di "Docker Desktop".

Selezionando **Containers** nel frame di sinistra viene visualizzato un singolo **bundle** contenente i quattro container, collegati insieme sulla stessa rete virtuale. Con un click del tasto destro del mouse sul bundle vengono visualizzati i *container*. Le icone a destra di ciascuno consentono di eseguire delle operazioni sui container, le nostre macchine virtuali: accesso, avvio/arresto, ricarica e rimozione, con icone intuitive. Le icone di accesso possono essere due, se esiste anche un accesso web oltre a quello via terminale.

Ciò accade per la macchina virtuale *desktop*, che ha un tasto che visualizza il messaggio **Open with browser**. Premendolo si viene riportati sul browser, su cui viene aperta la finestra di accesso alla macchina virtuale, come visto sopra. 

Per accedere alla macchina *server* si accede prima alla macchina *desktop*, e su quest'ultima si apre un terminale sulla macchina server con il comando:

    $ ssh server
    
Ricordate che la password è *user*.

In alternativa, dalla dashboard di *Docker Desktop* si può accedere ad un terminale in esecuzione come root. Per passare all'account utente digitare il comando:

    # su -l user 

Le macchine virtuali condividono la directory `shared`. Serve per scambiare contenuti in modo molto semplice tra i container *Desktop* e *Server*. E' anche accessibile dalla macchina *host* con privilegi di super-utente.

La directory condivisa `shared` è collocata in posizioni diverse ma ben visibili:

* nella macchina reale (chiamata **host**) è nella directory da cui avete lanciato il comando `docker compose`
* nel container **server** è nella home dell'utente `user`
* nel container **desktop** è ben visibile sul desktop

I contenuti delle tre directory sono sempre sincronizzati: modificando una si modificano anche le altre. Dalla macchina host sono necessari privilegi di superutente.

La directory `web-data` presente sul container **desktop** ha un funzionamento simile: il suo contenuto è sincronizzato con la directory del container **web-server** nginx contenenti i file HTML, CSS e JavaScript. Il web server è raggiungibile alla URL <code>http://172.16.1.5<code>. Per accedere ai file di configurazione di nginx (non richiesto per le attività di laboratorio in programma) aprire un terminale dalla dashboard dell'applicazione `Docker Desktop`)

### Svolgimento degli esercizi di laboratorio

I repo degli esercizi sono disponibili su github ed hanno in comune il prefisso **https://github.com/AugustoCiuffoletti/** a cui aggiungere il nome del repository (ad esempio https://github.com/AugustoCiuffoletti/stella).

Note:

-) gli esercizi per la copiatura delle chiavi funzionano tanto con nc (anno 2021) quanto con ssh-copy-id (anni precedenti)

