# Laboratory Work No.9


### Objectives

#### Procedures and Functions

### Tasks

1. Create 2 procedures based on previously created querries (in lab4):

  __SQL Querry:__
  
  ```sql
DROP PROCEDURE IF EXISTS ex1_1;
GO

CREATE PROCEDURE ex1_1
	@param INT
AS
	SELECT Disciplina
	FROM DISC
	WHERE LEN(Disciplina) > @param
GO

EXECUTE ex1_1 @param = 20;

DROP PROCEDURE IF EXISTS ex1_2;
GO

CREATE PROCEDURE ex1_2
	@mark FLOAT
AS
	SELECT Disciplina, AVG(Nota) AS Media
	FROM plan_studii.discipline d INNER JOIN studenti.studenti_reusita r
	ON d.Id_Disciplina = r.Id_Disciplina
	GROUP BY Disciplina HAVING AVG(Nota) > @mark
	ORDER BY Disciplina
GO

EXECUTE ex1_2 @mark = 7.0
  ```
  
  __Output:__

  - The outputs can be seen [here](../Lab4)

2. Create a procedure that doesn't have an input parameter, but has an output parameter. The ouput parameter should return the number of students that didn't pass at least one exam:

    __SQL Querry:__
  
  ```sql
DROP PROCEDURE IF EXISTS ex2;
GO

CREATE PROCEDURE ex2
AS
	RETURN
	    (SELECT COUNT(*)
		FROM studenti.studenti_reusita
		WHERE Nota < 5 OR Nota = NULL)
GO

DECLARE @badStudents INT
EXECUTE @badStudents = ex2
SELECT @badStudents as 'Bad Students'
  ```
  
  __Output:__

  |Bad Students|
  |------------|
  |    198     |


3. Create a procedure that would insert a new student into the database:

  __SQL Querry:__

  ```sql
DROP PROCEDURE IF EXISTS insertInfo
GO

CREATE PROCEDURE insertInfo
	@id INT,
	@name VARCHAR(50),
	@surname VARCHAR(50),
	@birthday DATE,
	@address VARCHAR(500),
	@GroupCode VARCHAR(6)
AS
	INSERT INTO studenti.studenti
	VALUES (@id, @name, @surname, @birthday, @address)
	INSERT INTO studenti.studenti_reusita
	VALUES (@id, 106, 104, @GroupCode, 'lul', NULL, NULL)
GO

EXECUTE insertInfo
		@id = 233,
		@name = 'Buratino2',
		@surname = 'Ghenadievici',
		@birthday = '02/02/1931',
		@address = 'Siberia',
		@GroupCode = 1
  ```
  
4. Create a procedure that would replace a professor in the _studenti_reusita_ table with another one:

  __SQL Querry:__
  
  ```sql
DROP PROCEDURE IF EXISTS ProfReassign
GO

CREATE PROCEDURE ProfReassign
	@oldName VARCHAR(60),
	@oldSurname VARCHAR(60),
	@newName VARCHAR(60),
	@newSurname VARCHAR(60),
	@discipline VARCHAR(255)
AS
	DECLARE @old_id INT, @new_id INT, @disc_id INT
	
	SET @old_id = (SELECT Id_Profesor
		FROM cadre_didactice.profesori
		WHERE Nume_Profesor = @oldName
		AND Prenume_Profesor = @oldSurname)
	
	SET @new_id = (SELECT Id_Profesor
		FROM cadre_didactice.profesori
		WHERE Nume_Profesor = @newName
		AND Prenume_Profesor = @newSurname)
	
	SET @disc_id = (SELECT Id_Disciplina
		FROM plan_studii.discipline
		WHERE Disciplina = @discipline)
	
	IF (@new_id = NULL OR @old_id = NULL OR @disc_id = NULL)
	BEGIN
		RAISERROR('Data not found.', 15, 1)
	END

	UPDATE studenti.studenti_reusita
	SET Id_Profesor = @new_id
	WHERE Id_Profesor = @old_id AND Id_Disciplina = @disc_id
GO

EXECUTE ProfReassign
		@oldName = 'Frent',
		@oldSurname = 'Tudor',
		@newName = 'Jugaru',
		@newSurname = 'George',
		@discipline = 'Cercetari operationale'
  ```
  
  
