# Progettazione di architetture software sicure

## Progettazione sicura

La progettazione di un sistema software sicuro che garantisca sia proprietà funzionali che quelle di sicurezza dell'infrastruttura entro il quale sarà collocato è un'attività complessa dalla quale però  non è possibile prescindere. L'analisi della sicurezza è un processo ortogonale allo sviluppo, che si rende indispensabile per garantire le proprietà software che si vogliono offrire bilanciandole con l'esposizione al rischio.

## Processo di sviluppo, requisiti e design

Nello sviluppo di software sicuro andrebbe utilizzato un processo di sviluppo ricorsivo nel quale ad ogni definizione di un nuovo modulo va attuata l'analisi della sicurezza.  
Nella definizione dei requisiti connessi alla sicurezza occorre identificare **ciò che va protetto, da chi va protetto e per quanto tempo va protetto e quanto vale mantenere certi dati protetti**

Nella fase di design dell'applicativo occorre identificare:
* come i dati transitano da una componente all'altra
* utenti, ruoli e diritti esplicitamente dichiarati o implicitamente supposti dal design
* ogni soluzione potenzialmente applicabile a problemi individuati
* la relazione di trust di ciascuna componente. 


## Linee guida e principi per la progettazione di architetture software sicure
Nella pratica, sia che si inizi un nuovo progetto o che si debba correggere o aggiornare del software sono utili una serie di domande che i progettisti e gli sviluppatori debbano dare risposta in questa fase.

***Domande sulle vulnerabilità***   
* cosa può andar male? 
* cosa si sta cercando di proteggere?  
* chi pensiamo possa tentare di compromettere la sicurezza?   
* qual è il punto più debole della nostra difesa?

***Domande sulle risorse***
* abbiamo a disposizione un'architettura sicura? È stata già utilizzata?
* abbiamo accesso a librerie di software riutilizzabile?
* quali linee guida e standard abbiamo a disposizione?
* esistono esempi simili a cui ispirarsi?

***Domande sul software***
* a che punto della catena di trust si colloca il software?
* esistono applicazioni critiche successive che fanno affidamento sul software per l'autenticazione?
* esistono librerie o utility a livello superiore che potrebbero fornire input non affidabili?
* chi sono gli utenti legittimi?
* chi ha accesso al software (sorgente o eseguibile)?
* si prevedono cambiamenti futuri? che impatto questi avrebbero sulle assunzioni iniziali?
* qual è l'ambiente in cui il software viene eseguito?

***Domande sui goals***
* qual è l‘impatto che avrebbe un attacco alla sicurezza?
* quali qualità del software (i.e. usabilità, efficienza, accessibilità, riuso, ecc.) vengono compromesse dalle misure di sicurezza adottate?
* se l'utente ignora o viola le misure di sicurezza, come dovrebbe reagire il sistema? E come vengono rilevate queste violazioni?

## Principi per architetture sicure
**Livello di sicurezza**   
Quanto deve essere sicuro il software? *"**Just secure enough**"* ossia sicura il sufficiente. Rendere l'applicazione il più sicura possibile può causare il fallimento della progettazione

**Progettare con in mente il nemico**  
*Adversary principle*: progetta il software come se il nemico più astuto lo attaccherà. 
Il progettista dovrebbe anticipare gli attacchi di "avversari intelligenti, razionali e irrazionali" (GASSP Generally Accepted System Security Principles) e prevedere possibili soluzioni
	
**Conoscere e rispettare la chain of trust**  
*Non invocare programmi non fidati da programmi fidati*  . Spesso violato quando un’applicazione invoca un comando di sistema per eseguire un’operazione. Es: Vulnerabilità della utility man di Unix  
*Regola generale*: un programma NON deve delegare l’autorità di fare un’azione senza delegare anche la responsabilità di controllare se l’azione è appropriata  
Un’applicazione rispetta la chain of trust se:
* valida tutto quello che riceve in input
* non passa tasks ad entità meno fidate
* emette informazioni tanto valide e sicure quanto quelle prodotte dalle altre risorse  

Altra direzione della chain of trust e di ripulire tutti i dati esterni prima di eseguire un programma, ad es:
* inizializzazione e configurazione di file
* argomenti su linee di comando
* nomi di file
* URLs …

