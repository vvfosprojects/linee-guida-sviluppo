# Linee guida per lo sviluppo applicativo nel Corpo Nazionale dei Vigili del Fuoco (CNVVF)

## Introduzione

Scopo di questa circolare è la diffusione di una serie di linee guida e metodologie, in allegato, a cui attenersi sistematicamente nell'ambito di qualsiasi attività di produzione di sistemi software condotte nell'ambito del Corpo Nazionale dei Vigili del Fuoco, che conferiscano soddisfacenti livelli di qualità, tra i quali in particolare: correttezza, efficienza, robustezza, sicurezza, uniformità ed integrabilità nelle infrastrutture istituzionali, manutenibilità, estensibilità, documentabilità, sostenibilità, ottemperanza alle normative vigenti in tema di sicurezza informatica.

## Architetture applicativa e di sistema

Le procedure sono sviluppate in tecnologia Web e sono pertanto fruibili, senza limitazioni, attraverso i più comuni web browsers:

* Mozilla Firefox
* Google Chrome
* Opera
* Safari
* Internet Explorer 11+.

Vi può essere eventualmente la possibilità di avere un front-end basato su terminali mobili (per es. Android, iOS).

È da evitare l'installazione di componenti software o hardware presso le sedi territoriali. Non devono essere necessari privilegi di esecuzione elevati sui sistemi deputati all'esecuzione delle procedure.

### Integrazione applicativa

Deve essere sistematicamente realizzata l'integrazione con i sistemi istituzionali in tutti i casi di accesso a dati di competenza delle altre procedure; ciò vale in special modo per l'autenticazione dell'utente e per l'accesso alle anagrafiche dipartimentali (personale, mezzi, sedi di servizio, anagrafica territorio, ecc.). Detta integrazione è basata su servizi web, secondo il paradigma delle Service-Oriented Architectures (SOA). Se non esistenti, va concordato lo sviluppo dei web services utili ad una corretta integrazione applicativa.

Specularmente, la procedura informatica deve esporre i servizi web utili alla condivisione del dato di sua competenza.

In entrambi i casi, i servizi web sono realizzati a _grana fine_, nel senso che deve essere possibile accedere a dati elementari e deve essere evitata la modalità di esportazione massiva di dati a beneficio di un'altra applicazione. Per questo motivo, i singoli servizi web devono essere realizzati secondo stringenti requisiti di efficienza (non più di pochi millisecondi per la risposta corrispondente ad ogni chiamata), eventualmente facendo intenso uso di strategie di caching dei dati.

### Il livello di persistenza

Il database applicativo rappresenta la principale area di archiviazione delle informazioni strutturate gestite a cura della procedura. Tale database non deve contenere informazioni di competenza di altre procedure, ma esclusivamente riferimenti a queste mediante uso di codici di dominio (per es. codici fiscali, codici ISTAT, codici sede). Non vanno realizzate attività di sincronizzazione periodica dei database con i dati di competenza di altre procedure. Non devono essere realizzate forme di integrazione applicativa a livello database. Il database applicativo, deve anzi essere considerato una risorsa strettamente privata ad uso della sola procedura informatica a cui appartiene. Di conseguenza, non deve essere consentito l'accesso al database da parte di altre procedure, né deve essere consultato direttamente il database di altre procedure. In caso di utilizzo di database relazionali, l'integrazione della procedura con le funzionalità di archiviazione deve auspicabilmente avvenire con la mediazione di un middleware di persistenza (ORM), che astragga le funzionalità del database e incoraggi l'uso corretto delle sue funzionalità e l'aderenza agli standard.

### Funzionalità di ricerca