5. Create a procedure that would find the top 3 best students from a group (input parameter) and add 1p to their marks:

  ```sql
DROP PROCEDURE IF EXISTS Top3Discipline
GO

CREATE PROCEDURE Top3Discipline
	@discipline VARCHAR(255)
AS
	DECLARE @ret TABLE
		(Cod_Grupa VARCHAR(6),
		Nume_Prenume_Student VARCHAR(255),
		Disciplina VARCHAR(255),
		NotaVeche TINYINT,
		NotaNoua TINYINT,
		Id INT)

	INSERT INTO @ret
	SELECT DISTINCT TOP 3 Cod_Grupa, Nume_Student + ' ' + Prenume_Student, Disciplina, Nota, IIF(Nota = 10, 10, Nota + 1), S.Id_Student
	FROM studenti.studenti_reusita SR
	JOIN studenti.studenti S ON S.Id_Student = SR.Id_Student
	JOIN plan_studii.discipline D ON SR.Id_Disciplina = D.Id_Disciplina
	JOIN grupe G ON SR.Id_Grupa = G.Id_Grupa
	WHERE Disciplina = @discipline
	ORDER BY Nota DESC

	SELECT Cod_Grupa, Nume_Prenume_Student, Disciplina, NotaVeche, NotaNoua FROM @ret 

	UPDATE studenti.studenti_reusita
	SET Nota = Nota + 1 WHERE Id_Student IN (SELECT Id FROM @ret) AND Nota < 10

	RETURN SELECT Cod_Grupa, Nume_Prenume_Student, Disciplina, NotaVeche, NotaNoua
	FROM @ret
GO

EXECUTE Top3Discipline @discipline = 'Cercetari operationale'
  ```

  __Output:__
  
| Cod_Grupa | Nume_Prenume_Student | Disciplina              | NotaVeche | NotaNoua | 
|-----------|----------------------|-------------------------|-----------|----------| 
| CIB171    | Cosovanu Geanina     | Cercetari operationale  | 10        | 10       | 
| CIB171    | Damian Adina         | Cercetari operationale  | 10        | 10       | 
| CIB171    | Diaconu Samuel       | Cercetari operationale  | 10        | 10       | 
 
 
6. Create functions based on the 4th lab:

  __SQL Querry:__

  ```sql
DROP FUNCTION IF EXISTS ft_longerDiscNames
GO

CREATE FUNCTION ft_longerDiscNames(@nr INT)
RETURNS TABLE
AS
	RETURN (SELECT Disciplina
		FROM plan_studii.discipline
		WHERE LEN(Disciplina) > @nr)
GO

DROP FUNCTION IF EXISTS ft_HigherMark
GO

CREATE FUNCTION ft_HigherMark(@mark FLOAT)
RETURNS TABLE
AS
	RETURN (SELECT Disciplina, AVG(Nota) AS Media
		FROM plan_studii.discipline d INNER JOIN studenti.studenti_reusita r
		ON d.Id_Disciplina = r.Id_Disciplina
		GROUP BY Disciplina HAVING AVG(Nota) > @mark)
GO

SELECT * FROM ft_longerDiscNames(20)
SELECT * FROM ft_HigherMark(7.0)

  ```
  
   __Output:__

  - As in the 1st task.
  
7. Create a function that would compute the age of a student:

  __SQL Querry:__

  ```sql
DROP FUNCTION IF EXISTS ft_age
GO

CREATE FUNCTION ft_age(@bd DATE)
RETURNS INT
BEGIN
	RETURN DATEDIFF(YEAR, @bd, GETDATE())
END
GO

SELECT Nume_Student, Prenume_Student, dbo.ft_age(Data_Nastere_Student) as Virsta
FROM studenti.studenti
  ```
  
  __Output:__
  
