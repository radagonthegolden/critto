Le firme digitali servono ad assicurare che un messaggio sia effettivamente arrivato da chi dice di essere stato mandato. Questo per evitare, ad esempio, attacchi [[Man in the Middle]].

# Firma con [[RSA]]
Possono essere implementate in qualsiasi protocollo asimmetrico, ma vediamolo nel caso dell'[[RSA]]. 

>Nell'RSA abbiamo la chiave pubblica $e$ e la chiave privata $d = e^-1$. Per cifrare B fa $C = M^e \mod n$ e per decifrare A fa $M = C^d \mod n$

Il problema sorge quando A vuole rispondere a B, e vuole firmare il *nuovo* messaggio $H$ per identificarsi. La firma si ottiene come $F = H^d \mod n$

B, avendo la chiave pubblica $e$, può fare $H' = F^e \mod n$. 

Solo che non ha niente con cui confrontare $H'$. Infatti in realtà A invia $(H,F)$, B potrà poi vedere se $H = H'$, caso in cui si è verificata l'identità di A, visto che solo lui poteva avere $d$. 

Chiaramente A non può mandare il vero messaggio in chiaro, infatti questo $H$ inviato è in realtà l'[[prereq_2#Funzione trapdoor|hash]] del vero messaggio.
## Blinding attack
Si tratta di un attacco alla firma digitale con 2 condizioni necessarie per E:
- Poter "inserire" un messaggio malevolo in un messaggio firmato da A (ottenibile solo con ingegneria sociale)
- Poter scegliere a piacere un testo cifrato

Il procedimento e':
1. E cerca $R$ tale che $X = R^e\cdot M \mod n$ sia un messaggio che A firmerebbe (e che chiaramente porti dei vantaggi a E). 
2. A firma, producendo $F = X^d \mod n$ 
3. Siccome $F = X^d \mod n = (R^e\cdot M \mod n)^d = R^{ed}\cdot M ^d \mod n = R\cdot M^d \mod n$ a E basta calcolare $F \cdot R^{-1} \mod n$ per ottenere $M^d \mod n$, ovvero la firma vera!

Si tratta comunque di un attacco molto improbabile visti i requisiti. Senza contare che usando l'[[prereq_2#Funzione trapdoor|hash]] del messaggio. 

**Probabilistic Signature Scheme:** in realtà dopo l'hash il messaggio e' più corto della lunghezza richiesta dall'[[RSA]], perciò si allunga con un padding.
# Firma con [[Rabin El Gamal#Il Sistema crittografico El-Gamal|El-Gamal]]
Ricordiamo che con questo algoritmo A sceglie $p$ che definisce $Z^*_p$, $g$ generatore di $Z^*_p$ e $a \in Z^*_p$. Calcola poi $A = g^a \mod p$ che diffonde come chiave pubblica (insieme a $p,g$), mentre tiene $a$ come chiave privata.

Nonostante le chiavi siano le stesse, il protocollo di firma non e' uguale a quello di cifratura. 

Firma del messaggio (fatta da A):
1. Viene scelto $k$ a caso tale che $MCD(k, p-1) = 1$
2. Vengono calcolati $r = g^k \mod p$ e $s = k^{-1} (M-a\cdot r) \mod (p-1)$ 
3. Viene inviato a B il messaggio firmato $(M,(r,s))$

Verifica della firma (fatta da B):
1. Vengono calcolati $x_1 = A^r\cdot r^s \mod p$ e $x_2 = g^M \mod p$. Ricordiamo che B conosce $p,g,A$ in quanto chiavi pubbliche
2. Si accetta la firma se $x_1 = x_2$

Controlliamo perché ha senso. 

Intanto vediamo a cosa corrisponde $r^s \mod p$:
$$\begin{align}
r^s \mod p &= (g^k \mod p)^s \mod p \\
&= (g^k)^{k^{-1}(M-a\cdot r)\mod (p-1)} \mod p \\
&= (g^k)^{k^{-1}(M-a\cdot r)-(p-1)/h} \mod p \\
&= (g^k)^{k^{-1}(M-a\cdot r)}(g^k)^{-(p-1)/h} \mod p \\
&= g^{M-a\cdot r} (g^{-kh})^{p-1} \mod p \\
&= g^{M-a\cdot r} ((g^{-kh})^{p-1} \mod p) \mod p \\
&= g^Mg^{-a\cdot r} \mod p \\
&= g^M (g^{-a} \mod p)^r \mod p \\
&= g^M A^{-r} \mod p
\end{align}$$
Dove $h$ e' il resto della divisione di $k^{-1}(M-a\cdot r)$ per $p-1$

Detto ciò e' chiaro che senza manomissione o errori si ha:
$x_1 = A^r \cdot r^s \mod p =A^r \cdot (r^s \mod p) \mod p = A^r\cdot (g^M A^{-r} \mod p) \mod p = g^M \mod p = x_2$
### Usi multipli di $k$
E' molto importante usare il $k$ scelto a caso **una volta sola**. 

Sapendo che $\log_g x_2 \mod p = M\mod p$ vediamo che vale:

$$\begin{align}
\log_g x_1 \mod p &= \log_g A^r \cdot r^s \mod p \\
&= (r\cdot \log_a A+s\cdot \log_g r) \mod p \\
&= (r\cdot \log_g A + s\cdot \log_g r) \mod p \\
&= (r\cdot a + s\cdot k) \mod p
\end{align}$$

Quindi siccome con una firma autentica vale $x_1 = x_2$, deve valere $\log_g x_1 \mod p= \log_g x_2 \mod p$. Mettendo tutto assieme vediamo che vale:
$(r\cdot a + s\cdot k) \mod p = M \mod p$

Quindi se due messaggi $M_1,M_2$ sono firmati con lo stesso $k$ vale il sistema di due equazioni in due incognite:
$$\begin{cases}
a\cdot r_1 + k\cdot s_1 \mod p = M_1 \mod p \\
a\cdot r_2 + k\cdot s_2 \mod p = M_2 \mod p
\end{cases}$$
Invece se $k$ cambia sempre allora qualsiasi sistema avrà sempre un'incognita in più rispetto all'equazione. Quindi impossibile da risolvere. 
### Valore hash del messaggio
Vediamo che il protocollo **deve** usare l'hash del messaggio. 

E sceglie due numeri $x,y$ tali che $MCD(y,p-1) = 1$, dopo si pone:
$r = g^x \cdot A^y \mod p = g^x \cdot (g^a \mod )^y \mod p = g^{x+a\cdot y} \mod p$
$s = -r\cdot y^{-1} \mod (p-1)$ 

E sa' infatti che B controllerà se:
$A^r \cdot r^s \mod p = g^M \mod p$

Dunque pone $M = x\cdot s (\mod (p-1))$

Infatti risulta che:
$$\begin{align}
A^r\cdot r^s \mod p &= (g^a \mod p)^r \cdot (g^{x+ay} \mod p)^s \mod p \\
&= g^{ar}\cdot g^{(x+ay)s} \mod p \\
&= g^{ar+xs+ays} \mod p \\
&= g^{xs} \mod p \\
&= g^{M+h(p-1)}\mod p \\
&= g^M \mod p
\end{align}$$
Dove la correttezza del terzo passaggio deriva dalla scelta di $s$. Infatti siccome $s = -r\cdot y^{-1} \mod (p-1)$ risulta $y\cdot s = -r+h'\cdot (p-1)$ con $h'$ resto della divisione per $p-1$. 

