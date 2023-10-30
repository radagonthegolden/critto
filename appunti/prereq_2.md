Le sequenze di bit casuali ed indipendenti sono fondamentale per la crittografia.

Non è facile generare numeri casuali da un computer perché questi sono deterministici, in generale si sfruttano **fenomeni fisici esterni** usando:
1. Un **trasduttore** che trasforma un fenomeno fisico in segnali elettrici
2. Un **amplificatore** che amplifica i segnali
3. Un **convertitore analogico-digitale** che trasforma i segnali in bit

Generare sequenze di bit così è molto costoso e la lunghezza delle sequenze può non essere adatta. Perciò di solito si parte da sequenze davvero casuali per generarne altre di lunghezza utile, quante volte vogliamo. Queste sequenze sono dette **pseudo-casuali**.

In Linux fenomeni difficilmente predicibili come l'input da utente è inserito nella **entropy pool** che viene data ai programmi quando richiesta.
# Algoritmi probabilistici
Si tratta di algoritmi che sfruttano una qualche **sorgente di casualità**. Quelli che vedremo sono detti **algoritmi decisionali** perché il loro output è solo true/false.

## Algoritmi Monte Carlo
Si dicono così gli algoritmi tali che:
1. Se l'input è True (una mail è spam), restituisce True con una probabilità fissa maggiore di zero.
2. Se l'input è False (la mail non è spam), restituisce sempre False.
Precisamente, un algoritmo del genere è **probability bounded one-sided error**:
- **probability bounded:** la probabilità di errore non tende ad infinito al crescere dell'input.
- **one-sided error:** siccome può dare True solo su risposte True, l'algo sbaglia solo se ha dato False e in realtà l'input era True. Il caso "l'input era False ma ha detto True" non esiste.

Un algoritmo del genere è utile perché se la probabilità che sia corretto è costante, possiamo ripetere molte run per aumentarla a piacere. Supponiamo sia anche piccola $\varepsilon = 0.01$. Noi runniamo l'algoritmo $N = 100$, la prima volta che dice True allora anche l'input era per forza True. Se invece tutte le run dicono False, dichiariamo l'input False.

Qual è la probabilità che sia errato? Capita solo se l'input era True, ma anche in quei casi la probabilità di sbagliare è $(1-0.01)^N = 0.36$, abbiamo già un due terzi di avere ragione. Con un computer, runnare anche migliaia di test è veloce, facendo decrescere la probabilità di aver sbagliato.

Come potrebbe essere un algoritmo non probability bounded? Mettiamo di costruire un numero $n$ è composto. Per farlo semplicemente scegliamo un numero dispari $d$ tra $[1,n/2]$. Se $n \mod d = 0$ allora diciamo che è composto (True), sennò diciamo che è primo (False).

Chiaramente è one-sided error: se il numero è primo (False) nessun $d$ potrà dividerlo e diremo sempre che è primo (False). Se però è composto (True) abbiamo una probabilità di dire che è primo (False). Qual è questa probabilità?

I divisori di un numero vanno in coppia: se $x$ divide $n$ allora anche $x/n$ lo divide. Inoltre $x$ potrà essere al massimo $\sqrt{n}$. Quindi i divisori di $n$ sono al massimo $2\sqrt{n}$. Il numero $d$ è estratto tra tutti i numeri dispari tra $1$ e $n/2$, quindi sono $n/4$. Di conseguenze per ogni $n$ la percentuale massima di divisori è $\frac{2\sqrt{n}}{n/4} = \frac{8}{\sqrt{n}}$. Questo numero cresce all'infinito per $n \to \infty$. 
## Algoritmi Las Vegas
Si tratta di algoritmi che restituiscono **sempre la risposta giusta** ma ci mettono un **tempo casuale**, possibilmente infinito. 

Per esempio mettiamo di avere una sequenza infinita di bit casuali ma non uniforme, quindi $P(z_1=0) = p, P(z_i = 1) = 1-p$, da cui vogliamo estrarre una sequenza sempre causale ma uniforme: $P(x_i = 0) = P(x_i = 1) = 1/2$. 

L'algorimo è: 
1. $i=1$
2. Se $z_i = 0 \land z_{i+1} = 1$ allora restituisci 0
3. Se $z_i = 1 \land z_{i+1} = 0$ allora restituisci 1
4. $i = i+2$