| Nume_Student | Prenume_Student | Virsta | 
|--------------|-----------------|--------| 
| Brasovianu   | Teodora         | 18     | 
| Cosovanu     | Geanina         | 18     | 
| Coste        | Claudia         | 19     | 
| Damian       | Roxana          | 18     | 
| Damian       | Adina           | 18     | 
| Dan          | David           | 20     | 
| Danci        | Larisa          | 19     | 
| Diaconu      | Samuel          | 18     | 
| Demian       | Bogdan          | 18     | 
| Dobrea       | Daniela         | 19     | 
| Dobrita      | Maria           | 18     | 
| Dobrovat     | Mihai           | 18     | 
| Dordai       | Ovidiu          | 21     | 
| Ene          | Mihai           | 18     | 
| Farcas       | Alina           | 18     | 
| Forgaci      | Mihai           | 21     | 
| Fratila      | Ovidiu          | 18     | 
| Gadalean     | Gabriela        | 17     | 
| Gheorghescu  | Gabriel         | 17     | 
| Ghimpu       | Eduard          | 18     | 
| Ghiran       | Andrei          | 19     | 
| Ghiurea      | Stefan          | 18     | 
| Giurca       | Sebastian       | 18     | 
| Goia         | Ariana          | 18     | 
| Hanea        | Marius          | 20     | 
| Holhos       | Bogdan          | 18     | 
| Corovet      | Eduard          | 18     | 
| Luca         | Alex            | 18     | 
| Mates        | Catalin         | 18     | 
| Dascal       | Florina         | 19     | 
| Florea       | Ioan            | 18     | 
| Galambosi    | Norbert         | 18     | 
| Jinga        | Cristian        | 18     | 
| Lincar       | Alexandra       | 18     | 
| Muresan      | Sergiu          | 18     | 
| Nicola       | Claudiu         | 18     | 
| Nicolae      | Radu            | 18     | 
| Nicolescu    | Aurel           | 19     | 
| Oncioiu      | Costin-Ilie     | 19     | 
| Oniga        | Bogdan          | 18     | 
| Orian        | Sergiu          | 18     | 
| Paros        | Constantin      | 18     | 
| Petok        | Lorand          | 19     | 
| Pintea       | Andrei          | 18     | 
| Pitigoi      | Valentina       | 18     | 
| Poienar      | Robert          | 18     | 
| Popov        | Andrei          | 18     | 
| Pop          | Alexandru       | 18     | 
| Pop          | Irina           | 20     | 
| Popa         | Mihaela         | 18     | 
| Suciu        | Ionut           | 18     | 
| Timu         | Andrei          | 17     | 
| Vacareanu    | Stefan          | 18     | 
| Vaman        | Mihai           | 18     | 
| Varga        | Izabella        | 18     | 
| Viman        | Viorel          | 18     | 
| Irimia       | Cristiana       | 24     | 
| Irimus       | Andrei          | 19     | 
| Judea        | Stefana         | 18     | 
| Jurj         | Flaviu          | 18     | 
| Covas        | Roman           | 18     | 
| Letea        | Roland          | 18     | 
| Luca         | Laura           | 18     | 
| Lucaciu      | Raul            | 18     | 
| Lucaciu      | Alexandru       | 19     | 
| Lucasu       | Victor          | 18     | 
| Marcu        | Daniel          | 18     | 
| Marin        | Stefan          | 18     | 
| Martis       | Dan             | 20     | 
| Matko        | Mihai           | 19     | 
| Maxim        | Tudor           | 18     | 
| Matasari     | Alexandru       | 18     | 
| Mazareanu    | Sergiu          | 23     | 
| Medrea       | Diana           | 18     | 
| Mesesan      | Maria           | 18     | 
| Buratino     | Ghenadievici    | 87     | 
| Buratino2    | Ghenadievici    | 87     | 


8. Create a function that would return a student's data regarding his marks:


  __SQL Querry:__

  ```sql
DROP FUNCTION IF EXISTS ft_studentData
GO

CREATE FUNCTION ft_studentData(@name VARCHAR(50))
RETURNS TABLE
AS
	RETURN (SELECT @name Nume_Prenume_Student, Disciplina, Nota, Data_Evaluare
			FROM studenti.studenti S
			JOIN studenti.studenti_reusita SR ON S.Id_Student = SR.Id_Student
			JOIN plan_studii.discipline D ON SR.Id_Disciplina = D.Id_Disciplina
			WHERE @name = Nume_Student + ' ' + Prenume_Student)
GO

SELECT * FROM ft_studentData('Dan David')
  ```
  
 __Output:__
 
