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
Vediamo che il protocollo **deve** usare l'hash del messaggio per evitare che E possa usare la firma di A.

E sceglie due numeri $x,y$ tali che $MCD(y,p-1) = 1$, dopo si pone:
$r = g^x \cdot A^y \mod p = g^x \cdot (g^a \mod )^y \mod p = g^{x+a\cdot y} \mod p$
$s = -r\cdot y^{-1} \mod (p-1)$ 

E sa' infatti che B controllerà se:
$A^r \cdot r^s \mod p = g^M \mod p$

Dunque pone $M = x\cdot s \mod (p-1)$

Infatti risulta che:
$$\begin{align}
A^r\cdot r^s \mod p &= (g^a \mod p)^r \cdot (g^{x+ay} \mod p)^s \mod p \\
&= g^{ar}\cdot g^{(x+ay)s} \mod p \\
&= g^{ar+xs+ays} \mod p \\
&= g^{xs} \mod p \\
&= g^{M+h(p-1)}\mod p \\
&= g^M \mod p
\end{align}$$
Dove la correttezza del terzo passaggio deriva dalla scelta di $s$. Infatti siccome $s = -r\cdot y^{-1} \mod (p-1)$ risulta $y\cdot s = -r+h'\cdot (p-1)$ con $h'$ resto della divisione per $p-1$, dunque:
$g^{ar+ays} \mod p = g^{ar-ar+ah'(p-1)} \mod p = 1$

Quindi siccome $A^r\cdot r^s \mod p = g^M \mod p$ la firma risulta valida!

Per evitare questo problema A non firma $M$, firma invece $H(M||r)$ dove $H$ è la funzione di hash, e $||$ indica la concatenazione di $M$ con $r$. Per E risulta inutile porre $M = x\cdot s \mod (p-1)$ perché non è questo che B usa nella verifica. E dovrebbe trovare un messaggio $M$ (che la avvantaggi) e tale che $H(M||r) = x\cdot s \mod (p-1)$. Ma questo vorrebbe dire che $H$ non è **first pre-image resistant**.
# Firma digitale con DSA
Il **Digital Signature Algorithm** è quello di fatto in uso. A differenza degli altri è stato pensato da subito per usare una funzione hash, nello specifico la **SHA-1**.

### Protocollo originale

**Generazione delle chiavi** (fatta da A):
1. Genera un numero primo $q$ di **160 bit**
2. Genera un numero primo $p$ di **1024 bit** tale che $q$ sia un divisore primo di $p-1$
3. Determina un elemento $g \in Z_p^*$ di ordine $q$, ovvero $g$ è il generatore del sottogruppo $Z_q$ di $Z_p^*$ aventi $q$ elementi
4. Sceglie a caso $a \in Z_q$, che sarà la **chiave privata**
5. Calcola $A = g^a \mod p$
6. Pubblica $(p,q,g,A)$ come **chiave pubblica**

**Firma del messaggio** (sempre fatta da A):
1. Creato il messaggio $M$, calcola $m = SHA-1(M)$ (160 bit)
2. Sceglie $k$ a caso in $Z_q^*$
3. Calcola $r = (g^k \mod p) \mod q$ e $s = k^{-1}(m+ar) \mod q$
4. Se $r$ o $s$ finiscono per essere $0$ vengono scartati e si torna al passo 2
5. Altrimenti viene inviato il messaggio $M$ insieme alla firma $(r,s)$

**Verifica della firma** (fatta da B):
1. Controlla che $0 < r,s <q$ (se non fosse così qualcosa è andato storto)
2. Calcola $x = SHA-1(M) \cdot (s^{-1} \mod q)$
3. Calcola $y = r\cdot (s^{-1} \mod q)$
4. Esegue il controllo $(g^x A^y \mod p) \mod q = r$ e accetta se l'uguaglianze è vera

La sicurezza del protocollo dipende dalla difficoltà del **logaritmo discreto** su un sottogruppo di $q$ elementi. Nel protocollo originale $q$ è scelto con 160 bit, quindi abbiamo $\log(\sqrt{2^{160}}) = 80$ bit di sicurezza (per quanto riguarda gli algoritmi correnti). 

In realtà $p$ può uno qualsiasi tra i valori 1024,2048 o 3072 bit. Allo stesso modo $q$ si può scegliere di lunghezza 160, 224 o 256 bit. La funzione hash può essere una qualsiasi nelle pubblicazioni **FIPS 180**.

# Autenticità delle chiavi
Perché i protocolli di firma digitale e di [[RSA|cifratura asimmetrica]] funzionino serve che B sia sicuro che le chiavi pubbliche che usa siano effettivamente di A. Ci sono due approcci per avere questa garanzia.

## TLS
Si usa nelle interazioni tra web client (un browser) e un web server. 

Di solito è il client che contatta il server. Questo contatto **è in chiaro**, quindi il rischio di attacchi preliminari come **site-spoofing** sono possibili. Il server invia poi un **certificato di autenticità**, che essenzialmente è la chiave pubblica del server firmata da una qualche autorità riconosciuta. 

L'interazione prosegue in due modi diversi in base a se il client possiede già o no un certificato del server **verificato e ancora valido**:
- Se il client possiede già questo certificato allora ha anche la chiave pubblica del server, quindi si può andare avanti normalmente. 
- Se il client non possiede il certificato, ma questo è firmato da una **autorità** che il client ha già certificato allora considera la chiave pubblica del server autentica e se la memorizza nel **keyring** per usi futuri. Si può procedere come necessario.
- Se il client non ha nemmeno certificato l'autorità che garantisce per il server. Queste autorità sono organizzate ad **albero**, quindi il client controlla **l'autorità che garantisce per l'autorità che garantisce per il server**. Se il client non ha certificato nemmeno questa si sale di livello. Risulta chiaro che le autorità **top-level** devono essere memorizzate nel **sistema operativo appena installato**. Eventualmente il client raggiunge una fonte che ha certificato, questo certifica tutte le autorità sotto di lei e infine il server.

## GnuPG
Se il TLS ha un'organizzione ad albero, la GnuPG è tipo "peer-to-peer". Si tratta infatti di una soluzione molto leggera che non potrebbe essere usate per gli scopi TLS come e-commerce, home-banking, ecc. 

GnuPG funziona tramite una **web of trust**, facciamo un esempio:
1. Un docente vuole firmare le email che manda ai suoi studenti perché questi siano certi che vengano da lui.
2. Se uno studente vuole validare la firma del prof, può accedere ad un server di chiavi PGP (per esempio l'**MIT PGP**) e recuperare la chiave pubblica del docente. Come fa però lo studente ad essere sicuro che la chiave non sia stata manomessa?
3. La chiave ha associata una **fingerprint** (un hash) che viene comunicata tramite un canale sicuro. Per esempio durante una lezione di persona (non c'è chance di manomissione). Uno studente che ha ricevuto la fingerprint in persona attribuisce alla chiave pubblica un **alto grado di fiducia**.
4. Gli studenti che non erano presenti a lezione, ma a cui serve comunque la chiave, possono vedere che quelli presenti hanno dato un altro grado di fiducia a quella particolare fingerprint. Perciò la importano con un grado di fiducia leggermente inferiore a quello dei compagni presenti a lezione. 

La forza di questa **web of trust** sta nel fatto che è difficile imbrogliare tante persone alla volta. Quindi se uno studente senza chiave pubblica può "fidarsi" del fatto che se tante persone hanno dato un alto grado di fiducia ad una particolare chiave, probabilmente è quella vera. 

Il protocollo OpenPGP ha poi delle regole precise sul come far crescere questa rete di fiducia. 





