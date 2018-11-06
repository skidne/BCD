# Laboratory No.4


### Objectives

#### Query editor for T-SQL and PL/SQL:

### Tasks

1. (4) Display which Courses (_Discipline_) have names longer than 20 characters:

__SQL Querry:__

```sql
SELECT Disciplina
FROM discipline
WHERE LEN(Disciplina) > 20
```

  __Output:__
  
| NR | Disciplina                                 | 
|----|--------------------------------------------|
| 1  | Programarea calculatoarelor                | 
| 2  | Asamblare si depanare PC                   | 
| 3  | Cercetari operationale                     | 
| 4  | Structuri de date si algoritmi             | 
| 5  | Limbaje evaluate de programare (Java,.NET) | 
| 6  | Programarea aplicatiilor Windows           | 
| 7  | Tehnologii de procesare a informatiei      | 
| 8  | Programarea declarativa                    | 
| 9  | Proiectarea sistemelor informatice         | 
| 10 | Practica de productie                      | 
| 11 | Integrare informationala europeana         | 

2. (21) How many marks (_Note_) does each student have? Display their names and surnames:

  __SQL Querry:__

```sql
SELECT Nume_Student, Prenume_Student, COUNT(Nota) AS Note
FROM studenti S LEFT JOIN studenti_reusita R ON S.Id_Student = R.Id_Student
GROUP BY Nume_Student, Prenume_Student
ORDER BY Nume_Student
```

 __Output:__
 
| Nume_Student|Prenume_Student|Note| 
|-------------|-------------|----| 
| Brasovianu  | Teodora     | 40 | 
| Corovet     | Eduard      | 52 | 
| Cosovanu    | Geanina     | 40 | 
| Coste       | Claudia     | 40 | 
| Covas       | Roman       | 0  | 
| Damian      | Adina       | 40 | 
| Damian      | Roxana      | 40 | 
| Dan         | David       | 40 | 
| Danci       | Larisa      | 40 | 
| Dascal      | Florina     | 52 | 
| Demian      | Bogdan      | 40 | 
| Diaconu     | Samuel      | 38 | 
| Dobrea      | Daniela     | 40 | 
| Dobrita     | Maria       | 40 | 
| Dobrovat    | Mihai       | 40 | 
| Dordai      | Ovidiu      | 40 | 
| Ene         | Mihai       | 40 | 
| Farcas      | Alina       | 40 | 
| Florea      | Ioan        | 52 | 
| Forgaci     | Mihai       | 40 | 
| Fratila     | Ovidiu      | 40 | 
| Gadalean    | Gabriela    | 40 | 
| Galambosi   | Norbert     | 50 | 
| Gheorghescu | Gabriel     | 40 | 
| Ghimpu      | Eduard      | 40 | 
| Ghiran      | Andrei      | 40 | 
| Ghiurea     | Stefan      | 40 | 
| Giurca      | Sebastian   | 40 | 
| Goia        | Ariana      | 40 | 
| Hanea       | Marius      | 40 | 
| Holhos      | Bogdan      | 52 | 
| Irimia      | Cristiana   | 0  | 
| Irimus      | Andrei      | 0  | 
| Jinga       | Cristian    | 52 | 
| Judea       | Stefana     | 0  | 
| Jurj        | Flaviu      | 0  | 
| Letea       | Roland      | 0  | 
| Lincar      | Alexandra   | 52 | 
| Luca        | Alex        | 52 | 
| Luca        | Laura       | 0  | 
| Lucaciu     | Alexandru   | 0  | 
| Lucaciu     | Raul        | 0  | 
| Lucasu      | Victor      | 0  | 
| Marcu       | Daniel      | 0  | 
| Marin       | Stefan      | 0  | 
| Martis      | Dan         | 0  | 
| Matasari    | Alexandru   | 0  | 
| Mates       | Catalin     | 52 | 
| Matko       | Mihai       | 0  | 
| Maxim       | Tudor       | 0  | 
| Mazareanu   | Sergiu      | 0  | 
| Medrea      | Diana       | 0  | 
| Mesesan     | Maria       | 0  | 
| Muresan     | Sergiu      | 52 | 
| Nicola      | Claudiu     | 52 | 
| Nicolae     | Radu        | 52 | 
| Nicolescu   | Aurel       | 52 | 
| Oncioiu     | Costin-Ilie | 52 | 
| Oniga       | Bogdan      | 52 | 
| Orian       | Sergiu      | 52 | 
| Paros       | Constantin  | 52 | 
| Petok       | Lorand      | 52 | 
| Pintea      | Andrei      | 52 | 
| Pitigoi     | Valentina   | 52 | 
| Poienar     | Robert      | 52 | 
| Pop         | Alexandru   | 52 | 
| Pop         | Irina       | 52 | 
| Popa        | Mihaela     | 52 | 
| Popov       | Andrei      | 52 | 
| Suciu       | Ionut       | 52 | 
| Timu        | Andrei      | 14 | 
| Vacareanu   | Stefan      | 0  | 
| Vaman       | Mihai       | 0  | 
| Varga       | Izabella    | 0  | 
| Viman       | Viorel      | 0  | 

3. (35) Find the names of the Courses (_Discipline_) and their average marks. Display the courses with an average mark bigger than 7.0:

  __SQL Querry:__

  ```sql
  SELECT Disciplina, AVG(Nota) AS Media
  FROM discipline D INNER JOIN studenti_reusita R ON D.Id_Disciplina = R.Id_Disciplina
  GROUP BY Disciplina HAVING AVG(Nota) > 7.0
  ORDER BY Disciplina
  ```

  __Output:__
  
| Disciplina             | Media | 
|------------------------|-------|
|Programarea declarativa | 8.0   | 