Vanno realizzate con estrema cura le funzionalità di ricerca del dato, poiché ricerche efficienti migliorano sensibilmente la produttività nell'uso dell'applicazione. Per questo motivo, deve essere possibile sistematicamente effettuare ricerche evolute all'interno dei dati di competenza dell'applicazione. Le ricerche devono basarsi sul principio del raffinamento successivo: ad una ricerca di massima, alimentata inizialmente da un dato il più possibile elementare (per es. una stringa di ricerca destrutturata), corrisponde una prima risposta non necessariamente precisa, ma che consenta di selezionare opportune partizioni del dato (per es. partizioni temporali, spaziali, tipologiche, ecc.) mediante raffinamenti successivi. Per la realizzazione di queste funzionalità è possibile trarre vantaggio dall'uso di specifici strumenti software liberi appositamente disponibili e afferenti al campo delle ricerche a testo libero (full-text search, faceted search, ecc.).

### Reportistica

Nella realizzazione di funzionalità di reportistica applicativa, piuttosto che l'integrazione diretta nella procedura stessa, va considerata l'opportunità di utilizzare appositi sistemi di reportistica e business intelligence. I vantaggi nell'uso di tali sistemi risiedono principalmente in:

* alta flessibilità di tali sistemi a beneficio dell'esperienza d'utente;
* riduzione delle risorse di sviluppo necessarie alla realizzazione della reportistica;
* disaccoppiamento delle funzionalità di analisi dei dati (OLAP) da quello della sua gestione dettagliata (OLTP), che scongiura un appesantimento del carico imposto al database applicativo.

### Cloud-enabling

Le applicazioni devono essere nativamente cloud-enabled. In particolare devono essere rispettati i seguenti requisiti.

* La procedura non deve conservare stato nella memoria volatile degli application server; in particolare non devono essere memorizzate informazioni sul contesto d'utente al fine di scongiurarne la perdita in caso di riavvio degli application server oppure di aggiornamento applicativo.
* La procedura non deve salvare informazioni sul file-system locale.
* La procedura può salvare logs applicativi su file-system locale, ma deve essere all'occorrenza predisposta ad indirizzare questi logs verso un apposito log-server.

## Metodologie di sviluppo

