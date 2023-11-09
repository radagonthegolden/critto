Si tratta di algoritmi "competitor" all'RSA che non hanno fatto successo.

E' dimostrabilmente equivalente alla fattorizzazione, ovvero un algoritmo che rompe la fattorizzazione rompe REG e viceversa. 

Ha un difetto: ad ogni cyphertext corrispondono 4 plaintext equivalenti.

### Radici quadrate
Vogliamo calcolare le radici quadrate di $y$ in $Z_n$ dove $n = p\cdot q$, per farlo facciamo il contrario: partiamo da $x$ e lo eleviamo al quadrato $y = x^2$

Notiamo che anche $-x$ e' radice di $y$, ma c'e' ne sono altre 2, dovute al fatto che $n$ e' il prodotto di 2 primi.

Notiamo anche $y\mod p$ e $y \mod q$ sono [[prereq_2#Residui quadratici e test di Solovay-Strassen|residui quadratici]]. Infatti $x\mod p$ e' radice di $y\mod p$:

$((x\mod p)\cdot (x\mod p))\mod p = (x\cdot x) \mod p = ((x\cdot x)\mod n) \mod p = y \mod p$
Analogamente $y\mod q$ ha radice $y\mod q$. 

Naturalmente notiamo come le radici siano a due a due congrue modulo $p$ e modulo $q$. 
# L'Algoritmo
Richiede un solo parametro: $N$, la lunghezza in bit del modulo. Viene richiesto che $M$ sia un numero minore del modulo, o trasformato in tale da funzioni invertibili.

A genera due primi a caso: $p,q = 3 \mod 4$. Notare che il fatto che $p,q = 4k+3$ non e' necessario ma aiuta i calcoli.

A diffonde $n = p\cdot q$ come chiave pubblica e tiene $p,q$ come chiave privata.

B cifra il suo messaggio $M$ facendo $C = M^2 \mod n$, per poi inviare $C$.

>Ricordiamo che se $p$ divide $n$ allora per ogni $x$ vale $(x\mod n) \mod p = x\mod p$

Per decifrare A calcola $M_p = C^{(p+1)/4} \mod p$ e $M_q = C^{(q+1)/4} \mod q$. Notare che siccome $p,q = 4k+3$ gli esponenti $(p,q +1)/4$ sono interi.

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
