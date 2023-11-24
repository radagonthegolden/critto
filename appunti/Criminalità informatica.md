# Capitolo I & II
- Aumento netto dei cyber attacchi nel periodo 2018-2022
	- La maggior parte in America all'inizio, poi aumenta anche l'EU
		- Ragioni: difese più alte in America, maggiore digitalizzazione in Europa
	- I malware sono ospitati al 31% in Russia, 25% USA. Nota: non vuol dire che gli aggressori si trovino in quel paese
	- La maggior parte degli attacchi sono Malware, Phishing e Account Cracking. Tecniche a basso costo per l'attaccante
		- Sfruttano l'incapacità di tenere tutti gli account aggiornati e sicuri
		- Il Cybercrime opera con le stesse idea di un'azienda normale *massimo risultato per minimo sforzo*
	- Al primo posto degli attacchi ci sono Multiple Targets, tra cui Governo e Militari per via dell'invasione in Ucraina. 
		- C'è stato un gran salto sul campo Healthcare
	- Le tecniche che funzionano di più sono le stesse di 30 anni fa, spesso banali e obsolete
- Bisogna implementare logiche di **security by design**
	- Cambio di logica: non più la cybersecurity deve essere una cosa "forzata" dalla legge ma implementata dei business per loro interesse
		- I settori più colpiti sono infatti quelli con meno regolamentazioni e prescrizioni
- La maggior parte dei cybercrime riguardano l'ecommerce, ma quelli dove si rubano più soldi sono di gran lunga quelli legati al trading e investimenti
- Molti reati tradizionali diventano digitali
	- Violazione di Domicilio $\implies$ Accesso abuso del sistema
	- Truffa $\implies$ Truffa informatica
	- Danneggiamento $\implies$ Danneggiamento di informazioni e dati
- Caso Joker
	- Gruppo di truffatori di basso rango che si evolve in ambito internazionale con grandi organizzazioni cybercriminali
- L'ingegneria sociale è spesso la causa di un crimine informatico, non violazioni "tecniche" degli algoritmi di sicurezza
- **Phishing**: mandare mail dove ci si spaccia per qualcun altro un'istituzione per rubare dati alle vittime. Si tratta di **social-engineering**, perché la vittima stessa rivela i dati. Non c'è una vulnerabilità in un algoritmo da sfruttare.
	- Phishing B2B (business to business): un criminale che crea kit per fare phishing ad altri criminali
	- **Spear Phishing:** phishing mirato ad una singola persona usando informazioni su di loro (tipo se ha figli, dove lavora)
	- **Smishing:** phishing fatto con SMS
	- **Vishing:** phishing fatto con vocali
	- **QRishing:** phishing fatto con QR code
	- **Kevin Mitnick:** detto *Condor* e autore dell'*Arte dell'inganno*, libro in cui racconta come ha violato server di multinazionali per poi essere ricercato dall'FBI
	- Scarsa cooperazione internazionale 
	- Il 97% degli URL di phishing usano HTTPS, che normalmente dovrebbe dare sicurezza
		- Infatti i siti finti di phishing usano certificati **Domain Validation**, comprabili a pochi euro, a volte gratis
		- Si dovrebbe controllare che il sito possegga un certificato **Organization Validated** o ancora meglio **Extended Validation** 
