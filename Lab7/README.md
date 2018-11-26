# Laboratory No.6


### Objectives

#### Creation of tables and indexes

### Tasks

1. Write a T-SQL instruction to fill the __Adresa_Postala_profesor__ from the table __profesori__ with the value _mun.Chisinau_ if the address is null:

__SQL Querry:__

```sql
UPDATE profesori
SET Adresa_Postala_Profesor = 'mun.Chisinau'
WHERE Adresa_Postala_Profesor IS NULL;
```

  __Output:__
    
| Id_Profesor | Nume_Profesor | Prenume_Profesor | Adresa_Postala_Profesor                                      | 
|-------------|---------------|------------------|--------------------------------------------------------------|
| 100         | Popescu       | Gabriel          | mun. Chisinau                                                | 
| 101         | Micu          | Elena            | mun. Chisinau, bd. Dacia, 44                                 | 
| 102         | Avram         | Sanda            | mun. Chisinau, or. Vadul lui Voda, str. Stefan cel Mare, 2/2 | 
| 103         | Munteanu      | Alexandru        | mun. Chisinau, bd. Decebal, 91                               | 
| 104         | Mocanu        | Diana            | mun. Chisinau, str. Titulescu, 47                            | 
| 105         | Ilascu        | Ionela           | mun. Chisinau, s. Stauceni, str. Mateevici, 9                | 
| 106         | Prodan        | Cristian         | mun. Chisinau, bd. Alba Iulia, 198                           | 
| 107         | Ungureanu     | Iulia            | mun. Chisinau, str. N. Costin, 44/1                          | 
| 108         | Bivol         | Ion              | mun.Chisinau                                                 | 
| 109         | Dogar         | Alexandru        | mun.Chisinau                                                 | 
| 110         | Frent         | Tudor            | mun.Chisinau                                                 | 
| 111         | Ilie          | Vasile           | mun.Chisinau                                                 | 
| 112         | Jugaru        | George           | mun.Chisinau                                                 | 
| 113         | Micu          | George           | mun.Chisinau                                                 | 
| 114         | Mihalache     | Mihai            | mun.Chisinau                                                 | 
| 115         | Mihaliuc      | Ioana            | mun.Chisinau                                                 | 
| 116         | Mircea        | Sorin            | mun.Chisinau                                                 | 
| 117         | Mircea        | Maria            | mun.Chisinau                                                 | 
| 118         | Misan         | Andrei           | mun.Chisinau                                                 | 
| 119         | Moisuc        | Nicoleta         | mun.Chisinau                                                 | 
| 120         | Moldovan      | Alexandru        | mun.Chisinau                                                 | 
| 121         | Moldovanu     | Tudor            | mun.Chisinau                                                 | 
| 122         | Morariu       | Simion           | mun.Chisinau                                                 | 
| 123         | Muresan       | Mihaela          | mun.Chisinau                                                 | 
| 124         | Murg          | Daniel           | mun.Chisinau                                                 | 
| 125         | Nagy          | Alexandru        | mun.Chisinau                                                 | 
| 126         | Nazarie       | Alexandru        | mun.Chisinau                                                 | 
| 127         | Neamt         | Ioan             | mun.Chisinau                                                 | 
| 128         | Nechita       | Vasile           | mun.Chisinau                                                 | 
| 129         | Negru         | Sergiu           | mun.Chisinau                                                 | 
| 130         | Obreja        | Elena            | mun.Chisinau                                                 | 
| 131         | Olteanu       | Andrei           | mun.Chisinau                                                 | 
| 132         | Vieriu        | Denis            | mun.Chisinau                                                 | 



2. Modify the scheme of the table __grupe__, so that the field _Cod_Grupa_ has unique not null values:


  __SQL Querry:__

```sql
ALTER TABLE grupe
ADD UNIQUE(Cod_Grupa);
```



3. Add to the table __grupe__ 2 new columns - _Sef_Grupa_ and _Prof_Indrumator_:

  __SQL Querry:__

  ```sql
--this code should be run at least 3 times
ALTER TABLE grupe
ADD Sef_grupa INT, Prof_Indrumator INT;

UPDATE grupe
SET Sef_grupa =	(	SELECT TOP 1 Id_Student
					FROM studenti_reusita R
					WHERE grupe.Id_Grupa = R.Id_Grupa
					GROUP BY Id_Student
					ORDER BY AVG(CONVERT(FLOAT, Nota)) DESC)

UPDATE grupe
SET Prof_Indrumator = (	SELECT TOP 1 Id_Profesor
						FROM studenti_reusita R
						WHERE grupe.Id_Grupa = R.Id_Grupa
						GROUP BY Id_Profesor
						ORDER BY COUNT(Id_Disciplina) DESC, Id_Profesor DESC)
  ```

  __Output:__
    
