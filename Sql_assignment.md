1. Sono date le relazioni seguenti (le chiavi primarie sono sottolineate):
AUTORE(CodAutore, Nome, Cognome, Dipartimento, Università)
ARTICOLO(CodArticolo, Titolo, Argomento)
AUTORI ARTICOLO(CodArticolo, CodAutore)
EDIZIONI CONFERENZA(Conferenza, Edizione, NomeEdizione, DataInizio, DataFine, Editore) 
AUTORE PRESENTA ARTICOLO(CodAutore, Data, OraInizio, OraFine, Sala, CodArticolo, Conferenza, Edizione)
Esprimere la seguente interrogazione in SQL

(a) Per ciascun autore che ha presentato solo articoli di argomento ’Data Mining’, visualizzare il codice,
il cognome, l’università di afferenza e il numero totale di articoli presentati dall’autore in ciascuna edizione di ogni conferenza.

```sql
SELECT AU.CodAutore, Cognome, Università, COUNT (DISTINCT CodArticolo)
FROM AUTORE AU, AUTORE_PRESENTA_ARTICOLO APA
WHERE AU.CodAutore NOT IN  (SELECT CodAutore
				          FROM AUTORI_ARTICOLO AA, ARTICOLO AR
				          WHERE AA.CodArticolo = AR.CodArticolo AND
                                 		                          Argomento <> ‘Data Mining’)
GROUP BY Edizione, Conferenza, AU.CodAutore, Cognome, Università;
```

2. Sono date le relazioni seguenti (le chiavi primarie sono sottolineate):
STUDENTE(MatricolaS, Nome, Cognome, Corso di Laurea)
HOMEWORK DA CONSEGNARE(CodHW, Titolo, Argomento, DataScadenzaPrevista) 
DOCENTE(CodDocente, Nome, Cognome, Dipartimento)
VALUTAZIONE HOMEWORK CONSEGNATI(MatricolaS, CodHW, CodDocente,
                                       DataConsegna, DataValutazione, Valutazione)
Esprimere la seguente interrogazione in SQL

(a) Per ogni studente che ha consegnato almeno 3 homework ricevendo in ciascuno una valutazione superiore a 4,
visualizzare il cognome dello studente, il numero totale di homework consegnati, la valutazione media ricevuta
e il numero di docenti diversi che hanno effettuato le valutazioni.