La probabilità che $z_i = 0 \land z_{i+1} = 1$ è $p(1-p)$ è uguale alla probabilità che $z_i = 1 \land z_{i+1} = 0$, ovvero $(1-p)p$. Quindi l'algoritmo prima o poi ritorna la sequenza cercata, ma non sappiamo quanto ci mette. Se tutti i bit $z_i$ sono uguali non finisce mai.

#todo-maybe studio sul tempo di attesa.
# La domanda dei numeri primi
I primi sono fondamentali nella crittografia, conviene chiederci quanti ce ne siano tra 1 e $n$. Per questo abbiamo il **Prime number theorem**, il quale dice che se $\pi(N)$ è il numero di primi $\leq N$ allora vale $\pi(N) \sim \frac{N}{\ln N}$.

Seppur facendo una semplificazione, possiamo circa dire che estraendo un numero a caso tra 1 e $N$ ho un primo con probabilità $1/\ln N$. 

Il modo più efficiente per generare un primo casuale è semplicemente produrre un numero a caso e controllare se è primo. Bisogna però che questo "controllo che sia primo" sia esso stesso efficiente. Infatti volendo un numero primo di $n$ bit, in media dobbiamo fare $n \ln 2 \simeq 0.69n$, siccome $n$ può essere gigante, servono **prime check** efficienti.
## Piccolo Teorema di Fermat
Questo dice che se $p$ è primo, allora vale:
	$x^{(p-1)} = 1 \mod p$
Per ogni $x$ coprimo con $p$, in particolare vale per ogni $x$ positivo e minore di $p$. 

Il viceversa non vale però: infatti se $n$ è composto e anche $x$ è composto, potrebbe comunque valere $x^{n-1} \mod n = 1$. Caso in cui $n$ è detto **pseudo-primo in base x**. La loro esistenze ci impedisce di poter usare il PTF come test della primalità. Sono comunque molto pochi, si stima che gli pseudo-primi in base 2 siano circa 1 su $10^{20}$ fra tutti i numeri di 512 bit. 

Un algoritmo del genere 
```
from ACLIB.utils import modexp 
def FLT_primality(n): 
	"Fermat's Little Theorem primality test" 
	return modexp(2,n-1,n) == 1
```
è di sicuro one-sided error, ma non è Monte Carlo. In realtà non è neanche probabilistico: se continuo a provare lo stesso numero continuerò ad ottenere lo stesso risultato. 

Si potrebbe pensare di renderlo migliore prendendo una base casuale invece che 2, ma ci sono numeri che sono pseudo-primi in ogni base, sono detti **numeri di Carmichael**. Sono rari, ma relativamente abbondanti nei piccoli numeri. 
## Residui quadratici e test di Solovay-Strassen
Ricordiamo che un elemento $a \in Z_n$ è detto **residuo quadratico** se esiste $x \in Z_n$ tale che $a = x^2 \mod n$. 

### Simbolo di Legendre
Se $p$ è primo e $a \in Z_p$, il **simbolo di Legendre** è $\left( \frac{a}{p} \right)$ è la funzione con output:
- 0 se $p$ divide $a$
- 1 se $a$ è un RQ mod $p$
- -1 altrimenti

