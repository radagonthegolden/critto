Obbiettivi: 
- **Confidenzialità:** terzi non devono capire il messaggio
- **Autenticità:** devo essere sicuro da chi viene il messaggio
- **Integrità:** devo essere sicuro che terzi non abbiano cambiato il messaggio
- **Non ripudio:** non si può negare di aver inviato un messaggio

**Chiave:** un cifrario (ovvero un protocollo per la criptazione dei messaggi) è definito dagli algoritmi di criptazione e decriptazione e da una chiave. Siccome non vogliamo inventare un nuovo algoritmo ogni volta che scambiamo messaggi, l'idea è quella di avere una chiave segreta mentre l'algoritmo può essere pubblico. Siccome la chiave è l'unica componente segreta, tutto deve dipendere da lei. 

**Crittografia simmetrica:** A e B hanno la stessa chiave, oppure è facile passare da una chiave all'altra

**[[Crittografia asimmetrica]]:** ci sono due chiavi, una privata e una pubblica. Trovare la privata partendo dalla pubblica deve essere impossibile

Siccome la cifratura deve essere invertibile, la lunghezza del **plaintext** e del **cyphertext** deve essere la stessa.

**Cifrario a blocchi:** cifrario in cui il messaggio è diviso in vari blocchi su cui viene applicata una permutazione. L'algoritmo che organizza le permutazioni sui blocchi è detto **mode of operation**

**Cifrario a flusso:** cifrari dove si interpreta il plaintext come un flusso continuo di bit. Utile per esempio per le telefonate (non ne parleremo)

# Cifrario di Cesare
Consideriamo un cifrario dove andiamo a shiftare ogni lettera di un fattore k. Quindi se k = 3 allora $a\to d$, $d \to g$, e cosi via. Questi shift sono detti **rotazioni**.

Caratteristiche: 
- La chiave è proprio il fattore k
- La permutazione è uno shift
- La mode of operation è quella di applicare la stessa permutazione ad ogni lettera