L'ambiente eterogeneo, altamente specialistico e fortemente dinamico che si configura nel CNVVF, impone l'utilizzo di metodologie agili per lo sviluppo applicativo. Si incoraggiano pertanto le tecniche di sviluppo [SOLID](https://en.wikipedia.org/wiki/SOLID), l'utilizzo del Test Driven Development, l'uso di sistemi di versionamento del codice evoluti, la continuous integration.

## Linguaggi di sviluppo

Considerando che i linguaggi di sviluppo utilizzati hanno un forte impatto sui processi di manutenzione correttiva ed evolutiva, è necessario evitare la proliferazione di diversi linguaggi di sviluppo, convergendo su un ristretto sottoinsieme delle numerose tecnologie software oggi disponibili. Si auspica pertanto l'utilizzo dei seguenti mix tecnologici.

### Backend

Il backend deve essere realizzato conformemente al paradigma della programmazione orientata agli oggetti (OOP). I linguaggi da utilizzare sono i seguenti.

* Java 8+ su application server Tomcat o WildFly
* C# .NET Core 2.2+

### Frontend

* HTML/CSS
* Bootstrap 4+
* Angular 6+
* jQuery3+
* JSF PrimeFaces

Possono essere considerate proposte di estensione della suddetta rosa di linguaggi di sviluppo. L'estensione deve comunque deve essere necessariamente supportata da specifiche attività di formazione a potenziamento delle competenze del Personale in forze al CNVVF.

## Database

Le tecnologie database candidate all'utilizzo sono le seguenti.

### Database relazionali

* Postgresql 9+

### Database documentali

* MongoDB 4+

## Ricerca a testo libero (full-text search)

Per la ricerca a testo libero è utilizzabile il prodotto Apache Solr 7+.

## Sicurezza applicativa

Le applicazioni devono essere sviluppate nel rispetto delle [linee guida sulla sicurezza applicativa emanate da AGID](https://www.agid.gov.it/it/sicurezza/cert-pa/linee-guida-sviluppo-del-software-sicuro), seguendo l'impostazione _by-design_ e _by-default_, come suggerito dalla normativa EU/2016/679.

Le funzionalità di autenticazione applicativa (Authentication) vanno realizzate mediante l'integrazione con il sistema Single-Sign-On dipartimentale. Le procedure non devono in nessun caso manipolare, o semplicemente ricevere, le credenziali dell'utente, neanche per la trasmissione a servizi terzi di autenticazione. L'autenticazione dell'utente alle differenti procedure, o web services da queste esposti, deve avvenire mediante uso di authentication-token (per es. JWT). Le funzionalità di autorizzazione (Authorization) devono essere basate esclusivamente sulle informazioni fornite dalla funzionalità di autenticazione.

Nel caso in cui l'applicazione esponga dati in forma dettagliata o sintetica, è opportuno indicare la licenza con cui tali dati vengono distribuiti, privilegiando l'approccio [Open Data](https://www.dati.gov.it/), così come indicato da AGID.

Sulle procedure realizzate, preventivamente alla messa in esercizio, vengono eseguiti da parte dell'Ufficio per i Servizi Informatici penetration tests e vulnerability assessment, al fine di individuare vulnerabilità che possano compromettere i requisiti di disponibilità e riservatezza della procedura e dei dati da essa trattati.

## Versionamento del codice

Fin dalle prime fasi di sviluppo della procedura, e della annessa documentazione, va utilizzato il sistema di versionamento del codice GIT. In ottemperanza all'Art. 69 del Codice dell'Amministrazione Digitale, vige l'obbligo per l'Amministrazione di rendere pubblicamente disponibile il codice sorgente dell'applicativo. [Come suggerito da AGID](https://lg-acquisizione-e-riuso-software-per-la-pa.readthedocs.io/it/latest/attachments/allegato-b-guida-alla-pubblicazione-open-source-di-software-realizzato-per-la-pa.html) è opportuno localizzare la repository del progetto su un portale di versionamento pubblico (GitHub, BitBucket, GitLab, ecc.). La licenza da adottare è preferibilmente è la [Affero General Public License v3.0](https://www.gnu.org/licenses/agpl-3.0.en.html) (AGPL v3.0). In alternativa, in caso di motivate ragioni di ordine e sicurezza pubblica, o difesa nazionale, l'Ufficio per i Servizi Informatici rende disponibile un sistema di versionamento GIT ad accesso controllato.

È necessario rimuovere dal codice sorgente qualsiasi password o certificato o altra credenziale relativa a sistemi reali (anche di test); a tale scopo si deve ricorrere a file di configurazione separati o a blacklist nel sistema di controllo di versione (ad esempio, il file .gitignore). Qualora si intenda integrare il repository con un meccanismo di deployment automatico e dunque si necessiti di mantenere delle credenziali, è possibile utilizzare i meccanismi sicuri di cifratura previsti per la piattaforma di code hosting e per i sistemi di continuous integration adottati (ad es. git-crypt).

È importante verificare che non si siano depositate per errore tali credenziali (API keys, secrets, password, ecc.) all’interno del repository, non solo nella versione corrente ma anche in revisioni precedenti.

Deve essere evitata se possibile la riscrittura di algoritmi già disponibili in librerie open source esterne (ad esempio: crittografia, sanitizzazione dell’input, protocolli di rete, parsing di XML o altri formati, gestione della memoria eccetera). L'uso di librerie standard, diffuse e aperte migliora la produttività, la correttezza e la manutenibilità del prodotto finale.

Tutto il codice _morto_, ovvero non utilizzato, deve essere rimosso poiché potrebbe portare a confusione od essere considerato mantenuto ed erroneamente reintegrato senza i necessari controlli.

## Documentazione applicativa

Ogni procedura deve essere corredata dal relativo documento di procedura, secondo il modello realizzato dalla DCRLS/USI e disponibile all'indirizzo [http://intranet.dipvvf.it/linee-guida-sviluppo-applicativo](http://intranet.dipvvf.it/linee-guida-sviluppo-applicativo). Tale documento, in particolare, richiede di dettagliare i seguenti aspetti.

* la descrizione delle funzionalità del sistema e dell'utenza alla quale si rivolge;
* l'indicazione del livello di qualità del servizio ritenuto soddisfacente;
* il dettaglio dell'architettura applicativa;
* il funzionamento dei singoli componenti;
* la descrizione di tutte le eventuali integrazioni con altri sistemi, interni o esterni al CED dipartimentale;
* i requisiti dettagliati dell'ambiente di esecuzione (Application Server);
* i requisiti dettagliati delle postazioni client;
* la natura, l'entità ed il trend di crescita delle informazioni archiviate su memoria di massa;
* la natura e l'entità del traffico di rete generato in condizioni ordinarie o straordinarie;
* la natura e l'entità del traffico generato verso i database server, unitamente al trend di crescita della mole di dati archiviata;
* la descrizione dettagliata di eventuali, inevitabili, procedure schedulate che coinvolgano la gestione massiva di informazioni;
* la descrizione dettagliata delle procedure di compilazione del codice e deployment sui server di pre-esercizio ed esercizio, con le relative procedure per il testing delle funzionalità;
* le caratteristiche delle procedure di backup da predisporre a fini di disaster-recovery.

La documentazione dettagliata del codice deve essere inoltre considerata parte integrante del progetto software, senza la quale sono seriamente compromesse le caratteristiche di correttezza e manutenibilità della procedura. È fortemente auspicato un approccio alla documentazione _in linea_, dove i singoli moduli software vengono realizzati direttamente insieme alla relativa documentazione, contestualizzate nello stesso frammento di codice. Ciò anche al fine di abilitare metodologie automatizzate di produzione della documentazione (per es. JavaDoc) e sistemi automatici di rilevazione di errori o obsolescenza nella documentazione.

Il codice sorgente deve essere correttamente indentato, preferibilmente con sistemi automatizzati. Vanno auspicabilmente adottati sistemi di formattazione automatica e standardizzata del codice sorgente.

## Manualistica

Ogni procedura deve essere corredata da una manualistica sull'utilizzo di qualità soddisfacente. È particolarmente incoraggiata la redazione di manuali in formato audio/video che descrivano le principali casistiche di utilizzo, per classi di utenza, da privilegiare rispetto a documenti prevalentemente testuali (per es. PDF). Tali contributi devono auspicabilmente essere brevi (qualche minuto al max.), focalizzati sulla singola funzionalità e ben indicizzati per una consultazione estemporanea. Questo tipo di risorse rappresenta infatti un valido supporto per la formazione iniziale dell'utenza, per la consultazione regolare durante l'uso del programma e per la formazione dei nuovi utenti che possono subentrare sporadicamente, anche molto tempo dopo l'iniziale adozione ufficiale dell'applicativo nel CNVVF.

L'applicazione deve rendere disponibili, accanto ai controlli di interfaccia grafica, scorciatoie contestuali verso la manualistica in formato digitale, consultabili dagli utenti su necessità.

## Assistenza alla procedura

Contestualmente alla messa in esercizio della procedura, va predisposto uno sportello di assistenza applicativa. L'assistenza è erogata preferibilmente mediante una casella di posta elettronica, con la possibilità di attivare anche un canale telefonico in caso di elevata urgenza della problematica riscontrata e della criticità della procedura coinvolta. Tale aspetto va analizzato fin dalle prime fasi di sviluppo della procedura. In prima istanza bisogna puntare a limitare al massimo, con una buona progettazione, l'esigenza dell'utente di ricorrere agli sportelli di assistenza. In ogni caso deve comunque essere assicurata la disponibilità di uno sportello competente a risolvere efficacemente le problematiche di natura applicativa.