- Attacchi indiretti: violare direttamente un'azienda che ha preso delle precauzioni può essere difficile. Quindi spesso vengono eseguiti dei **supply chain attack** dove si attacca un fornitore o un cliente dell'azienda (per poi arrivare a quest'ultima)
	- Esempio: nel 2011 un attacco riuscii a penetrare il colosso di cybersecurity americano RSA, per rubare i seed per la generazione di Token usati per identificarsi. Loro non erano però il vero obbiettivo: il bersaglio era la Lockheed Martin, azienda militare. Gli attaccanti, probabilmente stati ostili agli USA, volevano rubare i dati della LM per scopi strategici.

# Capitolo III
- **BEC:** Business Email Compromise, ovvero il criminale è entrato nel sistema email aziendale
	- Una truffa possibile è quella in cui il "CEO" di un'azienda manda una mail ad un impiegato di basso livello, che quindi si sente in dovere di fare ciò che dice
		- Esempio: *Ho bisogno di soldi per dei regali da fare a clienti ma sono senza al momento. Mi fai un bonifico? Sono in riunione quindi non rispondo al cellulare. Nel pomeriggio te li rimborso*
	- Il criminale può anche inviare fatture false, richieste di fondi, o altri danni
	- Siccome sono effettivamente mail aziendali i filtri anti-spam non funzionano
	- Oltre a perdite dirette, un'azienda colpita da un BEC rischia di perdere reputazione e clienti, nonché di subire multe legali
	- Si tratta del secondo più grande tipo di cyberattacco, particolarmente in aumento in Italia
	- Le BEC si dividono a grande linee in due tipi:
		- **BEC-as-a-service:** "Attacchi "di massa", dove si inviano tantissime mail a tante aziende con un account anonimo. La stragrande maggioranza verrà ignorata, ma anche solo pochi abboccano risulta comunque lucrativo
		- Attacchi più mirati dove prima si viola una casella legittima, da cui far partire l'attacco che in generale riguarda furto di soldi o installare un malware
	- Siccome allegati da scaricare possono essere infetti, di solito le BEC non li contengono, in modo da far abbassare la guardia ai dipendenti
	- Possono essere molto efficaci se bonifici e trasferimenti bancari sono operazioni che il target compie giornalmente
	- Modi per proteggersi:
		- Usare email con accesso robusto e password forti
		- Non usare webmail, facili da attaccare
		- Essere scaltri con mail sospette: chiama il mittente al telefono, vai nel suo ufficio in persona
		- Verificare bene la mail che è arrivata
	- **Business Executive and Attorney Impersonation:** variante della BEC in cui il criminale si finge il rappresentante di uno studio legale e fa pressione sul bersaglio, spesso dicendo che si tratta di roba urgente
	- **Data Theft:** altra variante della BEC in cui si rubano dati
-  **Man in the Middle:** attacchi in cui un terzo si pone tra due persone, potendo ascoltare o alterare la conversazione. 
	- **Eavesdropping:** letteralmente "origliare", si tratta di un attacco in cui il terzo ascolta la conversazione. Facile da eseguire su Wi-Fi con access point non criptati
	- Per combatterlo si usa la **certificazione**, per esempio nel TLS
	- Per esempio se l'attaccante entra in contatto con l'azienda $X$ fingendosi l'azienda $Y$ (di cui $X$ si fida), può dire "questo mese pagatemi a quest'altro indirizzo bancario" per poi rubare i soldi
	- **Man in the Browser:** attacco MITM in cui il criminale si pone tra un browser e un web server
		- Esempio: malware che aspetta che tu ti connetti alla tua banca, per poi mettersi in mezzo. L'unico modo che hai per capire che qualcosa non va è che il protocollo che normalmente è HTTPS è HTTP. Quando cerchi di fare il bonifico, questo viene modificato per dare tutti i tuoi soldi al criminale, senza che tu possa rendertene conto. 
		- Non ci sono molte difese nel caso in cui l'attaccante stia solo cercando di leggere i tuoi dati
		- Un modo per difendersi dal **tampering** (modifica dei dati) è invece la **2 factor authentication**, dove è richiesto all'utente di autorizzare la transazione da un dispositivo diverso. Nell'esempio di prima, se per fare il bonifico fosse richiesto il telefono e questo indicasse chiaramente a chi arrivano i soldi e quanti, ti renderesti conto della truffa
			- Chiaramente il telefono non deve essere infetto e l'utente deve leggere bene le notifiche. Alcuni dicono che richiedere questo livello di controllo faccia perdere fiducia agli utenti
		- Blowlocking #todo capire che cazzo è
		- Se un utente nega di aver fatto un pagamento (perché truffato) è responsabilità della banca provare che in realtà l'ha fatto è che la procedura:
			- è stata autenticata
			- è stata correttamente registrata
			- è avvenuta senza malfunzionamenti
		- Se così non succede la banca deve ridarti i soldi
		- Le responsabilità dell'utente sono quindi solo
			- Usare gli strumenti di pagamento come richiesto
			- Denunciare furto o truffe velocemente
		- In generale la responsabilità è dell'istituzione, visto che considerata **la più adatta a gestire il rischio**
			- Questo perché è chiaro che è l'azienda a sapere meglio, in media, quanti soldi vengano persi per cyberattacchi, e quindi è lei quella meglio in grado di spalmare questi su tutti i suoi utenti

# Capitolo IV
- Il fattore umano è coinvolto nell'85% dei crimini informatici del 2021
- Gli attacchi sono spesso a tre fasi:
	1. Invio di mail phishing in grado di installare malware
	2. Download di malware in grado di rubare le credenziali
	3. Uso delle credenziali per fare danni
- Di solito la SI è usata in due modi
	1. Si salta la parte di cracking vero e proprio, arrivando direttamente ai risultati
	2. Viene usata per aiutare la parte di cracking, attaccando degli intermediari
- Nel 2015 è stata violata la mail del capo della CIA
	- Per fare ciò gli hacker hanno usato SI contro l'ISP della CIA e il loro provider mail (America-On-Line)
	- Un gruppo di adolescenti ha rivendicato l'attacco, anche se si fa fatica a credere
	- Questo attacco mostra come anche un bersaglio iper-protetto sia vulnerabile se i suoi collaboratori sono colpiti
- **Sim Card Swapping:** trasferire il numero di telefono di una SIM ad un'altra. Il criminale trasferisce il numero di telefono della vittima ad una SIM che possiede
	- Per fare ciò si può usare SI, corrompere chi lavora in uno store (che di solito non prende molto, quindi è attirato da soldi facili), con un documento falso oppure per semplice incompetenza dei dipendenti del SIM store
		- Per esempio il criminale poteva chiamare o presentarsi allo store dicendo di aver perso la SIM, oppure di voler cambiare operatore. Se gli veniva chiesto un documento poteva usarne uno falso o rubato. Sempre che glielo chiedessero
	- Ottenuto il numero di telefono della vittima questa è vulnerabile ad attacchi che gli richiedono la 2FA
	- Il SIM Swapping è molto vecchio, ma è tornato in auge per via della proliferazione dei crypto wallet
	- Per aumentare la sicurezza, ora per ottenere una nuova SIM ad uno store viene richiesto lo SPID, non più foto di documenti che potrebbero essere falsate
- **Steganografia:** un gruppo hacker (WOROC) ha ideato una tecnica per mettere pezzi di virus dentro immagini PNG
	- I bersagli sono stanti enti governativi in Medio Oriente, Sud Est asiatico, Africa

## Casi di attacco
### Caso 1
Organizzazione:
- **Programmatore:** esperto che crea siti falsi su uno spazio web non rintracciabile
- **Procuratore di numeri:** persona che procura tanti numeri cellulari, meglio ancora se i bersagli hanno la banca connessa al cellulare. Di solito queste liste sono prese da Call Center, oppure dalla banca stessa tramite un'agente al loro interno
- **Soggetti con ottima dialettica:** esperto di sociali engineering
- **Soggetti senza competenze:** usati per prelevare i soldi agli sportelli

L'organizzazione attiva poi un **servizio VOIP** con numero modificabile, per esempio possono usare quello della banca. Con questo numero viene attivato un service di **bulk messaging** per inviare tanti messaggi infetti.

In questo caso studio i messaggi erano tipo: *Gentile cliente, la sua carta è stata bloccata per mancata sicurezza web psd2 antifrode. Verifica e riattiva ora: bit\.ly/XXXXXXXXX-*

A questo punto venivano inviato su un sito fake ostato su un dominio gratis. Venivano presi solo nome, cognome e numero di telefono però. A questo il sito diceva che un operatore avrebbe chiamato a breve. 

Il soggetto con ottima dialettica li avrebbe chiamati, rassicurandoli. A quel punto alla vittima arriva un codice SMS che gli viene chiesto di comunicare ai truffatori. Se ciò viene fatto questi riescono ad installare l'app di home banking della vittima sul loro telefono. 

Infine il truffatore avvisava la vittima che era tutto a posto ma non sarebbe stato possibile usare il servizio di home banking per 48 ore (falso).

A questo punto il soggetto senza competenze veniva inviato a prelevare dagli ATM, fare bonifici, o in generale rubare soldi senza venire sgamato.

### Caso 2
Attacchi fatti per rubare account WhatsApp.

Anche in questo caso su è usata la SI per rubare i codici di attivazione di WA. La 2-factor-authentication di WA risulta un tallone d'Achille: dopo aver rubato l'account, i criminali attivano la 2FA in modo che la vittima non possa recuperare l'account.

Dopodiché inviano a tutti i contatti messaggi simili cercando di colpire più persone. Messaggi del tipo *scusa mi sono confuso, potresti reinviarmi il codice che ti è arrivato tramite SMS?*

WA ora ha l'opzione di poter riattivare l'app sul telefono delle vittime anche senza il codice, ma bisogna aspettare 7 giorni. 7 giorni in cui il criminale fa quello che vuole.

Inoltre ora WA manda il codice della 2FA direttamente sull'app, senza passare da SMS, fintanto che WA è attivo e il cellulare connesso alla rete. Questo aumenta la protezione: se gli SMS sono stati violati magari comunque i criminali non hanno accesso a WA.

### Caso 3
Google è in realtà molto buona a proteggere gli utenti da danni fatti da app scaricati dal Play Store, non possono fare nulla però nel caso di APK scaricate senza usarlo. 

Nel 2021 iniziarono report di smishing atto a rubare dati di carte di credito e per 2FA. Si notò come i paesi colpiti erano più che altro occidentali: Italia, Spagna, Germania, ma anche Ungheria e Polonia. Esclusi erano gli ex paesi URSS, infatti si ritiene che l'attacco sia originato lì. Infine l'Europol riuscì a smantellare i server olandesi presso cui era ospitato il malware definito FluBot.

Le campagne FluBot italiane hanno interessato DHL, UPS, Amazon, più che altro corrieri. FluBot si fingeva una di queste aziende, spacciandosi per un'app per tracciare il pacco, per poi intercettare SMS e diffondersi.

FluBot non violava falle nel sistema operativo, prendeva il controllo dei **servizi di accessibilità** come lettore dello schermo o tastiere automatiche per i disabili.

### Caso 4
Tutto cominciava con una farmacia che riceveva mail di phishing che riportavano guasti nel portale per l'inserimento dell'esito del tampone. Alla risposta da parte del personale della farmacia, seguiva una telefonata di criminale che si fingeva tecnico del Sistema Sanitario Regionale. 

Il personale medico veniva convinto ad installare software come Anydesk o Teamviewer, fatto ciò gli veniva chiesto di inserire l'ID per la connessione terminale farmacia-portale sistema sanitario.  

Siccome il personale non si ricordava quasi mai tale password, dovevano accedere al password manager del browser per ottenerla. Fatto ciò anche il finto tecnico ne aveva accesso.

Con queste password potevano produrre green pass falsi ma anche svuotare i conti in cui erano entrati.

Il servizio usato all'inizio era lufix.to, un sito apertamente criminale: 

*At Lufix.to you can Buy Spamming Tools, SSH, Shells, RDP, Cpanel, Mailer, SMTP, Leads, Email:password, Combo, FULLZ*

  


