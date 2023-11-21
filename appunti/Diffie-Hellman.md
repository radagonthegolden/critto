I ricercatori Diffie ed Hellman furono i primi ad esaminare l'idea di una cifratura asimmetrica, che permetterebbe al ricevente dei messaggi di gestire solo una coppia di chiavi invece che una per ogni trasmissione. Non ci riuscirono ma il protocollo a cui arrivarono è essenziale per lo **scambio di chiavi private simmetriche**.

Il problema è che per accordarsi su una chiave segreta, bisogna per forza mandare informazioni su un canale insicuro, prima o poi.

# L'algoritmo
A e B si accordano su $p$ primo e $g$ generatore di $Z_p^*$, tutto pubblicamente. Di solito è chi inizia la conversazione a deciderli, il ricevente li conferma e basta.

- A sceglie a caso uniformemente $a \in Z_p^*$, e calcola $x_a = g^a \mod p$, inviando $x_a$ a B.
- B fa la stessa cosa, scegliendo un $b \in Z_p^*$ e inviando ad A $x_b = g^b \mod p$. 
- A calcola $z_a = x_b^a$, similmente B calcola $z_b = x_a^b$. 
- Chiaramente $z_a = g^{b^a} \mod p = g^{ab} = g^{a^b} \mod p = z_b := z$. Partendo da $z$ generiamo la chiave segreta.

L'algoritmo è molto efficiente: sappiamo che calcolare expmod è di costo polinomiale, e la scelta di $p$ viene fatta grazie ad un **[[prereq_1#Safe Prime | safe prime]] con generatore fisso**.

La sicurezza si basa sul fatto che per trovare $z = g^{ab}$, anche sapendo $g^a$ o $g^b$, servono $a$ e $b$. Il problema è che per risalire ad uno dei due bisogna fare un logmod, un problema NP. Notare ciò non è provato, infatti l'idea che rompere DH sia difficile è appunto chiamata **Computational Diffie-Hellman assumption**.

La $z$ trovata non può essere usata direttamente come chiave segreta. Infatti vorremmo che questa abbia ogni bit a 0/1 con probabilità $1/2$, mentre $z$ essendo un numero di $Z_p$ in generale non ha questa proprietà. Per fare ciò basta applicare una [[Funzioni hash|funzione hash]] a $z$. 

## Man-in-the-middle
Decifrare i messaggi è infattibile, però è possibile per E mettersi "in mezzo" alla comunicazione tra A e B. Questo è del tutto fattibile senza protocolli di autenticazione robusti. E può decidere di alterare i messaggi (short term attack) oppure non alterarli e passare inosservato (long term attack).

Per evitarlo ognuna delle parte deve possedere una chiave pubblica dell'altra parte per verificarne la firma (oltre al normale scambio per il DH):
![[authDH.jpg]]

In generale quindi per scambiarsi chiave simmetriche serve comunque un **long-term secret** come una chiave RSA. Questo non rende DH inutile? Non potremmo direttamente usare la crittografia asimmetrica?

In realtà solo usando solo la crittografia asimmetrica non si ha **forward-secrecy**, infatti se E scopre la chiave privata di A o B riesce a leggere tutti i messaggi che si sono scambiati in passato. 

L'idea è quindi di autenticarsi con chiavi asimmetriche, per poi accordarsi su una chiave DH simmetrica **effimera**, ovvero che verrà scartata dopo una conversazione. Se anche questa chiave fosse scoperta, sarebbe inutile per messaggi futuri. 