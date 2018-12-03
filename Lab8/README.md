# Laboratory Work No.7


### Objectives

#### Diagrams, Schemes and Synonyms

### Tasks

1. Create a standart diagram of the __universitatea__ database:

  __Result:__

  ![task1](https://user-images.githubusercontent.com/22482507/49022288-29e78b80-f19d-11e8-9f0d-e8a5e9929c9e.JPG)


2. Add constraints for _Sef_grupa_ and _Prof_Indrumar_:

  __Result:__

  ![task2](https://user-images.githubusercontent.com/22482507/49022286-29e78b80-f19d-11e8-942c-3e7725a275aa.JPG)


3. Add the table _orarul_ to the diagram:

  __Result:__

  ![task3](https://user-images.githubusercontent.com/22482507/49022290-2a802200-f19d-11e8-9907-cea4639e02fb.JPG)
  
  
4. Add secondary keys to the table _orarul_:

  __Result:__
  
  ![task4](https://user-images.githubusercontent.com/22482507/49022292-2a802200-f19d-11e8-9d67-faa39c635142.JPG)
  
  
5. Also define FK-PK constraints to the table _orarul_:

  __Result:__

  ![task5](https://user-images.githubusercontent.com/22482507/49022293-2a802200-f19d-11e8-8b25-e356d0fcba50.JPG)


6. Create 3 new schemas in the __universitatea__ database: _cadre_didactice_, _plan_studii_ and _studenti_. Transfer the table _profesori_ to the schema _cadre_didactice_, tables _orarul_, _discipline_ to the schema _plan_studii_, and the tables _studenti_, _studenti_reusita_ to the schema _studenti_:

  __SQL Querry:__

  ```sql
  ALTER SCHEMA cadre_didactice TRANSFER dbo.profesori
  ALTER SCHEMA plan_studii TRANSFER dbo.orarul
  ALTER SCHEMA plan_studii TRANSFER dbo.discipline
  ALTER SCHEMA studenti TRANSFER dbo.studenti
  ALTER SCHEMA studenti TRANSFER dbo.studenti_reusita
  ```
  
   __Result:__

  ![task6](https://user-images.githubusercontent.com/22482507/49023087-0faead00-f19f-11e8-8d9e-93e7a6e9ed1e.JPG)
  

7. Modify 3 querries from the 4th lab with renamed table names:

  __SQL Querry:__

  ```sql
  SELECT Disciplina
  FROM plan_studii.discipline
  WHERE LEN(Disciplina) > 20
  
  SELECT Nume_Student, Prenume_Student, COUNT(Nota) AS Note
  FROM studenti.studenti s FULL JOIN studenti.studenti_reusita r ON s.Id_Student = r.Id_Student
  GROUP BY Nume_Student, Prenume_Student
  ORDER BY Nume_Student
  
  SELECT Disciplina, AVG(Nota) AS Media
  FROM plan_studii.discipline d INNER JOIN studenti.studenti_reusita r ON d.Id_Disciplina = r.Id_Disciplina
  GROUP BY Disciplina HAVING AVG(Nota) > 7.0
  ORDER BY Disciplina
  ```

8. Create synonyms to simplify the previous querries:

__SQL Querry:__

  ```sql
  CREATE SYNONYM DISC FOR plan_studii.discipline
  CREATE SYNONYM STUD FOR studenti.studenti
  CREATE SYNONYM STUD_R FOR studenti.studenti_reusita
  
  SELECT Disciplina
  FROM DISC
  WHERE LEN(Disciplina) > 20
  
  SELECT Nume_Student, Prenume_Student, COUNT(Nota) AS Note
  FROM STUD s FULL JOIN STUD_R r ON s.Id_Student = r.Id_Student
  GROUP BY Nume_Student, Prenume_Student
  ORDER BY Nume_Student
  
  SELECT Disciplina, AVG(Nota) AS Media
  FROM DISC d INNER JOIN STUD_R r ON d.Id_Disciplina = r.Id_Disciplina
  GROUP BY Disciplina HAVING AVG(Nota) > 7.0
  ORDER BY Disciplina
  ```

  - The outputs can be seen [here](../Lab4).
