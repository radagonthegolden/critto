# Funzioni one-way
Per la crittografia asimmetrica sono essenziali le funzioni **one way**: facili da calcolare da impossibili da invertire.

"Facili" nel senso che la funzione ha un algoritmo con costo polinomiale rispetto all'input. "Impossibili da invertire" nel senso che NON esistono algoritmi polinomiali per trovare una retro-immagine.

In matematica ogni problema per cui la soluzione ha un costo non polinomiale e' detto **NP-completo**, tra cui l'inversione delle funzioni one-way.

Nessuna funzione e' dimostrata NP-completa, visto che ciò' dimostrerebbe la congettura P vs NP.

Una funzione che e' facile da calcolare e facile da invertire ma solo se si possiede un'informazione segreta e' invece detta **funzione trapdoor**.
# Aritmetica modulare
$Z_n$ e' definito come tutti i possibili resti della divisione per n, quindi $Z_4 = \{0,1,2,3\}$. 

Notare che i numeri negativi sono ben definiti, infatti $-8 \mod 3 = 1$ visto che $-8 = 3 \cdot -3 +1$.

In particolare siccome $-1 = n \cdot -1 + n -1$ allora $-1 \mod n = n-1$ sempre.

L'inverso moltiplicativo non e' definito in $Z_n$ per 0, chiaramente. Pero' ci sono anche altri numeri che non sono inclusi. Infatti l'inverso di x esiste solo se e' coprimo con n. Chiaramente se n e' primo, allora tutti gli elementi di $Z_n$ a parte 0 hanno l'inverso.

Possiamo raccogliere le operazioni modulari alla fine. Per esempio $3 \mod 5 + 7 \mod 5 = 3+7 \mod 5$
Questo può facilitare alcune operazioni: $2^{100} \mod 29 = (2^5)^{20} \mod 29 = (2^5 \mod 29) \mod 29 = 3^{20} \mod 29$.

Proprietà: se m divide n allora per ogni x vale $(x\mod n) \mod m = x\mod m$.

## Algoritmo di Euclide
Serve per calcolare l'MCD tra due numeri. 

Si basa sulla seguente uguaglianza $MCD(x,y) = MCD(y, x \mod y)$, se $y \neq 0$. Se definiamo $MCD(x,0) = x$ allora possiamo ricorsivamente applicare il modulo finché' $y=0$, a tal punto l'altro numero e' l'MCD. 

Si può modificare l'algoritmo (detto algoritmo di Euclide esteso) per trovare due valori che soddisfino l'identità di Bezout:
$MCD(x,y) = a\cdot x + b\cdot y$

Se $y=0$ allora $MCD(x,0) = 1\cdot x+0\cdot 0$

Unito al passo induttivo dove EEuclid(x,y) =  $m,b,a-b\cdot \frac{x}{y}$ (arrotondata per difetto). 

Arrivati ad avere $m,a,b$ = EEuclid(x,n) posso usarli per calcolare $x^{-1} \mod n$. Se $m>1$ allora non c'e' speranza perché' x non e' coprimo con n. 

Se invece $m=1$ allora $1 = a\cdot x+b\cdot n$ quindi
$a\cdot x = 1-b\cdot n$, facendo mod n: $a\cdot x \mod n = 1$ quindi l'inverso moltiplicativo mod n di x e' proprio a. 

# Teorema cinese dei resti
Sia n un numero composto dal prodotto di $r>1$ numeri primi tra loro: $n = n_1 \cdot n_2 \cdot ... \cdot n_r$, allora il resto della divisione $n/a$ per qualsiasi a e' definita completamente dalle divisioni $n_1/a, n_2/a, ..., n_r/a$. 

In termini di algebra esiste una corrispondenza 1-1 tra $Z_n$ e $Z_{n_1} \times Z_{n_2} \times ... \times Z_{n_r}$ (prodotto cartesiano)

**Dim:** Per dimostrarlo dobbiamo trovare il modo di passare da $Z_n$ a $Z_{n_1} \times Z_{n_2} \times ... \times Z_{n_r}$ e viceversa. 

Preso $a \in Z_n$ allora la r-upla corrispondente in $Z_{n_1} \times Z_{n_2} \times ... \times Z_{n_r}$ e' $(a \mod n_1,a \mod n_2,...,a \mod n_r)$.

Viceversa, se partiamo dalla r-upla $(a_1,a_2,..., a_r)$ allora cerchiamo dei coefficienti $c_i$ che potranno essere 0 o 1, che producano $C = c_1a_1 + ... + c_ra_r$ tale che valga $C \mod n_i = a_i \quad \forall i$ . Il perche' risulta chiaro dopo.