| Id_Grupa | Cod_Grupa | Specialitate              | Nume_Facultate             | Sef_grupa | Prof_Indrumator | 
|----------|-----------|---------------------------|----------------------------|-----------|-----------------| 
| 1        | CIB171    | Cibernetica               | Informatica si Cibernetica | 118       | 101             | 
| 2        | INF171    | Informatica               | Informatica si Cibernetica | 151       | 104             | 
| 3        | TI171     | Tehnologii Informationale | Informatica si Cibernetica | 127       | 100             | 


4. Write a T-SQL querry that would add an additional point to the evaluation marks of the group chiefs:


  __SQL Querry:__

```sql
UPDATE studenti_reusita 
SET Nota += 1 WHERE Nota != 10 AND Id_Student IN (SELECT Sef_grupa FROM grupe)
```

__Output:__

_I've run this querry so many times, every group chief has 10 now_


5. Create a new table _profesori_new_ (as described in the book):

  __SQL Querry:__

```sql
CREATE TABLE profesori_new (
	Id_Profesor INT,
	Nume_Profesor VARCHAR(60) NOT NULL,
	Prenume_Profesor VARCHAR(60) NOT NULL,
	Localitate VARCHAR(255) DEFAULT('mun.Chisinau'),
	Adresa_1 VARCHAR(255),
	Adresa_2 VARCHAR(255),
	PRIMARY KEY CLUSTERED (Id_Profesor))

INSERT INTO profesori_new(Id_Profesor, Nume_Profesor, Prenume_Profesor, Localitate, Adresa_1, Adresa_2)
SELECT	Id_Profesor,
		Nume_Profesor,
		Prenume_Profesor,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, 1, CHARINDEX(', str.', Adresa_Postala_Profesor) - 1)
			WHEN CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, 1, CHARINDEX(', bd.', Adresa_Postala_Profesor) - 1)
			ELSE SUBSTRING(Adresa_Postala_Profesor, 1, LEN(Adresa_Postala_Profesor))
		END Localitate,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(	Adresa_Postala_Profesor,
								CHARINDEX(', str.', Adresa_Postala_Profesor) + 2,
								CHARINDEX(',', SUBSTRING(	Adresa_Postala_Profesor,
															CHARINDEX(', str.', Adresa_Postala_Profesor) + 2, 99)) - 1)
			WHEN CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(	Adresa_Postala_Profesor,
								CHARINDEX(', bd.', Adresa_Postala_Profesor) + 2,
								CHARINDEX(',', SUBSTRING(	Adresa_Postala_Profesor,
															CHARINDEX(', bd.', Adresa_Postala_Profesor) + 2, 99)) - 1)
		END Adresa_1,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0 OR CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, PATINDEX('%[0-9]%', Adresa_Postala_Profesor), 10)
		END Adresa_2
FROM profesori
```

__Output:__

