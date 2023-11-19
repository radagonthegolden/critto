Le firme digitali servono ad assicurare che un messaggio sia effettivamente arrivato da chi dice di essere stato mandato. Questo per evitare, ad esempio, attacchi [[Man in the Middle]].

Possono essere implementate in qualsiasi protocollo asimmetrico, ma vediamolo nel caso dell'[[RSA]]. 

>Nell'RSA abbiamo la chiave pubblica $e$ e la chiave privata $d = e^-1$. Per cifrare B fa $C = M^e \mod n$ e per decifrare A fa $M = C^d \mod n$

Il problema sorge quando A vuole rispondere a B, e vuole firmare il *nuovo* messaggio $H$ per identificarsi. La firma si ottiene come $F = H^d \mod n$

B, avendo la chiave pubblica $e$, può fare $H' = F^e \mod n$. 

Solo che non ha niente con cui confrontare $H'$. Infatti in realtà A invia $(H,F)$, B potrà poi vedere se $H = H'$, caso in cui si è verificata l'identità di A, visto che solo lui poteva avere $d$. 

Chiaramente A non può mandare il vero messaggio in chiaro, infatti questo $H$ inviato è in realtà l'[[prereq_2#Funzione trapdoor|hash]] del vero messaggio.
## Blinding attack
