# Laboratory Work No.8


### Objectives

#### Views and table-expressions

### Tasks

1. Create 2 views based on previously created querries (in lab4). The first view should be created in the querry editor, the second in View Designer mode:

  __SQL Querry:__
  
  ```sql
  CREATE VIEW view1 AS
	SELECT *
	FROM grupe
  GO
  ```
  
  __Result:__

  ![task1_1](https://user-images.githubusercontent.com/22482507/49365851-83494080-f6f0-11e8-86fc-65da8c787682.JPG)
  ![task1_2](https://user-images.githubusercontent.com/22482507/49365849-83494080-f6f0-11e8-8839-e24debfe98f4.JPG)


2. Write an example of Insert, Update, Delete statements on the created view:

    __SQL Querry:__
  
  ```sql
  INSERT INTO view1
  VALUES ('FAF161', 'TI', 'CIM', 133, 103)
  GO

  UPDATE view1
  SET Specialitate = 'Tehnologii Informationale' WHERE Specialitate = 'TI'
  GO

  DELETE FROM view1
  WHERE Cod_Grupa = 'FAF161'
  GO
  ```

3. Write the necessary SQL instructions which would modify the views so they can't be modified or deleted if the _WHERE_ cause isn't satisfied:

  __SQL Querry:__

  ```sql
  DROP VIEW IF EXISTS view1

  CREATE VIEW view1 (Id_Grupa, Cod_Grupa, Specialitate, Nume_Facultate, Sef_grupa, Prof_Indrumator) WITH SCHEMABINDING AS
    SELECT Id_Grupa, Cod_Grupa, Specialitate, Nume_Facultate, Sef_grupa, Prof_Indrumator
    FROM dbo.grupe
    WITH CHECK OPTION
  GO

  SELECT * FROM view1

  DROP VIEW IF EXISTS VIEWDESIGNER

  CREATE VIEW VIEWDESIGNER (Desciplina) WITH SCHEMABINDING AS
    SELECT Disciplina
    FROM plan_studii.discipline
    WHERE LEN(Disciplina) > 20
    WITH CHECK OPTION
  GO

  SELECT * FROM VIEWDESIGNER
  ```
  
4. Test the previous instructions:

  __SQL Querry:__
  
  ```sql
  SELECT * FROM view1
  ALTER TABLE grupe DROP COLUMN Cod_Grupa

  SELECT * FROM VIEWDESIGNER
  ALTER TABLE plan_studii.discipline DROP COLUMN Disciplina
  ```
  + __The object 'UQ__grupe__1AAD322C13F2842D' is dependent on column 'Cod_Grupa'.__
  + __ALTER TABLE DROP COLUMN Cod_Grupa failed because one or more objects access this column.__
  + __The object 'VIEWDESIGNER' is dependent on column 'Disciplina'.__
  + __ALTER TABLE DROP COLUMN Disciplina failed because one or more objects access this column.__
  
5. Write 2 querries from the 4th lab in __CTE__:

  ```sql
  WITH CTE1 AS (SELECT * FROM grupe)

  SELECT * FROM CTE1

  WITH CTE2 AS
    (SELECT Disciplina
    FROM plan_studii.discipline
    WHERE LEN(Disciplina) > 20)

  SELECT * FROM CTE2
  ```

6. Write a recursive table-expression to traverse in depth a graph (from any node to 0):

  __SQL Querry:__

  ```sql
  CREATE TABLE graph
  (
    id INT PRIMARY KEY,
    anchor INT REFERENCES graph
  )

  INSERT INTO graph
  VALUES	(1 ,0),
      (2, 1),
      (3, 2),
      (4, 2),
      (5, 0),
      (0, null)

  WITH graph_traversal (curr, prev, depth)  AS
    (SELECT id, cast(null as int) as prev, 0
    FROM graph
    WHERE anchor IS NULL
    UNION ALL
    SELECT id, anchor as prev, depth + 1
    FROM graph
    INNER JOIN graph_traversal ON anchor = curr)
  SELECT * FROM graph_traversal
  ```
  
   __Output:__

| curr | prev | depth | 
|------|------|-------| 
| 0    | NULL | 0     | 
| 1    | 0    | 1     | 
| 5    | 0    | 1     | 
| 2    | 1    | 2     | 
| 3    | 2    | 3     | 
| 4    | 2    | 3     | 