**Privilegi adeguati**   
Un’applicazione necessita di adeguati privilegi e accessi per operare
Seguire il *principio del minimo privilegio*
* Per leggere un record non aprire il file con accesso anche in scrittura
* Per creare un file in una directory condivisa, fa uso del "group access“ o delle liste di controllo d’accesso

**Test delle azioni contro la policy**   
*Principio di mediazione completa*: passo per passo, occorre testare ogni azione che è possibile tentare contro la policy

**Offuscamento delle informazioni**   
*Principio del non offuscamento*: nascondere la modalità di funzionamento di una componente software o il registro in cui è memorizzato un particolare parametro di policy è pericoloso.
La segretezza non è affidabile come unico mezzo di difesa, ma può essere utile per ingannare un attaccante

**Mantenere lo stato minimale**   
*Principio del minimal retained state*: un programma deve tenere in memoria lo stato minimale (*Stato* = informazione che un programma deve tenere in memoria durante un transazione o esecuzione di un comando)  
* Contro esempi sono SYN flood attacks o CGI scripts attacks

**Livello di fault tolerance**
Un sistema software, in base alle certificazioni (CERT) deve garantire le tre *R*
* *Resistance*: la capacità di impedire un attacco
* *Recognition*: la capacità di riconosce attacchi e la misura del danno
* *Recovery*: la capacità di fornire servizi ed assetti essenziali durante un attacco e ripristinare tutti I
servizi dopo l’attacco

**Appropriato error-handling**   
Molti sistemi vengono compromessi a causa di improprio trattamento di errori inaspettati (eccezioni). Una corretta pianificazione dell’error-handling coinvolge
* *Architetto*: decide la progettazione generale del trattamento di errori
* *Designer*: decide come l’applicazione rileva un fallimento; come discrimina tra vari casi; ed il meccanismo di risposta
* *Programmatore*: cattura le condizioni di decision/triggering e realizza il design
* *Operatore* controlla se processi critici vengono interrotti e opportuni messaggi appaiono in console o vengono generati file di log

**Graceful degradation**  
*Principio di graceful degradation*: quando si verifica un guasto, il sistema non si ferma ma continua ad operare in modo ristretto o con funzionalità ridotta (degraded)
* Esempio di design senza graceful degradation.
	* Packets del protocollo TCP
* Esempio di design con graceful degradation
	* Gestione della memoria nel sistema operativo VMS della Digital
	
**Fallimento sicuro**  
*Principio di fault safely*: in caso di fallimento, un sistema deve terminare in una configurazione sicura
* Se il server su cui è il firewall muore, la rete deve essere lasciata chiusa
* Se il programma che controlla il time lock di una cassaforte fallisce, la cassaforte deve rimanere chiusa
* Se fallisce il programma di controllo di una porta a chiusura elettronica, la porta deve rimanere aperta

**Misure di sicurezze adeguate all’utente**
*Principio della accettabilità psicologica*: è importante usare modelli mentali e paradigmi tratti dal mondo reale. 
Ad esempio: seleziona un’interfaccia utente che renda facile fare le cose corrette. Se le misure di sicurezza di un sistema sono così onerose ed
irritanti che l’utente le evita, la sicurezza del sistema è compromessa

**Responsabilità individuali**   
*Principio di accountable individuals*: una architettura di successo deve garantire che gli individui siano responsabili delle proprie azioni    

Questo principio richiede che:
* ogni utente abbia ed usi un account personale
* sia difficile per un utente spacciarsi per un altro
* deve essere chiaramente stabilita la responsabilità della sicurezza delle risorse coinvolte

**Limite sulle risorse**
*Principio di resource-consumption limitation*: usare le funzionalità che il SO fornisce per limitare il consumo di
risorse del sistema. La limitazione del consumo di risorse deve essere combinata con un significativo ripristino da errore (error recovery)
* Adottare opportune contromisure che rilevano e gestiscono i casi di raggiunto limite
* Usare tecniche di graceful degradation

**Ricostruzione di eventi**
*Principio di auditability*: deve essere possibile ricostruire la sequenza di eventi che hanno condotto a certe azioni chiave (es. cambio di dati). Questo principio richiede che l’applicazione e il sistema di host creino e mantengano gli audit log