| Id_Profesor | Nume_Profesor | Prenume_Profesor | Localitate                        | Adresa_1             | Adresa_2 | 
|-------------|---------------|------------------|-----------------------------------|----------------------|----------| 
| 100         | Popescu       | Gabriel          | mun. Chisinau                     | NULL                 | NULL     | 
| 101         | Micu          | Elena            | mun. Chisinau                     | bd. Dacia            | 44       | 
| 102         | Avram         | Sanda            | mun. Chisinau, or. Vadul lui Voda | str. Stefan cel Mare | 2/2      | 
| 103         | Munteanu      | Alexandru        | mun. Chisinau                     | bd. Decebal          | 91       | 
| 104         | Mocanu        | Diana            | mun. Chisinau                     | str. Titulescu       | 47       | 
| 105         | Ilascu        | Ionela           | mun. Chisinau, s. Stauceni        | str. Mateevici       | 9        | 
| 106         | Prodan        | Cristian         | mun. Chisinau                     | bd. Alba Iulia       | 198      | 
| 107         | Ungureanu     | Iulia            | mun. Chisinau                     | str. N. Costin       | 44/1     | 
| 108         | Bivol         | Ion              | mun.Chisinau                      | NULL                 | NULL     | 
| 109         | Dogar         | Alexandru        | mun.Chisinau                      | NULL                 | NULL     | 
| 110         | Frent         | Tudor            | mun.Chisinau                      | NULL                 | NULL     | 
| 111         | Ilie          | Vasile           | mun.Chisinau                      | NULL                 | NULL     | 
| 112         | Jugaru        | George           | mun.Chisinau                      | NULL                 | NULL     | 
| 113         | Micu          | George           | mun.Chisinau                      | NULL                 | NULL     | 
| 114         | Mihalache     | Mihai            | mun.Chisinau                      | NULL                 | NULL     | 
| 115         | Mihaliuc      | Ioana            | mun.Chisinau                      | NULL                 | NULL     | 
| 116         | Mircea        | Sorin            | mun.Chisinau                      | NULL                 | NULL     | 
| 117         | Mircea        | Maria            | mun.Chisinau                      | NULL                 | NULL     | 
| 118         | Misan         | Andrei           | mun.Chisinau                      | NULL                 | NULL     | 
| 119         | Moisuc        | Nicoleta         | mun.Chisinau                      | NULL                 | NULL     | 
| 120         | Moldovan      | Alexandru        | mun.Chisinau                      | NULL                 | NULL     | 
| 121         | Moldovanu     | Tudor            | mun.Chisinau                      | NULL                 | NULL     | 
| 122         | Morariu       | Simion           | mun.Chisinau                      | NULL                 | NULL     | 
| 123         | Muresan       | Mihaela          | mun.Chisinau                      | NULL                 | NULL     | 
| 124         | Murg          | Daniel           | mun.Chisinau                      | NULL                 | NULL     | 
| 125         | Nagy          | Alexandru        | mun.Chisinau                      | NULL                 | NULL     | 
| 126         | Nazarie       | Alexandru        | mun.Chisinau                      | NULL                 | NULL     | 
| 127         | Neamt         | Ioan             | mun.Chisinau                      | NULL                 | NULL     | 
| 128         | Nechita       | Vasile           | mun.Chisinau                      | NULL                 | NULL     | 
| 129         | Negru         | Sergiu           | mun.Chisinau                      | NULL                 | NULL     | 
| 130         | Obreja        | Elena            | mun.Chisinau                      | NULL                 | NULL     | 
| 131         | Olteanu       | Andrei           | mun.Chisinau                      | NULL                 | NULL     | 
| 132         | Vieriu        | Denis            | mun.Chisinau                      | NULL                 | NULL     | 


6. Insert the following (as in the book) data in the table __orarul__:

__SQL Querry:__

```sql
CREATE TABLE orarul(
	Id_Grupa INT,
	Id_Disciplina INT,
	Id_Profesor INT,
	Ora VARCHAR(5),
	Auditoriu INT,
	Bloc CHAR,
	Zi VARCHAR(10),
	PRIMARY KEY (Id_Grupa, Id_Disciplina, Id_Profesor)
);

INSERT INTO orarul(Id_Grupa, Id_Disciplina, Id_Profesor, Ora, Auditoriu, Bloc, Zi)
VALUES	(1, 107, 101, '08:00', 202, 'B', 'Luni'),
		(1, 108, 101, '11:30', 501, 'B', 'Luni'),
		(1, 119, 117, '13:00', 501, 'B', 'Luni');
```

7. Also insert (using select) this data in the same table __orarul__:

__SQL Querry:__

```sql
INSERT INTO  orarul(Id_Grupa, Id_Disciplina, Id_Profesor, Ora, Auditoriu, Bloc, Zi)
VALUES (	
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Structuri de date si algoritmi'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Bivol' AND Prenume_Profesor = 'Ion'),
			'08:00', 101, 'B', 'Luni'
		),
		(
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Programe aplicative'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Mircea' AND Prenume_Profesor = 'Sorin'),
			'11:30', 101, 'B', 'Luni'
		),
		(
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Baze de date'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Micu' AND Prenume_Profesor = 'Elena'),
			'13:00', 101, 'B', 'Luni'
		)
```

__Output:__

| Id_Grupa | Id_Disciplina | Id_Profesor | Ora   | Auditoriu | Bloc | Zi   | 
|----------|---------------|-------------|-------|-----------|------|------| 
| 1        | 107           | 101         | 08:00 | 202       | B    | Luni | 
| 1        | 108           | 101         | 11:30 | 501       | B    | Luni | 
| 1        | 119           | 117         | 13:00 | 501       | B    | Luni | 
| 2        | 107           | 101         | 13:00 | 101       | B    | Luni | 
| 2        | 108           | 108         | 08:00 | 101       | B    | Luni | 
| 2        | 120           | 116         | 11:30 | 101       | B    | Luni | 