| Nume_Prenume_Student | Disciplina                         | Nota | Data_Evaluare | 
|----------------------|------------------------------------|------|---------------| 
| Dan David            | Cercetari operationale             | 8    | 2018-01-25    | 
| Dan David            | Cercetari operationale             | 8    | 2018-12-03    | 
| Dan David            | Cercetari operationale             | 5    | 2017-10-06    | 
| Dan David            | Cercetari operationale             | 9    | 2017-12-09    | 
| Dan David            | Baze de date                       | 8    | 2018-01-15    | 
| Dan David            | Baze de date                       | 8    | 2018-12-03    | 
| Dan David            | Baze de date                       | 9    | 2017-10-10    | 
| Dan David            | Baze de date                       | 9    | 2017-12-15    | 
| Dan David            | Structuri de date si algoritmi     | 3    | 2018-01-09    | 
| Dan David            | Structuri de date si algoritmi     | 3    | 2018-12-03    | 
| Dan David            | Structuri de date si algoritmi     | 5    | 2017-10-12    | 
| Dan David            | Structuri de date si algoritmi     | 5    | 2017-12-12    | 
| Dan David            | Matematica discreta                | 3    | 2018-01-25    | 
| Dan David            | Matematica discreta                | 3    | 2018-12-04    | 
| Dan David            | Matematica discreta                | 6    | 2017-10-03    | 
| Dan David            | Matematica discreta                | 9    | 2017-12-14    | 
| Dan David            | Modelarea sistemelor               | 10   | 2018-01-13    | 
| Dan David            | Modelarea sistemelor               | 10   | 2018-12-03    | 
| Dan David            | Modelarea sistemelor               | 10   | 2017-10-06    | 
| Dan David            | Modelarea sistemelor               | 10   | 2017-12-14    | 
| Dan David            | Programarea aplicatiilor Windows   | 5    | 2018-01-16    | 
| Dan David            | Programarea aplicatiilor Windows   | 5    | 2018-12-04    | 
| Dan David            | Programarea aplicatiilor Windows   | 10   | 2017-10-09    | 
| Dan David            | Programarea aplicatiilor Windows   | 7    | 2017-12-08    | 
| Dan David            | Proiectarea sistemelor informatice | 7    | 2018-01-15    | 
| Dan David            | Proiectarea sistemelor informatice | 7    | 2018-12-03    | 
| Dan David            | Proiectarea sistemelor informatice | 9    | 2017-10-13    | 
| Dan David            | Proiectarea sistemelor informatice | 7    | 2017-12-12    | 
| Dan David            | Practica de licenta                | 6    | 2018-01-25    | 
| Dan David            | Practica de licenta                | 6    | 2018-12-05    | 
| Dan David            | Practica de licenta                | 7    | 2017-10-04    | 
| Dan David            | Practica de licenta                | 9    | 2017-12-11    | 
| Dan David            | Practica de productie              | 5    | 2018-01-25    | 
| Dan David            | Practica de productie              | 5    | 2018-12-04    | 
| Dan David            | Practica de productie              | 10   | 2017-10-02    | 
| Dan David            | Practica de productie              | 6    | 2017-12-11    | 
| Dan David            | Integrare informationala europeana | 10   | 2018-01-14    | 
| Dan David            | Integrare informationala europeana | 10   | 2018-12-03    | 
| Dan David            | Integrare informationala europeana | 9    | 2017-10-04    | 
| Dan David            | Integrare informationala europeana | 7    | 2017-12-13    | 


9. Create a function that would find the best or the worst student in a group, depending on the value of the input variable __is_good__ which can be either 'sarguincios' or 'slab'. The function should return a table:


  
  __SQL Querry:__

  ```sql
DROP FUNCTION IF EXISTS ft_good_or_bad
GO

CREATE FUNCTION ft_good_or_bad(@Cod_Grupa VARCHAR(6), @is_good VARCHAR(20))
RETURNS @ret TABLE
	(Grupa VARCHAR(6),
	Nume_Prenume_Student VARCHAR(50),
	Nota_Medie DECIMAL(4, 2),
	is_good VARCHAR(20))
AS
BEGIN
	DECLARE @hack INT
	SELECT @hack = CASE @is_good WHEN 'sarguincios' THEN -1 WHEN 'slab' THEN 1 END
	
	INSERT INTO @ret
	SELECT TOP 1 @Cod_Grupa, Nume_Student + ' ' + Prenume_Student, AVG(CAST(Nota AS DECIMAL(4, 2))), @is_good
	FROM studenti.studenti S
	JOIN studenti.studenti_reusita SR
	ON S.Id_Student = SR.Id_Student
	JOIN grupe G
	ON SR.Id_Grupa = G.Id_Grupa
	WHERE Cod_Grupa = @Cod_Grupa AND Nota IS NOT NULL
	GROUP BY Nume_Student, Prenume_Student
	ORDER BY AVG(CAST(Nota AS DECIMAL(4, 2))) * @hack
	RETURN
END
GO

SELECT * FROM ft_good_or_bad('INF171', 'sarguincios')
SELECT * FROM ft_good_or_bad('INF171', 'slab')
  ```

 __Output:__
 
| Grupa  | Nume_Prenume_Student | Nota_Medie | is_good     | 
|--------|----------------------|------------|-------------| 
| INF171 | Timu Andrei          | 8.64       | sarguincios | 


| Grupa  | Nume_Prenume_Student | Nota_Medie | is_good | 
|--------|----------------------|------------|---------| 
| INF171 | Suciu Ionut          | 7.33       | slab    | 

