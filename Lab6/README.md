# Laboratory No.5


### Objectives

#### T-SQL: Procedural Instructions

### Tasks

1. Add IF/ELSE statements to the code:

__SQL Querry:__

```sql
DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE INT;

SET @N1 = 60 * RAND();
SET @N2 = 60 * RAND();
SET @N3 = 60 * RAND();

IF (@N1 < @N2 AND @N2 > @N3)
  SET @MAI_MARE = @N2
ELSE IF (@N1 > @N2 AND @N1 > @N3)
  SET @MAI_MARE = @N1
ELSE
  SET @MAI_MARE = @N3

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(2)); 
```

  __Output:__
  
25

11

18

Mai mare = 25


2. Display the first 10 rows of the students' marks on the first evaluation on Databases course, except for marks as 6 and 8:



  __SQL Querry:__

```sql
DECLARE @DISC VARCHAR(20); DECLARE @EVAL VARCHAR(20);
SET @DISC = 'Baze de date'
SET @EVAL = 'Testul 1'
 
SELECT TOP 10 Nume_Student, Prenume_Student, Nota
FROM studenti_reusita R
INNER JOIN discipline D ON R.Id_Disciplina = D.Id_Disciplina
INNER JOIN studenti S ON R.Id_Student = S.Id_Student
WHERE Tip_Evaluare = @EVAL AND Disciplina = @DISC AND Nota = iif(Nota <> 6 AND Nota <> 8, Nota, NULL)
ORDER BY Nota DESC
```

 __Output:__
 
| Nume_Student|Prenume_Student|Nota| 
|-------------|-------------|----| 
| Ghiran |	Andrei	| 10
| Dascal	| Florina	| 10
|Orian	| Sergiu	| 10
|Paros	| Constantin |	10
|Ghiurea |	Stefan |	9
|Hanea |	Marius |	9
|Pop	 | Alexandru	 |9
|Dan	 | David	 | 9
|Danci	| Larisa |	9
|Diaconu	| Samuel	  | 9


3. Rewrite the 1st task using CASE:

  __SQL Querry:__

  ```sql
DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE INT;

SET @N1 = 60 * RAND();
SET @N2 = 60 * RAND();
SET @N3 = 60 * RAND();

SET @MAI_MARE = 
CASE
	WHEN (@N1 < @N2 AND @N2 > @N3)
		THEN @N2
	WHEN (@N1 > @N2 AND @N1 > @N3)
		THEN @N1
	ELSE @N3
END

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(2)); 
  ```

4. Rewrite the 1st task with try/catch blocks and RAISERROR:


  __SQL Querry:__

```sql
DECLARE @N1 FLOAT, @N2 FLOAT, @N3 FLOAT;
DECLARE @MAI_MARE FLOAT;

BEGIN TRY

SET @N1 = 60 / RAND() / 0;
SET @N2 = 60 / RAND();
SET @N3 = 60 / RAND();


IF (@N1 > @N2 AND @N1 > @N3)
	SET @MAI_MARE = @N1
ELSE IF (@N2 > @N1 AND @N2 > @N3)
	SET @MAI_MARE = @N2
ELSE IF (@N3 > @N1 AND @N3 > @N2)
	SET @MAI_MARE = @N3
ELSE RAISERROR('SOMETHING IS WRONG', 11, 1)

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(20));

END TRY
BEGIN CATCH
	PRINT 'ERROR: ' + CAST(ERROR_MESSAGE() AS VARCHAR(20))
END CATCH
```

__Output:__

ERROR: Divide by zero error