La chiave di cifratura è -k (notare che le due chiavi non siano la stessa, ma sia facile passare da una all'altra).

Quello di Cesare è un cifrario **mono-alfabetico**, perché ogni lettera è sempre mappata nella stessa lettera.

# Permutazioni
Nessuna permutazione è "casuale". Data una certa permutazione questa infatti non contiene randomicità. Nonostante ciò ci conviene scegliere permutazioni che *sembrino* casuali. 

Le rotazioni sono chiaramente non casuali, infatti sapendo dove viene mappata una lettera sappiamo dove vengono mappate tutte le lettere. 

Le permutazioni dell'alfabeto inglese è $26! \simeq 1.3 \cdot 2^{88}$, impossibile da indovinare. Ciononostante non si è privi di vulnerabilità. 

**Analisi delle frequenze:** possiamo immaginare che se nel cyphertext la lettera x è la lettera più comune, probabilmente corrisponde alla lettera a (più comune nella lingua italiana). Questo rappresenta un problema perché a mappando ogni lettera cifrata ad ogni lettera in chiaro con frequenze simili si può rompere la cifratura.

# Cifrario di Vigenère
Cifrario **poli-alfabetico** (una lettera non è sempre mappata nella stessa)

La chiave è un'altra parola, che viene ripetuta per avere la stessa lunghezza del plaintext, per poi essere "sommata". Se il messaggio è algoritmi e la chiave è crypto allora otteniamo

	algoritmi +
	cryptocry =
	ccedkwvyk

Risulta vulnerabile se la chiave è molto più corta del messaggio. Immaginiamo il cyphertext "𝚏𝚡𝚋𝚡𝚣𝚔𝚝𝚠𝚛𝚍𝚟𝚏𝚌𝚜𝚏𝚡𝚋𝚡𝚚𝚠𝚙𝚕𝚋𝚗". Notiamo che la sottostringa "𝚏𝚡𝚋𝚡" è ripetuta a distanza di 14 caratteri. Potrebbe essere una coincidenza, ma potrebbe anche essere che sia perché quattro caratteri uguali di plaintext e cyphertext sono stati ripetuti. Questo vuol dire che la chiave o è lunga almeno 18 caratteri con ripetizioni, oppure è lunga un divisore di 14 (2,7,14). Abbiamo ottenuto molte informazioni sulla chiave.
# One-time pad
Consideriamo un cifrario di Vigenère in binario, dove invece della somma delle lettere effettuiamo uno XOR bit-wise:

	plaint: 0 0 0 1 0 1 1 0 1 0
	chiave: 1 0 1 1 0 0 1 0 0 1
	cypher: 1 0 1 0 0 1 0 0 1 1

Se la chiave è scelta a caso, non riutilizzata, ed è lunga come il plaintext, allora da un singolo plaintext è possibile ottenere con la stessa probabilità ogni cyphertext. Questo rappresenta il cifrario matematicamente più difficile da violare. 

Il problema è che se la chiave è usata più volte si possono ottenere informazioni su più messaggi. Consideriamo due cyphertext $C_1,C_2$ corrispondenti a plaintext $P_1,P_2$ e chiave $K$, si ha che:
$\begin{equation} C_1 + C_2 = (P_1 + K) + (P_2 + K) = (P_1 + P_2) + (K + K) = P_1 + P_2 \end{equation}$
Non so esattamente $P_1,P_2$ ma conosco il loro XOR. Già qualcosa.

# Indistinguibilità
Richiediamo che un cifrario faccia si che per un attaccante i testi cifrati siano indistinguibili da bit generati a caso. 

Consideriamo che E produca due messaggi. A ne sceglie uno a caso e lo cifra, mandando il cyphertext a E. Se E non riesce a capire quale dei suoi messaggi sia stato cifrato (almeno con probabilità $>>1/2$ ) si ha **indistinguibilità**.

Nella [[Crittografia asimmetrica]] capire quale messaggio è stato cifrato è facilissimo, visto che anche E può cifrarli. 

Nella cifratura simmetrica la si una generatore pseudocasuale crittografico, un DRBG. 

A produce $S = K || R$ dove K è la chiave e R un numero di bit casuali perché $S$ Sia lungo abbastanza da poter essere passato al DRBG. Il messaggio inviato da A è $(C,R)$ dove $C = M + DRBG(S)$.

Nella cifratura asimmetrica, K è pubblica, quindi non ha senso inserirla nel DRBG. Il messaggio inviato da A è invece $(C,E(K,R))$ dove E(\_,\_) è la funzione di cifratura.
# Non malleabilità
Si dice che un cifrario è **non malleabile** se, partendo da un cyphertext $C_1$ corrispondente al plaintext $P_1$ sia difficile produrre un $C_2$ corrispondente a $P_2$, dove quest'ultimo ha qualche relazione con $P_1$. 

Il one time pad risulta non malleabile se la chiave è riutilizzata.
# Cifrari a blocchi
Nei cifrari a blocchi il plaintext è diviso in blocchi lunghezza B, tipicamente 64 o 256. Se B non divide la lunghezza del plaintext, allora si aggiungono bit di **padding**.

La scelta di B è cruciale, e ci sono forze che spingono in direzioni opposte. Vogliamo un B piccolo perché ci sia poco padding nei messaggi corti e per l'efficienza hardware.

D'altro canto un B troppo piccolo risulta poco sicuro. Infatti ad un attaccante basterebbe fare un **codebook attack**, dove si compila una tabella di $2^B$ celle dove alla posizione $i$ è salvato il plaintext avente $i$ come cyphertext. Gli basterebbe poi un semplice **table lookup** per decifrare il messaggio. 

# Attack model
Per misurare la sicurezza di un cifrario ci poniamo nelle scarpe di un attaccante. Il modello che ci creiamo, incluse tutte le assunzioni su cosa abbiamo a disposizione, è detto **attack model**.

Ci sono modelli d'attacco che fanno uso di informazioni secondarie, tipo il tempo di esecuzione o fenomeni fisici. Questi sono detti attacchi **side channel** e non ne parleremo.

- **Cyphertext only attack (COA):** si assume che si conosca solo il cyphertext
- **Known plaintext attack (KPA):** si assume di aver visto alcune coppie plain-cyper text. 
- **Chosen plaintext attack (CPA):** l'attaccante può criptare un plaintext scelto da lui. Nella [[Crittografia asimmetrica]] questo è la norma.
- **Chosen cyphertext attack (CCA):** l'attaccante può decriptare un cyphertext arbitrario.

### Permutazioni utilizzabili
Siccome ci deve una corrispondenza 1-1 tra chiave (lunga k bit) e permutazione scelta, il numero massimo di permutazioni usabili è $2^K$. Il numero di permutazioni possibili su un blocco di B bit è $2^B!$, che cresce molto più velocemente. Le permutazioni utilizzabili sono quindi $\frac{2^K}{2^B!}$. 

Con una chiave realistica di 128 bit, possiamo usare solo $B \leq 5$ oppure la frazione di permutazioni possibili diventa troppo piccola.

Tra queste, ce ne sono comunque molte che non possiamo usare. Alcune non cambiano mai un bit, altre sono rotazioni che abbiamo visto essere troppo facili da rompere. Tutte queste rientrano nel campo delle **trasformazioni lineari affini invertibili** su $Z_2$: 

$xP+b = y(x)$. 

Dove x è il plaintext, y il cyphertext, P una matrice non singolare e b un vettore. 

In un modello CPA risulta molto facile ricavare P e b, infatti: 
$y(0) = 0P+b = b$. Noto b posso trovare P cifrando i vettori canonici $(1,0,...,0), (0,1,0,...,0), ... , (0,...,0,1)$. 

Altre proprietà che richiediamo alle permutazioni:
- **diffusione:** cambiare anche un solo bit del plaintext deve cambiare tutto il cyphertext
- **confusione:** ogni pattern nel plaintext non deve essere ripetuto nel cyphertext
# Feistel Networks
In realtà è uno schema per generare cifrari, dipendente da una funzione. Ad ogni stadio viene usata una chiave $k_i$ che dipende dalla chiave generale secondo una **key schedule**.

All'inizio il blocco viene diviso a metà in $L_0, R_0$. Si segue poi questo algoritmo:
	$L_{i+1} = R_{i}$
	$R_{i+1} = L_i + F(R_i, k_{i+1})$
Dove F è una funzione di cifratura. 

Notare che per come è costruito l'algoritmo, per decifrare basta inserire ripetere l'algoritmo invertendo l'ordine di $k_i$. 
## Digital Encryption Standard
La DES è una particolare implementazione di un Feistel Network dove la F funziona così: 
1. I 32 bit di $R_i$ vengono espansi 48 duplicandone alcuni
2. Il risultato è messo in XOR con $k_i$ (appunto lunga 48 bit)
3. Tramite 8 tabelle con celle a 4 bit indicizzate a 6 bit, il risultato è riportato a 32 bit. Questo processo è detto **S-Box**.
4. Il risultato è permutato e posto in XOR con $L_i$

Come chiavi si parte da una generale di 56 bit, divisa in due metà da 28. Ad ogni round vengono ruotate a sinistra di 1 o 2 bit, $k_i$ è formata prendendo 24 bit da ciascuna.

### Electronic Codebook Mode
La ECB è una mode of operation per il DES dove ogni blocco viene cifrato con la stessa chiave: $C_i = E(P_i, K)$

E' molto semplice, ma risulta chiaro che a parti di plaintext uguali risultano cyphertext uguali, situazione particolarmente problematica nelle immagini.
### Cipher Block Chaining
Per risolvere il problema dell'ECM, si cifra in questo modo:
	$C_1 = E(P_1 + IV, K) \quad C_{i+1} = E(P_{i+1} + C{i}, K)$
Dove IV è un **initialization vector**, di solito un zeros. Per via delle proprietà dello XOR per decifrare basta fare:
	$P_1 = D(C_1, K) + IV \quad P_{i+1} = D(C_{i+1}, K) + C_i$



