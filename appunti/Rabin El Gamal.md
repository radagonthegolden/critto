Si tratta di algoritmi "competitor" all'RSA che non hanno fatto successo.

E' dimostrabilmente equivalente alla fattorizzazione, ovvero un algoritmo che rompe la fattorizzazione rompe REG e viceversa. 

Ha un difetto: ad ogni cyphertext corrispondono 4 plaintext equivalenti.

### Radici quadrate
Vogliamo calcolare le radici quadrate di $y$ in $Z_n$ dove $n = p\cdot q$, per farlo facciamo il contrario: partiamo da $x$ e lo eleviamo al quadrato $y = x^2$

Notiamo che anche $-x$ e' radice di $y$, ma c'e' ne sono altre 2, dovute al fatto che $n$ e' il prodotto di 2 primi.

Notiamo anche $y\mod p$ e $y \mod q$ sono [[prereq_2#Residui quadratici e test di Solovay-Strassen|residui quadratici]]. Infatti $x\mod p$ e' radice di $y\mod p$:

$((x\mod p)\cdot (x\mod p))\mod p = (x\cdot x) \mod p = ((x\cdot x)\mod n) \mod p = y \mod p$

Analogamente $y\mod q$ ha radice $y\mod q$. 

Naturalmente le radici siano a due a due congrue modulo $p$ e modulo $q$. 
# Il sistema crittografico di Rabin
Richiede un solo parametro: $N$, la lunghezza in bit del modulo. Viene richiesto che $M$ sia un numero minore del modulo, o trasformato in tale da funzioni invertibili.

A genera due primi a caso: $p,q = 3 \mod 4$. Notare che il fatto che $p,q = 4k+3$ non e' necessario ma aiuta i calcoli.

A diffonde $n = p\cdot q$ come chiave pubblica e tiene $p,q$ come chiave privata.

B cifra il suo messaggio $M$ facendo $C = M^2 \mod n$, per poi inviare $C$.

>Ricordiamo che se $p$ divide $n$ allora per ogni $x$ vale $(x\mod n) \mod p = x\mod p$

Per decifrare A calcola $M_p = C^{(p+1)/4} \mod p$ e $M_q = C^{(q+1)/4} \mod q$. Questa è la parte "segreta" della decifratura, in quanto usa $p,q$ che nessuno a parte A conosce. Notare che siccome $p,q = 4k+3$ gli esponenti $(p,q +1)/4$ sono interi.

$M_p$ e' una delle radici quadrate di $C$ modulo $p$, infatti:
$$\begin{align}
M_p^2 \mod p &= \left( C^{(p+1)/4} \mod p \right)^2 \mod p \\
&= C^{(p+1)/2} \mod p \\
&= \left( C^{(p-1)/2} \cdot C\right) \mod p \\
&= \left( \left(M^2 \mod n\right)^{(p-1)/2} \cdot C\right) \mod p \\
&= \left( \left((M^2 \mod n) \mod p \right)^{(p-1)/2} \cdot C\right) \mod p \\
&= \left( \left(M^2 \mod p\right)^{(p-1)/2} \cdot C\right) \mod p \\
&= \left( M^{p-1} \cdot C\right) \mod p \\
&= \left( (M^{p-1}\mod p) \cdot C\right) \mod p \\
&= (1\cdot C) \mod p \\
&= C \mod p
\end{align}$$

Analogamente $M_q$ e' radice quadrata modulo $q$ di $C$. 

Calcoliamo i valori:
- $c_p = q\cdot (q^{-1} \mod p)$
- $c_q = p\cdot (p^{-1} \mod q)$
Con i quali possiamo calcolare i quattro valori delle radici modulo $n$ di $C$:
- $M_1 = (c_pM_p+c_qM_q) \mod n$
- $M_2 = (-c_pM_p-c_qM_q) \mod n$
- $M_3 = (c_pM_p-c_qM_q) \mod n$
- $M_4 = (-c_pM_p+c_qM_q) \mod n$

Infatti:
$$\begin{align}
M_1^2 \mod p &= ((c_pM_p+c_qM_q) \mod n)^2 \mod p \\
&= ((c_pM_p+c_qM_q) \mod p)^2 \mod p \\
&= (M_p \mod p)^2 \mod p \\
&= M_p^2 \mod p \\
&= C \mod p
\end{align}$$
Analogamente $M_1^2 \mod q = C \mod q$

Quindi il [[prereq_1#Teorema cinese dei resti|teorema cinese dei resti]] ci assicura che $M_1^2 \mod n = C \mod n$. 

Analogamente si trova che $M_2,M_3,M_4$ sono tutte radici di $C$ modulo $n$.
## Equivalenza con la fattorizzazione
E' chiaro che se sapessi fattorizzare $n$ in $p,q$ saprei decifrare senza problemi. Per dimostrare l'equivalenza dobbiamo far vedere il viceversa: che se sappiamo decifrare REG (con probabilità 1 su 4) allora sappiamo anche fattorizzare $n$.

Ovvero l'obbiettivo è quello di fattorizzare un numero $n$ qualsiasi avendo a disposizione un algoritmo **black-box** che dato $C \mod n$ restituisca una delle quattro radici. I passi sono i seguenti:
1. Genero a caso $r \in Z_n, r \neq 0$
2. Calcolo $m = MCD(r,n)$, se $n \neq 1$ ho avuto fortuna e $m, n/m$ sono i fattori di $n$. Altrimenti vado avanti
3. Considero $r^2 \mod n= C$ e uso l'algoritmo black-box per ottenere $r'$ (una delle radici)
4. Calcolo $m = MCD(r-r',n)$, se $m > 1, m \neq n$ allora ho trovato i fattori $m, n/m$. Altrimenti ritorno al passo 1

Si tratta di un algoritmo [[prereq_2#Algoritmi Las Vegas|Las Vegas]], visto che di sicuro fattorizza $n$, il tempo dipende dalla probabilità (comunque strettamente positiva) di trovare un fattore di $n$ al passo 4. Sappiamo infatti che $r'$ è una delle quattro radici di $r^2$, ognuna delle quali corrisponde alle combinazioni delle due radici di $r^2 \mod p$ e $r^2 \mod q$:
- $r_p = r \mod p$
- $-r_p = p-r_p = p-r \mod p$
- $r_q = r \mod q$
- $-r_q = q-r_q = q-r \mod q$

Supponiamo di partire da $r$ corrispondente alla coppia $(r_p,r_q)$ e consideriamo le 4 possibili risposte dell'algoritmo black-box:
1. $r' = r$, ovvero anche $r'$ corrisponde alla coppia $(r_p,r_q)$. In tal caso $r-r' = 0$, dunque al passo 4 trovo $m = n$ e si torna al passo 1.
2. $r'$ corrisponde alla coppia $(p-r_p,q-r_q)$, caso in cui abbiamo:
	   $(r-r') \mod p = r_p - (p-r_p) \mod p = 2r_p \mod p$
	   Analogamente: $(r-r') \mod q = 2r_q \mod q$
	   Per il [[prereq_1#Teorema cinese dei resti|teorema cinese dei resti]] risulta che $(r-r') \mod n = 2r \mod n$. Questo ci dà 2 possibilità: o $r$ ha un fattore in comune con $n$ (impossibile perché ci saremmo fermati al passo 2) oppure $MCD(r-r',n) = 1$.
	   Anche in questo caso l'algoritmo non dà risposta.
3. $r'$ corrisponde alla coppia $(r_p, q-r_q)$, caso in cui $(r-r') \mod q = r_q - (q-r_q) \mod q = 2r_q \mod q$
	   Questa volta però $(r-r') \mod p = 0$, per cui l'$MCD(r-r',n) = p$ e allora $p$ deve essere uno dei fattori di $n$, l'altro sarà $n/p$.
4. Il caso in cui $r'$ corrisponde a $(p-r_p,r_q)$ è speculare all'altro. Anche in questo caso abbiamo successo

Siccome due delle quattro possibili radici di $r$ ci dannò i fattori di $n$, l'algoritmo ha una probabilità $1/2$ di avere successo ogni round. In realtà un po' sopra 50% perché al passo 2 potremmo trovare già i fattori.
# Il Sistema crittografico El-Gamal
Non è stato il primo, prodotto qualche anno dopo l'RSA. Siccome non è stato brevettato è stato usato da alcune suite crittografiche open come GNUPG.

A genera:
- Un numero primo $p$ di lunghezza adeguata
- Una radice primitiva $g$ di $Z_p^*$
- Un numero a caso $a \in Z_p^*$ per poi calcolare $A = g^a \mod p$
Conserva poi $a$ come chiave segreta e diffonde $(p,g,A)$ come chiave pubblica

Per ora l'unica differenza con [[Diffie-Hellman]] è che in DH la chiave viene comunicata solo a B.

B, munito di $(p,g,A)$ compie i seguenti passi:
- Sceglie un numero $b \in Z_p^*$ uniformemente a caso
- Calcola $B = g^b \mod p$ e poi $c = (A^b \cdot M) \mod p$
- Invia ad A la coppia $C = (B,c)$ che costituisce il cyphertext

A, ricevuto $C$, calcola:
- $Z = B^a \mod p$ 
- Trova poi $Z^{-1}\mod p$ usando Euclide esteso
- Decifra infine il messaggio facendo $M = (Z^{-1} \cdot c) \mod p$
Da notare che il messaggio deve essere un numero minore di $p$, visto che lo otteniamo con un modulo $p$.

Come verifica notiamo che $Z = g^{ab} \mod p$, quindi $Z^{-1} = g^{-ab} \mod p$. Di conseguenza:
	$Z^{-1}\cdot c \mod p = g^{-ab}\cdot A^b\cdot M \mod p = g^{-ab}\cdot g^{ab}\cdot M \mod p = M \mod p$

Le operazioni da eseguire per cifrare e decifrare non sono lente, ma siccome $M$ deve avere lunghezza $<p$, il messaggio rischia di dover essere diviso in blocchi lunghi $p$. Per messaggi molto grossi questo diventa inefficiente rispetto agli algoritmi di crittografia moderna.
## Sicurezza
Violare il protocollo El-Gamal è **equivalente** a risolvere il **CDH problem**, ovvero finche vale l'ipotesi CDH il protocollo è sicuro. Si tratta dello stesso tipo di sicurezza dato da [[Diffie-Hellman]].

Da notare che cambiare il valore di $b$ ogni volta è importante, se infatti B cifra due messaggi con lo stesso $b$, allora anche $B$ è lo stesso. Si ha
	$C_1 = (B, c_1 = (A^b\cdot M_1)\mod p), \quad C_2 = (B, c_2 = (A^b\cdot M_2)\mod p)$
Se moltiplico $c_2$ per $c_1^{-1}$ vedo che:
	$c_1^{-1} \cdot c_2 \mod p = (M_1^{-1}\cdot M_2) \mod p \implies M_2 = (M_1 \cdot (c_2\cdot c_1^{-1})) \mod p$
Se quindi si riuscisse, per qualsiasi motivo, a decifrare $M_1$ si potrebbe decifrare tutti i messaggi successivi cifrati con lo stesso $b$.


