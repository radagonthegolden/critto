L'RSA è stato il primo protocollo asimmetrico ad essere fattibile in pratica. Prende il nome dei suoi inventori e si basa sulla difficoltà (sempre solo teorica) della fattorizzazione di numeri interi.

Ricordiamo che un eventuale **computer quantistico** sarebbe in grado di fattorizzare numeri interi e violarlo.

A generare le chiavi è il destinatario del messaggio, diciamo sia A.

- A genera due numeri primi a caso $p,q$ di lunghezza $N/2$ bit (N è l'unico parametro del protocollo)
- Calcola $n = p \cdot q$
- Calcola $\phi(n) = (p-1)\cdot (q-1)$
- Sceglie a caso un intero $e$ coprimo con $\phi(n)$
- Siccome $e$ è coprimo con $\phi(n)$ esiste $d = e^{-1} \mod \phi(n)$ 
- Diffonde $(e,n)$ come **chiave pubblica** e tiene $d$ come **chiave privata**

B, che legge e conosce $(e,n)$ cifra il suo messaggio in chiaro $M$ producendo $C = M^e \mod n$. 

A, per decifrare $C$ semplicemente fa $C^d \mod n= M$. Dimostriamo questa uguaglianza.

>Siccome $d = e^{-1} \mod \phi(n) \implies ed = 1 \mod \phi(n)$ vale $ed = k\cdot \phi(n)+1$, per qualche intero $k$

Quindi 
$$\begin{align}
C^d \mod n &= (M^e \mod n)^d \mod n \\
&= M^{ed} \mod n\\
&= M^{k\cdot \phi(n)+1} \mod n \\
&= M \cdot M^{ k\cdot \phi(n)} \mod n \\
&= M \cdot M^{k(p-1)(q-1)} \mod n
\end{align}$$
Rimane da dimostrare $M \cdot M^{k(p-1)(q-1)} \mod n = M \mod n$, per farlo partiamo col dimostrare che $M \cdot M^{k(p-1)(q-1)} \mod p = M \mod p$.

Se $M \mod p = 0$ allora per forza vale l'uguaglianza sopra perché sono entrambi 0. Altrimenti:
$$\begin{align}
\left( M \cdot M^{k(p-1)(q-1)} \right) \mod p &= \left( M \cdot M^{(p-1)^{k(q-1)}} \right) \mod p \\
&= \left( M \cdot \left(M^{p-1}\mod p\right)^{k(q-1)} \right) \mod p \\
&= \left( M \cdot 1^{k(q-1)} \right) \mod p \\
&= M \mod p
\end{align}$$
Allo stesso modo vale $M \cdot M^{k(p-1)(q-1)} \mod q = M \mod q$. Siccome valgono $C^d \mod p = M \mod p, C^d \mod q = M \mod q$ (e $p,q$ sono coprimi) per il [[prereq_1#Teorema cinese dei resti | toerema cinese dei resti]], vale $C^d \mod n = M \mod n$. 

**Efficienza:** 
- L'RSA richiede solo due primi, neanche che siano safe-prima
- Si calcolano $e,d$ una sola volta
- Cifrare e decifrare si fa tramite [[prereq_1#L'Esponenziale modulare|modexp]]

Ci sono vari modi per trovare $e$ tale che sia coprimo con $\phi(n)$:
- Prendere $e>\max(p,q)$, infatti $\phi(n) = (p-1)(q-1)$, quindi i suoi fattori primi sono entrambi minori di $p,q$. 
- Scegliere un intero a caso e controllare l'MCD che è efficiente. 
- Fissare $e$ in partenza piccolo (3 o 5) e generare $p,q$ finché $MCD(e,\phi(n)) \neq 1$. Utile perché significa che i due modexp saranno veloci da calcolare. Nella pratica si fa questo ma con $e$ più grandi

La **sicurezza** dell'RSA si basa sul fatto che per trovare $d$ bisogna sapere $\phi(n)$, che significa sapere $p$ e $q$. Questo è infattibile dato che si tratta di fattorizzare $n$. 

# Vulnerabilità
## [[Crittografia simmetrica#Non malleabilità|Malleabilità]]
L'RSA è non malleabile e risulta vulnerabili ad attacchi [[Crittografia simmetrica#Attack model|Chosen cyphertext attack]], ovvero sempre visto che la chiave di cifratura è pubblica. Supponiamo di avere $C_1 = M_1^e \mod n$.

Ora scegliamo $C_I = 2^e \mod n$, da cui deriviamo $C_2 = C_I \cdot C_1$. 

Se per qualsiasi ragione riusciamo a decifrare $C_2$ ottenendo $M_2$ allora abbiamo anche ottenuto $M_1$, infatti:
$$\begin{align}
M_2 = C_2^d \mod n &= (C_I\cdot C_1)^d \mod n \\
&= ((M_1^e \mod n)\cdot (2^e \mod n)^d) \mod n \\
&= ((M_1^e)^d \cdot (2^e)^d) \mod n \\
&= (M_1 \cdot 2) \mod n
\end{align}$$
Quini basterebbe uno shift per passare da $M_2$ a $M_1$.

## $e$ troppo piccolo
Se $e$ è molto piccolo e i messaggi sono anche loro piccoli può capitare che il modulo a $n$ sia inutile.

Per esempio, $e = 3, M = 5, n = 500$, abbiamo:
	$C = M^e \mod n = 5^3 \mod 500 = 125 \mod 500 = 125$
Quindi $C = M^e$ senza modulo, questo risulta un problema perché fare la radice $e$-sima è molto facile rispetto al [[prereq_1#Logaritmo discreto|logexp]].

## Fattorizzazione di $n$
### Primi troppo vicini
Un possibile attacco deriva da una proprietà ben conosciuta:

>il prodotto di due numeri dispari può sempre essere espresso come differenza di quadrati

Quindi (siccome $n = p\cdot q$ e $p,q$ sono primi quindi dispari) vale:
	$n = \left( \frac{p+q}{2} \right)^2 + \left( \frac{p-q}{2} \right)^2 = \frac{1}{4}((p+q)^2-(p-q)^2)$

Quindi se riuscissimo a scrivere $n = x^2-y^2$, quindi $n = x^2-y^2 = (x+y)(x-y)$, necessariamente avremmo $p = x+y, q = x-y$ (la scomposizione in primi è univoca).

L'algoritmo di questo attacco consiste nel partire da $x_0$, calcolare $z_0 = x_0^2 -n$ e controllare se $z_0$ è un quadrato perfetto, caso nel quale abbiamo fattorizzato $n$. Sennò facciamo $x_i = x_0 + i$, controlliamo $z_i = x_i^2-n$ e andiamo avanti. Con che valore di $x_0$ partire però?

E' chiaro che se partiamo con $x_0 > \frac{p+q}{2}$ abbiamo $z_0 = x_0^2-n > \left( \frac{p+q}{2} \right)^2-n = \left( \frac{p-q}{2} \right)^2$, ma siccome $z_i$ aumenta ad ogni passo questa rimarrà una disuguaglianza per sempre. Ci serve un lower bound per $\frac{p+q}{2}$ (che chiaramente non conosciamo). 

Ma visto che $n = p\cdot q$ (con $p\neq q$), per forza $q < \sqrt{n} < p$, quindi $\frac{p+q}{2} > \sqrt{n}$. Partiamo proprio con $x_0 = \sqrt{n}$ (o meglio, parte intera superiore di $\sqrt{n}$).

Ha senso però fare tutti questi giri? Che costo computazionale ha?

Supponiamo esista $c$ costante tale che $\frac{p+q}{2} < c \cdot \sqrt{2\cdot q}$  (vedremo in seguito perché). 

L'algoritmo parte facendo: 
	$\left( \frac{p+q}{2} \right)^2-n = \left( \frac{p-q}{2} \right)^2$
Che possiamo trasformare in:
	$\left( \frac{p+q}{2} + \sqrt{n} \right)\cdot \left( \frac{p+q}{2} - \sqrt{n} \right) = \left( \frac{p-q}{2} \right)^2$
Possiamo minorare con $2q$ il primo fattore a sinistra visto che $q < \sqrt{n}$:
	$2q\cdot \left( \frac{p+q}{2} - \sqrt{n} \right) < \left( \frac{p-q}{2} \right)^2$
Per ipotesi possiamo invece minorare il membro di destra con $2qc^2$:
	$2q\cdot \left( \frac{p+q}{2} - \sqrt{n} \right) < 2qc^2$
Con dei calcoli otteniamo:
	$\frac{p+q}{2}-\sqrt{n} < \frac{2qc^2}{2q} = c^2$

Ma la differenza $\frac{p+q}{2}-\sqrt{n}$ è proprio il numero di passi che il nostro algoritmo deve fare per trovare un altro quadrato perfetto. In parole povere ci mettiamo al massimo $c^2$ passi per rompere l'RSA. 

Siamo però partiti dall'assunzione che $\frac{p+q}{2} < c \cdot \sqrt{2\cdot q}$. Considerando che $q = O(\sqrt{n})$, la nostra ipotesi si traduce in **la differenza tra $p$ e $q$ è un $O(^4\sqrt{n})$**. In conclusione se $p,q$ coincidono nella meta di bit più significativa (la sinistra), allora questo attacco può avere senso.

### Generatori casuali
Uno studio ha visto che i $p,q$ generati "casualmente" non sempre così casuali, e il ripetersi di uno dei due non è del tutto infrequente.

Ciò è un enorme problema, infatti se $p$ viene riutilizzato, ovvero $n_1 = p\cdot q_1, n_2 = p\cdot q_2$, allora per forza $MCD(n_1,n_2) = p$.

La causa di tutto ciò è la relativa "non casualità" dei generatori casuali.
# Aspetti implementativi
## Efficienza
Il primo trucco per rendere l'RSA più efficiente è scegliere $e = 65537 = 2^{16}+1$, per vari motivi:
- E' primo, quindi per forza coprimo con $\phi(n)$, non serve cercare altri $e$
- E' molto grande, evitando attacchi dovuti a $e$ piccoli
- Essendo una potenza di 2 più 1, in binario è $10000000000000001$. Tutti quegli zeri rendono i calcoli molto veloci

Il secondo accorgimento entra in gioco in fase di decifratura. Per il [[prereq_1#Teorema cinese dei resti|teorema cinese dei resti]], avendo:
	$M_p = C^d \mod p, \quad M_q = C^d \mod q$ 
Possiamo scrivere $M$ come:
	$M = ((q\cdot (q^{-1} \mod p)) M_p +(p\cdot (p^{-1} \mod q)) M_q) \mod n$
Notiamo che i coefficienti $q\cdot (q^{-1} \mod p), p\cdot (p^{-1} \mod q)$ possono essere pre-calcolati. 

$M_p, M_q$ non li calcoliamo nel modo più ovvio però (l'espressione che li descrive), invece partiamo da:
	$s = d \mod (p-1), \quad t = d \mod (q-1)$, anche esse pre-calcolabili
Per definizione $d = s+k(p-1)$ per un certo $k$, quindi:
	$C^d \mod p = C^{s+k(p-1)} \mod p = C^s\cdot C^{k(p-1)} \mod p = C^s\cdot 1^k\mod p = C^s \mod p$
Analogamente $C^d \mod q = C^t \mod q$. 

Quindi invece di calcolare $C$ modexp a $d$, lo calcoliamo a $s,t$. Sono due modexp rispetto ad uno, ma con lunghezza dell'esponente circa dimezzata, rendendo molto più veloci i calcoli.
# Optimal Asymmetric Encryption Padding (OAEP)
Nel concreto l'RSA (ma anche tutti gli algoritmi di cifratura) richiedono un [[Crittografia simmetrica#Cifrari a blocchi|padding]] composto da sequenze casuali. Ci servono inoltre due [[Funzioni hash|funzioni hash]] $G,H$. 

Se $N$ è la lunghezza in bit del modulo $n$, allora i messaggi possono essere di lunghezza massima $t = N-h-k-2$, dove $h,k$ sono parametri del protocollo. Si tratta comunque di spazio ben sufficiente. 

Lo schema è il seguente:
![[OAEP.jpg]]
Dove $x$ è poi inviato come input all'RSA.

1. Il ricevente decifra l'RSA e ritrova $x$, da cui si estraggono i primi $t$ bit, ovvero $m_1$, e gli ultimi $k$ bit, ovvero $r_1$. 
2. Si applica $H$ a $m_1$. In cifratura avevamo $r_1 = H(m_1)+r$, per le proprietà dello XOR in bit possiamo ottenere $r = r_1 + H(m_1)$.
3. Allo stesso modo siccome $m_1 = m+G(r)$ possiamo ottenere $m = m_1 + G(r)$
4. Gli ultimi $h$ bit di $m$ sono di controllo, devono essere tutti 0 oppure il messaggio è scartato.