Costruiamo $mi = \prod_{j\neq i} n_j$, ovvero il prodotto di tutti gli $n_j$ a parte proprio l'i-esimo. Siccome $m_i$ sara' coprimo con $n_i$ esiste $m_i^{-1} \mod n_i$.

Poniamo $c_i = m_i (m_i^{-1} \mod n_i)$.

Per $i \neq j$ abbiamo $c_i = mod n_j = 0$ siccome $c_i$ e' multiplo di $m_i$ che a sua volta e' multiplo di $n_j$. 

Nel caso $j=i$ abbiamo $c_i \mod n_i = (m_i (m_i^{-1} \mod n_i)) \mod n_i = (m_i (m_i^{-1}) \mod n_i = 1 \mod n_i = 1$.

Risulta chiaro che con $C = c_1a_1 + ... + c_ra_r$ si ha $C \mod n_i = a_i \quad \forall i$.

Quindi $C$ ha gli stessi resti di $a$ per ogni divisione in $Z_{n_1} \times Z_{n_2} \times ... \times Z_{n_r}$, ma siccome la cardinalita di questo spazio e' la stessa di $Z_n$ allora deve essere $C=a$, abbiamo trovato l'equivalente in $Z_n$ della r-upla che descriveva in $Z_{n_1} \times Z_{n_2} \times ... \times Z_{n_r}$.
# Gruppi 
Siccome la somma modulare in $Z_n$ e' **chiusa, associativa, esiste il neutro e l'inverso** possiamo dire che $Z_n$ e' un **gruppo** rispetto all'addizione. 

Non e' vero per la moltiplicazione perche' solo gli elementi coprimi con $n$ hanno inverso. Indichiamo con $Z_n^*$ il gruppo che contiene solo loro. Questo e' chiaramente un gruppo rispetto alla moltiplicazione.

Siccome $Z_p$ e' un gruppo additivo abeliano, e $Z_p^*$ e' gruppo abeliano moltiplicativo (con $p$ primo), $Z_n$ e' un campo. 

L'ordine di un generico $x \in Z_n$ e' il numero di che posso aggiungere/sommare $x$  a se stesso prima di ottenere di $0$ o $1$.  Per esempio l'ordine di 2 in $Z_{6}$ per l'addizione e' 3: $2+2+2 \mod 6= 6 \mod 6 = 0 \mod 6$.

L'ordine di un gruppo e' invece il numero dei suoi elementi. Se un gruppo $G$ ha un elemento $g$ che ha lo stesso ordine di $G$ stesso, si dice gruppo **ciclico**, $g$ e' detto **generatore** o **radice primitiva**.

I gruppo $Z_n$ additivi sono chiaramente sempre ciclici, per la moltiplicazione sono ciclici solo $Z_p^*$. 

Preso un elemento non generatore di $Z_p^*$, quindi con ordine $s$ inferiore a $p-1$ ($Z_p^*$ ha ordine $p-1$ perche' non ha lo zero) consideriamo: $H = \{h^i \mod p\}$, ovvero il gruppo generato da $h$.

$H$ e' sottogruppo di $Z_p^*$, ovvero e' sottoinsieme ed e' anche un groppo. E' chiaramente un sottoinsieme, ed e' un gruppo perche' include $1$ ed esiste l'inverso. Infatti preso $x=h^i \in H$ il suo inverso e' $x^{-1} = h^{s-i}$ perche' $x\cdot x^{-1} \mod p = x^i \cdot x^{s-i} \mod p = x^s \mod p = 1$.

**Teorema di Lagrange:** qualsiasi sottogruppo di un gruppo di rodine $k$ ha come ordine un divisore di $k$.

**Teorema fondamentale dei gruppi ciclici:** se il gruppo ciclico $G$ ha ordine $k$, esiste un sottogruppo per ogni divisore di $k$ avente quel divisore come ordine.
# Safe Prime
**Safe prime:** un numero primo $p$ scrivibile come $p = 2q+1$ con anche $q$ primo.

Se $p$ e' un safe prime allora $Z_p^*$ ha per forza due sottogruppi, uno di ordine 2 (che sara $\{-1,1\}$) e un'altro di ordine $q$, chiamiamolo $Q$. 

Notiamo che esattamente meta' degli elementi di $Z_p^*$ generano $Q$ e gli altri generano tutto $Z_p^*$. Quelli che generano $Q$ sono la versione modulare dei quadrati perfetti, che vengono chiamati **residui quadratici**.

Per esempio in $Z_{23}^*$, 3 e' un residuo quadratico, visto che esiste un numero che moltiplicato per se stesso da 3, si tratta di 7: $7\cdot 7 \mod 23 = 3$. 

Facendo $x^q \mod p$, se il risultato e' 1 allora genera $Q$, in caso contrario genera tutto $Z_p^*$. 
# L'Esponenziale modulare
L'esponenziale modulare e' una funzione one-way. 

E' facile da calcolare, con un trucco. Infatti fare tutte le moltiplicazioni ripetute e' infattibile sia per tempo che spazio. 

Ci serve prima un modo efficiente di calcolare il prodotto $a\cdot b$. Facciamo un esempio con $b = 5_{10} = 101_{2}$. 

Abbiamo che $a\cdot b = a\cdot 1\cdot 2^2 + a\cdot 1\cdot 2^0$. Ovvero basta fare la somma di per tutte le potenze di 2 contenute in b, ma cio' significare sommare alcuni valori di $a$, ognuno raddoppiato un tot di volte. Per raddoppiare basta uno shift di bit a sinistra (molto veloce), anche la somma e' efficiente. 

#todo la parte sulla potenza non la capisco.

### Logaritmo discreto
L'inverso dell'esponenziale $b^e = x \mod n$ e' il **logaritmo discreto**: $e = \log_b x \mod p$. 

In $Z_n$ non e' detto che ogni numero abbia un logaritmo, esiste solo se $x$ e' coprimo con $n$. In $Z_p^*$ il generatore di tutto il gruppo e' anche una base per cui esiste il logaritmo di ogni elemento.

Notare che nei gruppi additivi il **logaritmo additivo** di $x$ esiste, ma e' sta volta definito a partire dalla moltiplicazione. E' infatti il valore $k$ tale che $k\cdot b = x \mod n$. Si usa nelle [[Curve ellittiche]].

Per un qualsiasi gruppo $Z_p^*$ non sono noti algoritmi polinomiali per sapere se $a \in Z_p^*$ e' un generatore, ne per trovarne uno.

Il numero di generatori di $Z_p^*$ si indica con $\phi(p)$ (**funzione toziente di Eulero**), definita come: $\phi(p) = |\{i | 1 \leq i < p, MCD(i,p)=1\}|$, ovvero tutti i numeri minori di $p$ e coprimi con esso. Si usa nel protocollo [[RSA]].

Nel caso di $n = p\cdot q$ con $p,q$ primi, calcolare $\phi(n)$ e' molto facile $\phi(n) = (p-1)(q-1)$.

Infatti tra i numeri minori di $n$, $p$ non e' coprimo, ma anche $2p,3p, ..., (q-1)p$. DI questi ce ne sono appunto $(q-1)$. Lo stesso ragionamento vale per $q$, togliendo altri $(p-1)$ numeri.

Quindi $\phi(n) = n - (p-1) - (q-1) - 1 = (p-1)(q-1)$.

Facendo la scomposizione in numeri primi, questo ragionamento vale per tutti gli $n$, anche quelli non in forma $n=p\cdot q$.

Come detto all'inizio, non e' dimostrato che il logaritmo esponenziale sia davvero difficile da calcolare, ma tutti gli algoritmi che si conoscono hanno un worst case con numeri di lunghezza esponenziale.

Questo vuol dire che non sono tanto meglio del **brute force**, dove si provano tutti gli esponenti sperando di trovare quello corretto ($O(2^{n-1})$).

### Baby-Steps Giant-Steps
Si tratta di un algoritmo brute force che bilanciando spazio e tempo arriva a $O(2^{n/2}$).

Supponendo di calcolare $\log_g x \mod p$, scegliamo due numeri $r\cdot s \geq p$. Notare che ogni $t \in Z_p$ puo' essere espresso come $t = j+i \cdot r$ per $j$ che va da 0 a $r-1$ e $i$ che va da 0 a $s-1$ #ipotesi.

Calcoliamo le due successioni $1,g,g^2, ..., g^{r-1}$ e $x,xg^{-r},xg^{-2r},...,xg^{-(s-1)r}$.

Se per qualche valore $i,j$ abbiamo $g^i = xg^{-jr}$ allora abbiamo $g^{i+jr} = x$   che vuol proprio dire $i+jr = \log_g x$. L'unica possibile falla e' che $g^{i+jr}$ potrebbe non appartenere a $Z_p^*$, ma questo e' impossibile per quanto detto nell'[ipotesi](#ipotesi).