```sql
SELECT Cognome, COUNT (*), AVG (Valutazione), COUNT (DISTINCT CodD)
FROM STUDENTE S, VALUTAZIONE_HOMEWORK_CONSEGNATI VHC
WHERE VHC.MatricolaS = S.MatricolaS AND
	   S.MatricolaS IN (SELECT MatricolaS
			        FROM VALUTAZIONE_HOMEWORK_CONSEGNATI VHC2
			        WHERE Valutazione > 4
			        GROUP BY MatricolaS, CodHW
			        HAVING COUNT(*)>=3)
GROUP BY Cognome, VHC.MatricolaS;
```
3. Sono date le relazioni seguenti (le chiavi primarie sono sottolineate):
AUTORE(CodAutore, Nome, Cognome, Dipartimento, Universit`a)
ARTICOLO(CodArticolo, Titolo, Argomento)
AUTORI ARTICOLO(CodArticolo, CodAutore)
EDIZIONI CONFERENZA(Conferenza, Edizione, NomeEdizione, DataInizio, DataFine, Editore) 
AUTORE PRESENTA ARTICOLO(CodAutore, Data, OraInizio, OraFine, Sala,
                                       CodArticolo, Conferenza, Edizione)
Esprimere la seguente interrogazione in SQL

(a) Considerando le conferenze con almeno 10 edizioni, per ciascuna edizione della conferenza visualizzare il nome dell’edizione
e il codice dell’autore che ha presentato il maggior numero di articoli in quella edizione.

```sql
SELECT NomeEdizione, CodAutore
FROM EDIZIONI_CONFERENZA EC1, AUTORE_PRESENTA_ARTICOLO APA1
WHERE EC1.Conferenza IN (SELECT Conferenza
				 FROM EDIZIONI_CONFERENZA EC2
				 GROUP BY EC2.Conferenza, EC2.Edizione
				 HAVING COUNT(*) >= 10)
GROUP BY Edizione, CodAutore, Conferenza
HAVING COUNT (*) = (SELECT  MAX (NumArticoli)
			   FROM (SELECT COUNT(*) AS NumArticoli
				    FROM AUTORE_PRESENTA_ARTICOLO APA2
				    WHERE APA1.Conferenza = APA2.Conferenza AND
				                    APA1.Edizione = APA2.Edizione
				    GROUP BY APA1.CodArticolo, APA1.Conferenza, APA1.Edizione)  AS CONTEGGIO
			   GROUP BY CodAutore, Edizione, Conferenza);

```

4. Sono date le relazioni seguenti (le chiavi primarie sono sottolineate):
                 SEMINARIO(CodS, TitoloS, Argomento, Durata)
                 DOCENTE(CodD, NomeD, DataNascita)
                 CALENDARIO(CodS, DataInizio, OraInizio CodD, Aula)
                 COMPETENZE(CodD, Argomento)
Esprimere la seguente interrogazione in SQL

(a) Visualizzare il codice dei seminari per cui almeno una delle edizioni
a calendario è tenuta dal docente con il maggior numero di competenze.

```sql
SELECT (DISTINCT CodS) FROM CALENDARIO C1 WHERE CodD IN (SELECT CodD
6.
FROM CALENDARIO C2
GROUP BY CodD
HAVING COUNT(*) = (SELECT MAX(NUM_COMP)
FROM (SELECT COUNT(*) AS NUM_COMP FROM COMPETENZE CodD
GROUP BY CodD) AS COMP);
```

5. Sono date le relazioni seguenti (le chiavi primarie sono sottolineate):
      DOCENTE(MatrDoc, NomeDoc, Dipartimento, NomeGruppoRicerca)
      CORSO(CodCorso, NomeCorso, MatrDoc, NumStudentiIscritti, Area)
      AULA(CodAula, Piano, KitVideo, NumPosti)
      LEZIONE(CodAula, Data, OraInizio, OraFine, CodCorso, NumStudentiPresenti)
KitVideo = {si, no}
Esprimere la seguente interrogazione in SQL

(a) Per ciascun docente che ha tenuto solo corsi dell’area basi di dati,
visualizzare la matricola del docente e, tra i corsi che ha tenuto, 
il codice del corso con il più alto numero medio di studenti presenti alle lezioni del corso.

```sql
SELECT D.MatrDoc, CodCorso
FROM DOCENTE D, CORSO C1
WHERE D.MatrDoc NOT IN (SELECT MatrDoc
			           FROM CORSO C2
			           WHERE C2.Area <> ‘Basi di dati’) AND
	  D.MatrDoc = C1.MatrDoc AND
	  C1.CodCorso IN (SELECT CodCorso
			  FROM (SELECT MAX(Num_Stud), CodCorso
				   FROM (SELECT AVG(NumStudentiPresenti) AS Num_Stud, CodCorso
					    FROM LEZIONE L
					    GROUP BY CodCorso) AS MEDIE_CORSO) AS MAX_CORSO);
```

6. Sono date le relazioni seguenti (le chiavi primarie sono sottolineate):
STUDENTE(MatricolaS, Nome, Cognome, Corso di Laurea)
HOMEWORK DA CONSEGNARE(CodHW, Titolo, Argomento, DataScadenzaPrevista) 
DOCENTE(CodDocente, Nome, Cognome, Dipartimento)
VALUTAZIONE HOMEWORK CONSEGNATI(MatricolaS, CodHW, CodDocente,
                                       DataConsegna, DataValutazione, Valutazione)
Esprimere la seguente interrogazione in SQL

(a) Visualizzare la matricola, il cognome e il corso di laurea di ciascun studente che non ha mai consegnato un homework
in una data successiva alla data di scadenza prevista, e che ha consegnato tutti gli homework attesi
ricevendo in ciascuno di questi la valutazione piu` alta.

```sql
SELECT MatricolaS, Cognome, Corso_di_Laurea
FROM STUDENTE
WHERE MatricolaS NOT IN (SELECT MatricolaS
				   FROM VALUTAZIONE_HOMEWORK_CONSEGNATI, 
					   HOMEWORK_DA_CONSEGNARE
				   WHERE DataConsegna > DataScadenzaPrevista) AND
               MatricolaS IN (SELECT MatricolaS
			   FROM VALUTAZIONE_HOMEWORK_CONSEGNATI
			   WHERE Valutazione = (SELECT MAX(Valutazione)
FROM VALUTAZIONE_HOMEWORK_CONSEGNATI)
			   GROUP BY MatricolaS
			   HAVING COUNT(*) = (SELECT COUNT(*)
						     FROM HOMEWORK_DA_CONSEGNARE)) AND
	MatricolaS NOT IN (SELECT MatricolaS
			            FROM VALUTAZIONE_HOMEWORK_CONSEGNATI
			            WHERE Valutazione <> (SELECT MAX(Valutazione)
     FROM   VALUTAZIONE_HOMEWORK_CONSEGNATI));

```