Si può calcolare così: $\left( \frac{a}{p} \right) = a^{\left( \frac{p-1}{2} \right)} \mod p$, si tratta quindi del semplice calcolo di un [[prereq_1#L'Esponenziale modulare | esponenziale modulare]]. 

Il simbolo di Legendre gode di alcune interessanti proprietà, le più notevoli sono:
- **Criterio perché 2 sia un RQ mod p:**
	- $\left( \frac{2}{p} \right) = -1^{\frac{p^2-1}{8}}$, da cui risulta che 2 è RQ solo se $p = 1,7 \mod 8$, ovvero se $p = 8k+1$ oppure $p = 8k+7$. 
- **Criterio perché 5 sia un RQ mod p** è che $p = 1,4 \mod 5$.
#todo: guardare se serve davvero in Diffi-Helmann
### Simbolo di Jacobi
Si tratta della generalizzazione del simbolo di Legendre per numeri composti. Infatti preso un $n$ intero dispari scomposto in $p_1,p_2,...,p_k$ primi (non per forza distinti), per ogni $a>0$ vale:
	$\left( \frac{a}{n} \right) = \prod_{i=1}^k \left( \frac{a}{p_i} \right)$ 
Ovvero semplicemente il simbolo di Jacobi è il prodotto di tutti i simboli di Legendre fatti secondo ogni primo che compone $n$. Questo però porta a perdere alcune sicurezze che avevamo nel simbolo di Legendre.

Infatti se $\left( \frac{a}{n} \right) = 1$ non è detto che $a$ sia RQ di $n$, potrebbe infatti essere che sia composto da due simboli di Legendre entrambi -1, quindi $(-1)(-1)=1$. Allo stesso modo se $a$ è un RQ mod $n$, può succedere che $\left( \frac{a}{n} \right) = 0$, ma solo se non sono coprimi.

Valgono le seguenti proprietà:
1. $\left( \frac{ab}{n} \right) = \left( \frac{a}{n} \right) \left( \frac{b}{n} \right)$
2. Se $a = b \mod n$ allora vale $\left( \frac{a}{n} \right) = \left( \frac{b}{n} \right)$
3. $\left( \frac{2}{n} \right) = 1 \iff n = 1,7 \mod 8$, altrimenti deve valere $-1$ perché di sicuro 2 non divide $n$ (tranne per il caso banale di $n = 2$).
4. Se $n,m$ sono **interi dispari** allora $\left( \frac{m}{n} \right) = -\left( \frac{n}{m} \right)$ se $n,m = 3 \mod 4$, altrimenti vale $\left( \frac{m}{n} \right) = \left( \frac{n}{m} \right)$

Questo proprietà ci permettono di calcolare il simbolo di Jacobi senza dover fattorizzare $n$, che sappiamo essere un'operazione costosa. Per farlo basta usare questo codice:
```
from ACLIB.utils import Euclid,modexp 
def jacobiS(a,n): 
	if Euclid(a,n)>1: #non sono coprimi
		return 0 
	if a==1: #per forza a divide n visto che 1 divide tutti i numeri
		return 1 
	if a==2: #proprietà 3 di sopra
		if n%8==1 or n%8==7 else -1 
	if a%2==0: #proprietà 1 di sopra 
		return jacobiS(2,n)*jacobiS(a//2,n) 
	if a>n: #proprietà 2 sopra (chiaramente a mod n== a%n mod n) 
		return jacobiS(a%n,n) 
	if a%4==3 and n%4==3: #proprietà 3, controlliamo il caso con il meno
		return -jacobiS(n,a) # a=n=3 (mod 4) 
	return jacobiS(n,a) #sempre proprietà 3, che a questo punto deve valere
```

Avendo il simbolo di Jacobi possiamo ora controllare che $\left( \frac{a}{n} \right) = a^{\left( \frac{n-1}{2} \right)} \mod p$ valga anche per un $n$ che non siamo sicuri essere primo (ovviamente non siamo sicuri, tutto questo serve proprio a capire se lo è). 

Anche con Jacobi però è possibile che l'uguaglianza sopra valga senza che $n$ sia primo, caso in cui viene chiamato **pseudo-primo di Eulero base $a$**. La cosa importante è che un numero non primo NON può essere pseudo-primo di Eulero per tutte le basi $a$. Un valore $a$ che divide $n$ oppure tale che $\left( \frac{a}{n} \right) \neq a^{\left( \frac{n-1}{2} \right)} \mod p$ è detto **testimone della non primalità di n**.

**Algoritmo di Solovay e Strassen:**
- Dato $n>2$ dispari scelgo $a \in [1,...,n-1]$ a caso.
- Calcolo $m = MCD(a,n)$ e se $m>1$ allora $a$ divide $n$, ritorno `composto`
- Calcolo $J = \left( \frac{a}{n} \right)$ e $P = a^{\left( \frac{n-1}{2} \right)} \mod n$
- Se $P \neq J$ ritorno `composto`
- ritorno `probabilmente primo`

Si può dimostrare che per un $n$ esistono al massimo $\frac{n-1}{2}$ interi $a$ tali che $n$ è pseudo-primo di Eulero a base $a$. Quindi gli altri $\frac{n-1}{2}$ sono testimoni della non primalità. Tutto questo vuol dire che l'algoritmo SS ha una probabilità di sbagliare quando dice `probabilmente primo` non superiore a $1/2$. 
# Funzione trapdoor
La moltiplicazione di numeri primi è un buon candidato per essere una funzione trapdoor. Dai $p_1,...,p_k$ moltiplicarli è semplice, ma fattorizzare il loro prodotto è arduo. Infatti un algoritmo brute force proverebbe tutti i divisori $d \leq \sqrt{n}$, impiegando tempo esponenziale. 

Ci sono modi migliori, l'algoritmo più rapido è il **General Number Field Sieve**, che empiricamente sembra avere un tempo **super-lineare**, più di lineare ma non esponenziale. Si tratta di un algoritmo molto complicato che non vedremo, studiamo invece quello di Pollard.
## Fattorizzazione di $\rho$ Pollard
Partendo da $x_1 \in Z_n$ produce una successione $x_{i+1} = f(x_i) \mod n$ che dovrebbe avere la proprietà di sembrare casuale, fino a che non inizia a ripetersi. Ogni tanto un valore viene salvato e usato nelle iterazioni successive. Le iterate salvate sono le potenze di $2$: $0,1,2,4,8,16,...,$ ecc. 

La funzione che si usa tipicamente è $f(x) = x^2-1 \mod n$. 
```
from ACLIB.utils import Euclid, rand
def pollard_rho(n):
	i = 2
	j = 1
	x_old = rand(n)
	y = x_old
	while True:
		j += 1
		x = (x_old*x_old -1)%n
		if (d :=Euclid(y-x,n) > 1) and d!=n:
			return p
		if j == i:
			y = x
			i *= 2
		x_old = x
```

Siccome lavoriamo sempre in $Z_n$ la successione dovrà avere un numero già visto prima. Ma siccome la sequenza dipende solo dal numero precedente, questa si ripeterà uguale da allora. La forma è quella di un gambo con i numeri generati una volta, che poi si chiude in un cerchio. 

![[rho.PNG]]

In generale il cerchio avrà una lunghezza di circa $O(\sqrt{n})$.

Notare che questo algoritmo restituisce **sempre** un fattore non banale di $n$. Infatti andiamo avanti finche `d = MCD(y-x,n) > 1`, quindi $p$ è divisore di $n$. Ma siccome $d$ divide anche la differenza $y-x$, si ha che $y \mod p = x \mod d$. Ovvero troviamo un divisore quando abbiamo due iterate **diverse** ma **congrue modulo un divisore di $n$**. L'algoritmo  in realtà lavora su una **sequenza ombra** di $z_i = x_i \mod d$. 

>**Proprietà:** se $d$ divide $n$, allora per ogni $x$ vale $(x\mod n) \mod d = x \mod d$

Con ciò possiamo vedere che:
$$  
\begin{align}  
z_{i+1} &= x_{i+1} \mod d \\
&= ((x_i^2-1) \mod n) \mod d \\
&= (x_i^2-1)\mod d \\
&= ((x_i \mod d)^2 -1) \mod d \\
&= (z_i^2 -1) \mod d

\end{align}  
$$
Notiamo quindi che $z_{i+1} = (z_i^2-1) \mod q$, esattamente ciò che facciamo con gli $x_i$ ma con $\mod d$ invece che $\mod n$. Anche questa successione ha una forma a "rho", con "testa" lunga circa $O(\sqrt{q})$. Siccome salviamo i valori ad intervalli sempre più grandi siamo sicuri che prima o poi $z_{2^i}$ è dentro la testa del rho. Quindi circa $2^i = O(\sqrt{d})$. 

Siccome $z_i = x_i \mod d$, vale che $x_j \mod d = x_{2^i} \mod d \implies x_j-x_{2^i} \mod d = 0$ ovvero $x_j-x_{2^i}$ è un divisore di $d$, quindi anche di $n$. 

Potrebbe però succedere che le due successioni finiscano per coincidere **nella stessa iterata**, senza darci nessuna informazione in più. 

Per esempio consideriamo $n = p\cdot q$ con $p,q$ primi. Arriviamo ad avere $x_j, x_{2^i}$ congruenti modulo $p$ e modulo $q$. Siccome sono primi, questo vuol dire che sono anche congruenti modulo $n \implies MCD(x_j, x_{2^i}) = n$. Ovvero il "divisore" di $n$ che abbiamo trovato è proprio $n$, nessuna informazione in più.

**L'efficienza** dell'algoritmo dipende da $p$, più piccolo fattore di $n$, ed è empiricamente registrata come $O(\sqrt{p})$. Siccome $p < \sqrt{n}$ la stima è di circa $O(^4\sqrt{N})$, dove $N$ è la lunghezza di bit.  

