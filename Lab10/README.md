# Laboratory Work No.10


### Objectives

#### Creating and using Triggers

### Tasks

1. Modify the _inregistrare_noua_ trigger so that on update a message would pop:

  __SQL Querry:__
  
  ```sql
CREATE TRIGGER inregistrare_noua ON plan_studii.orarul
AFTER UPDATE
AS
	IF (UPDATE(Auditoriu))
	BEGIN
		SELECT 'Schimbare in orar: ' + Cod_Grupa + ' , ' + Zi + ' ora ' +
			CAST(Ora AS VARCHAR(10)) + ' disciplina ' + Disciplina +
			' este mutata din blocul ' + Bloc + ' auditoriul ' +
			CAST(Auditoriu AS VARCHAR(10)) + ' in blocul ' +
			I.Bloc + ' auditoriul ' + CAST(I.Auditoriu AS VARCHAR(10)) + '.'
		FROM inserted I
		JOIN grupe G ON G.Id_Grupa = I.Id_Grupa
		JOIN discipline D ON D.Id_Disciplina = I.Id_Disciplina
	END
GO

SELECT * FROM plan_studii.orarul
UPDATE plan_studii.orarul SET Auditoriu = 202 WHERE Auditoriu = 210
SELECT * FROM plan_studii.orarul

  ```
  
  __Result:__

  ![task1](https://user-images.githubusercontent.com/22482507/49751224-91312f80-fcb5-11e8-980e-7da8fca254b3.JPG)

2. Create a trigger that would assure a consecutive insert into the tables _studenti_ and _studenti_reusita_:

    __SQL Querry:__
  
  ```sql
CREATE OR ALTER TRIGGER insert_students ON studenti.studenti
INSTEAD OF INSERT
AS
BEGIN
	DECLARE @ID_STUDENT INT = (SELECT Id_Student FROM inserted)
	INSERT INTO studenti.studenti SELECT * FROM inserted
	INSERT INTO studenti.studenti_reusita VALUES (@ID_STUDENT, 101, 101, 1, 'dummy', 10, GETDATE())
END

INSERT INTO studenti.studenti VALUES (190, 'Dodon', 'Igor', GETDATE(), 'Valea Perjii')

SELECT * FROM STUD
SELECT * FROM STUD_R
  ```
  
  __Result:__

![task2](https://user-images.githubusercontent.com/22482507/49751225-91312f80-fcb5-11e8-8680-6c0eb16ce373.JPG)

3. Create a trigger that would prohibit inserting smaller marks and modifying _Data_Evaluare_ data if it's not null in table _studenti_reusita_. The trigger should only be triggered when the affected data belongs to students from _CIB171_ group:

  __SQL Querry:__

  ```sql
CREATE OR ALTER TRIGGER cib171_trigger ON studenti.studenti_reusita
FOR UPDATE
AS
BEGIN
	IF EXISTS (SELECT TOP 1 I.Nota FROM inserted I INNER JOIN grupe G
		ON I.Id_Grupa = G.Id_Grupa INNER JOIN deleted D
		ON I.Id_Student = D.Id_Student
		WHERE Cod_Grupa = 'CIB171'
		AND (I.Nota < D.Nota OR D.Data_Evaluare IS NOT NULL))
	BEGIN
		PRINT('Nuh-uh! Stop right there, young man!')
		ROLLBACK TRANSEVENT
	END
END

UPDATE studenti.studenti_reusita
SET Nota = Nota - 2
WHERE Id_Grupa = (SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'CIB171')
AND Nota > 5
  ```
  
  __Result:__
  
  ![task3](https://user-images.githubusercontent.com/22482507/49751226-91312f80-fcb5-11e8-85a8-d5ca9b893902.JPG)
  
4. Create a DDL trigger that prohibits modifying the column _Id_Disciplina_:

  __SQL Querry:__
  
  ```sql
CREATE OR ALTER TRIGGER discipline_trigger ON DATABASE
FOR ALTER_TABLE
AS
BEGIN
	DECLARE @COL VARCHAR(50) = EVENTDATA().value('(/EVENT_INSTANCE/AlterTableEVENTList/Alter/Columns/Name)[1]', 'VARCHAR(50)');

	IF @COL = 'Id_Disciplina'
	BEGIN
		PRINT('ALTER TABLE??? You mad?! This database was here before your grandmother has even been born.')
		ROLLBACK TRANSEVENT
	END
END
  ```
  
5. Create a trigger that prohibits modifying the database outside work hours:

  ```sql
CREATE OR ALTER TRIGGER outside_workhours_trigger ON DATABASE
FOR ALTER_TABLE
AS
BEGIN
	IF (DATEPART(HOUR, GETDATE()) NOT BETWEEN 9 AND 18) OR
		(DATEPART(DAY, GETDATE()) NOT BETWEEN 1 AND 5)
	BEGIN
		PRINT('Man, you''re really pathetic, go get a life!')
		ROLLBACK TRANSEVENT
	END
END

ALTER TABLE studenti.studenti ALTER COLUMN Nume_Student VARCHAR(50)
  ```
 
6. Create a DDL trigger that would modify all tables if the column _Id_Profesor_ is changed:

  __SQL Querry:__

  ```sql
CREATE OR ALTER TRIGGER RecursiveChange_trigger ON DATABASE
FOR ALTER_TABLE
AS
BEGIN
	DECLARE @id_prof VARCHAR(50) = EVENTDATA().value('(/EVENT_INSTANCE/AlterTableEVENTList/*/Columns/Name)[1]', 'VARCHAR(100)')
	DECLARE @cmd VARCHAR(500)
	DECLARE @proc VARCHAR(500)
	DECLARE @objName VARCHAR(100) 
 
	IF (@id_prof = 'Id_Profesor')
	BEGIN
		SET @cmd = EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]','VARCHAR(100)') 
		SET @objName = EVENTDATA().value('(/EVENT_INSTANCE/ObjectName)[1]','VARCHAR(100)') 
		SET @proc = REPLACE(@cmd, @objName, 'profesori');
		EXECUTE (@proc) 
		SET @proc = REPLACE(@cmd, @objName, 'orarul');
		EXECUTE (@proc) 
		SET @proc = REPLACE(@cmd, @objName, 'studenti_reusita');
		EXECUTE (@proc)
	END 
END
  ```
