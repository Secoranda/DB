


# Laboratory Nr.8

## Objectives
MANAGEMENT OF VIEWS AND TABLES OF EXPRESSIONS

## Task 1
Create two visions based on the questions formulated in the two exercises indicated in the chapter4. 
The first view is built in the Query Editor, and the second, using ViewDesigner.

QUERY:
```

CREATE VIEW Exemplu1 AS
	SELECT *
	FROM studenti
	WHERE Nume_Student like '%u'
GO
SELECT * FROM Exemplu1

CREATE VIEW Example2 AS
	SELECT Disciplina, AVG(Nota) AS AvgMark
	FROM plan_studii.discipline AS C, studenti.studenti_reusita AS D
	WHERE C.Id_Disciplina = D.Id_Disciplina and
	Tip_Evaluare = 'Examen'
	GROUP BY Disciplina
	HAVING AVG(Nota) >7.0
GO

	SELECT * 
	FROM Example2
	ORDER BY Disciplina DESC
```
ViewDesigner:
![0 1](https://user-images.githubusercontent.com/24621285/49341676-a8d73b00-f659-11e8-9810-17811b2f5988.PNG)
![1 1](https://user-images.githubusercontent.com/24621285/49341673-a8d73b00-f659-11e8-9708-7de1cce82e36.PNG)
![1final](https://user-images.githubusercontent.com/24621285/49341674-a8d73b00-f659-11e8-8cbd-54a0387740f1.PNG)

## Task 2
Write an example of instructions INSERT, UPDATE, DELETE on created views. 
Add the relevant comments on the results of their execution instructions.

Comments: Insert, Update, Delete were done on rows of created views. Were inserted 3 rows with last column with "Undeva", which is student's address. With Update, one student got an address and with Delete, from one of left students with "Undeva", we deleted one with First Name "Botezatu"

Query:
```
INSERT INTO Exemplu1 
 VALUES 
	('177', 'Botezatu', 'Maria', '1997-04-12', 'Undeva'),
		('198', 'Cojocaru', 'Ana', '2000-08-21', 'Undeva'),
		('200', 'Marcu', 'Teimur', '1994-10-22', 'Undeva')
GO

Update Exemplu1 
SET Adresa_Postala_Student = 'Mun.Chisinau' WHERE Nume_Student = 'Cojocaru'
GO

 
DELETE FROM Exemplu1
WHERE  Nume_Student = 'Botezatu'
GO

```

Result:

![insertupdate](https://user-images.githubusercontent.com/24621285/49341727-361a8f80-f65a-11e8-9089-dc0fa8d1dfb2.PNG)

*Insert And Update*

![delete](https://user-images.githubusercontent.com/24621285/49341726-361a8f80-f65a-11e8-956d-e08eec607422.PNG)

*Delete*

## Task 3
Write the SQL statements that would change the views created (in exercise 1) in such a way that it is not possible to modify or delete the tables on which they are defined and the views not accept DML operations if the WHERE clause is not satisfied.

Query:
```
DROP VIEW IF EXISTS Exemplu1;
	GO

CREATE VIEW Exemplu1 (Id_Student, Nume_Student, Prenume_Student, Data_Nastere_Student, Adresa_Postala_Student)
			 WITH SCHEMABINDING AS
	SELECT Id_Student, Nume_Student, Prenume_Student, Data_Nastere_Student, Adresa_Postala_Student
	FROM studenti.studenti
	WHERE Nume_Student like '%u'
	WITH CHECK OPTION;
GO
SELECT * FROM Exemplu1

DROP VIEW IF EXISTS Example2;
	GO

CREATE VIEW Example2 (Disciplina, AvgMark) WITH SCHEMABINDING AS
	SELECT Disciplina, AVG(Nota) AS AvgMark
	FROM plan_studii.discipline AS C, studenti.studenti_reusita AS D
	WHERE C.Id_Disciplina = D.Id_Disciplina and
	Tip_Evaluare = 'Examen'	
	GROUP BY Disciplina
	HAVING AVG(Nota) >7.0
	WITH CHECK OPTION;
GO

	SELECT * 
	FROM Example2
	ORDER BY Disciplina DESC
```
Comments: "WITH SCHEMABINDING " is used to prevent the error of a view table in case initial tables are modified and WITH CHECK OPTION
we prevent DML operations related to WHERE clause state.

## Task 4
Write the test instructions for newly defined properties.

Query:
```
--test first view

SELECT * 
FROM Exemplu1
GO
ALTER TABLE studenti.studenti DROP COLUMN Data_Nastere_Student
GO
--test second view

SELECT * 
FROM Example2
GO
ALTER TABLE plan_studii.discipline DROP COLUMN Disciplina
GO

```

Result:
![4 1](https://user-images.githubusercontent.com/24621285/49341825-92ca7a00-f65b-11e8-8789-878d971e8426.PNG)
![4 2](https://user-images.githubusercontent.com/24621285/49341826-92ca7a00-f65b-11e8-8edc-9386f0b3a154.PNG)

## Task 5
Write 2 exercises from Chapter 4, in the same way, that the nested queries to be rendered in the form of CTE expressions.

Query:
```
WITH CTE1 AS 
(
SELECT * 
FROM studenti.studenti)
SELECT * 
FROM CTE1;

WITH CTE2 AS 
(
SELECT Disciplina, AVG(Nota) AS AvgMark
FROM plan_studii.discipline AS C, studenti.studenti_reusita AS D
WHERE C.Id_Disciplina = D.Id_Disciplina and
Tip_Evaluare = 'Examen'
GROUP BY Disciplina
HAVING AVG(Nota) >7.0
)
SELECT * FROM CTE2
ORDER BY Disciplina DESC;
```

Result: Same output as in Task1

## Task 6
It is considered an oriented graph, as shown in the figure below, and you may want to go through the path
from node id = 3 at the node where id = 0. Make the representation of the graph in the form of expression-recursive table.
Note the instruction after UNION ALL of the recursive member, as well as the part up to UNION ALL represented by the anchor member.

*Oriented Graph*
![graph](https://user-images.githubusercontent.com/24621285/49341863-15533980-f65c-11e8-8481-5eb21d154c78.PNG)

Query:
```
DROP TABLE IF EXISTS TASK6;
	GO
CREATE TABLE TASK6 
(
ID INT PRIMARY KEY,
Dependent_ID INT REFERENCES TASK6
)

INSERT INTO TASK6
VALUES
(5 ,0),
(1, 0),
(2, 1),
(4, 2),
(3 ,2),
(0, null)

WITH TRAVERSE (ID,startp, depth)  AS
(SELECT ID, cast(null as int) as startp,0  FROM TASK6
	WHERE Dependent_ID IS NULL
	UNION ALL
	SELECT TASK6.ID, Dependent_ID as startp,TRAVERSE.depth +1  FROM  TASK6
	INNER JOIN TRAVERSE
	ON TASK6.Dependent_ID = TRAVERSE.ID
)
SELECT * FROM TRAVERSE
ORDER BY depth ASC;
```

Result:

![output](https://user-images.githubusercontent.com/24621285/49341873-2f8d1780-f65c-11e8-9201-1abb31664b1d.PNG)
