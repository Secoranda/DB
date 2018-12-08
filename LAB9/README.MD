

# Laboratory Nr.9

## Objectives
STORED PROCEDURES AND DEFINED FUNCTIONS BY USER

## Task 1
Create procedures stored on exercise basis (2 exercises) in Chapter 4. Input parameters must meet the criteria in the WHERE clauses of those exercises.

QUERY:
```
DROP PROCEDURE IF EXISTS Task1;
GO
CREATE PROCEDURE Task1
	@Sufix Varchar(255) = '%u'
AS
	SELECT * 
	FROM studenti.studenti
	WHERE Nume_Student LIKE @Sufix

EXECUTE Task1

DROP PROCEDURE IF EXISTS TASK2;
GO
CREATE PROCEDURE TASK2
	@Test Varchar(255)= 'EXAMEN',
	@Mark FLOAT = '7.0'
AS
	SELECT Disciplina, AVG(Nota) AS AvgMark
	FROM plan_studii.discipline AS C, studenti.studenti_reusita AS D
	WHERE C.Id_Disciplina = D.Id_Disciplina and
	Tip_Evaluare = @Test
	GROUP BY Disciplina
	HAVING AVG(Nota) > @Mark

EXECUTE TASK2;
```
Output: Same output as in LAB4

## Task 2
Create a stored procedure that has no input parameter and has an output parameter. The exit parameter must return the number of students who have not supported at least one form of assessment (note less than 5 or NULL).

QUERY: 

```
DROP PROCEDURE IF EXISTS OUTPUTPAR;
GO
CREATE PROCEDURE OUTPUTPAR
	@Nrstud INT OUTPUT
AS
	SELECT @Nrstud = Count(Distinct(Id_Student)) 
	FROM plan_studii.discipline AS C, studenti.studenti_reusita AS D
	WHERE Nota < '5' AND Nota IS NOT NULL

DROP PROCEDURE IF EXISTS dbo.spINSERT_universitatea;
GO
```

OUTPUT:

![task2](https://user-images.githubusercontent.com/24621285/49687344-56d75f00-fb0a-11e8-9158-cb4338bdd014.PNG)

## Task 3
Create a stored procedure that would insert information about a new student into the database. as input parameters to serve the new student's personal data and the GroupID. Generate all the key entries required in the student_reusita table. Evaluation notes to be inserted as NULL.

QUERY:
```
CREATE PROCEDURE dbo.spINSERT_universitatea
	@Id INT = '210',
	@Name VARCHAR(50) = 'Agapi',
	@Surmane VARCHAR(50) = 'Ionela',
	@Birth DATE = '2010-12-12',
	@Address VARCHAR(500) = NULL,
	@Grupa CHAR(6) = 'INF171'

AS


INSERT INTO studenti.studenti
	(
	Id_Student, Nume_Student,Prenume_Student,Data_Nastere_Student,Adresa_Postala_Student
	)
VALUES (@Id,@Name,@Surmane,@Birth,@Address)


INSERT INTO studenti.studenti_reusita (Id_Student,Id_Disciplina,Id_Profesor,Id_Grupa,Tip_Evaluare,Nota,Data_Evaluare)
VALUES (@Id,
		105,
		110,
		(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = @Grupa),
		N'Examen',
		NULL,
		NULL)
    
    EXECUTE dbo.spINSERT_universitatea;
```

OUTPUT:


![task3 1](https://user-images.githubusercontent.com/24621285/49687346-56d75f00-fb0a-11e8-9040-7909a0a910f9.PNG)
![task3](https://user-images.githubusercontent.com/24621285/49687347-576ff580-fb0a-11e8-8765-bc5f91c5e191.PNG)


## Task 4
Whether a professor is relieved from office at the middle of the semester. Create a stored procedure that would reallocate records from the student_reusita table to another teacher. Input Parameters: First and last name of the old teacher, name and surname of the new teacher, discipline. if the inserted data is incorrect or incomplete, display a warning message.

QUERY:
```

DROP PROCEDURE IF EXISTS TASK4;
GO

CREATE PROCEDURE TASK4
	@OLDNAME VARCHAR(50),
	@OLDSURNAME VARCHAR(50),
	@NEWNAME VARCHAR(50),
	@NEWSURNAME VARCHAR(50),
	@SUBJECT VARCHAR(255),
	@ID_TOLD INT = NULL,
	@ID_TNEW INT = NULL,
	@ID_SUB INT = NULL

AS
	DECLARE @ERR_MESSAGE NVARCHAR(255)
	SET @ID_TOLD = (SELECT Id_Profesor
					FROM cadre_didactice.profesori
					WHERE Nume_Profesor = @OLDNAME
						AND Prenume_Profesor =@OLDSURNAME)
	
	SET @ID_TNEW = (SELECT Id_Profesor
					FROM cadre_didactice.profesori
					WHERE Nume_Profesor = @NEWNAME
						AND Prenume_Profesor =@NEWSURNAME)

	SET @ID_SUB = (SELECT Id_Disciplina
					FROM plan_studii.discipline
					WHERE Disciplina=@SUBJECT)

	
	IF @ID_TOLD IS NULL OR @ID_TNEW IS NULL OR @ID_SUB IS NULL
		BEGIN
			SET @ERR_MESSAGE = 'Missing ore or more IDs'
			RAISERROR(@ERR_MESSAGE, 10 ,1)
		END
	ELSE
		BEGIN
			UPDATE studenti.studenti_reusita SET Id_Profesor = @ID_TNEW 
				WHERE Id_Profesor = @ID_TOLD and Id_Disciplina = @ID_SUB;
				SELECT * FROM studenti.studenti_reusita WHERE Id_Profesor = @ID_TOLD
				SELECT * FROM studenti.studenti_reusita WHERE Id_Profesor = @ID_TNEW
		END
GO

EXECUTE TASK4 
	@OLDNAME = 'Popescu',
	@OLDSURNAME = 'Gabriel',
	@NEWNAME = 'Bivol',
	@NEWSURNAME = 'Ion',
	@SUBJECT = 'Sisteme de operare'
```
OUTPUT:


![task4](https://user-images.githubusercontent.com/24621285/49687348-576ff580-fb0a-11e8-90fc-64092cb2639a.PNG)

*Error Message*
![error message](https://user-images.githubusercontent.com/24621285/49687354-58a12280-fb0a-11e8-9c35-467fa1e679c6.PNG)


## Task 5
Create a stored procedure that would form a list of the top 3 best students in a discipline, and add these marks to the final exam with one point (the maximum possible mark is 10). as an input parameter, will serve as the name of the discipline. The procedure returns the following fields: Cod_ Grupa, Nume_Prenume_Student, Disciplina, Nota _ Veche, Nota _ Noua.

QUERY:
```
DROP PROCEDURE IF EXISTS TASK5;
GO

CREATE PROCEDURE TASK5
	@SUBJECT VARCHAR(255),
	@OLDMARK INT = NULL,
	@ID_SUB INT = NULL

AS
	SET @ID_SUB = (SELECT Id_Disciplina
					FROM plan_studii.discipline
					WHERE Disciplina = @SUBJECT)
	
BEGIN 
	UPDATE studenti.studenti_reusita SET Nota = Nota + 1 
	WHERE Nota < 10 AND Tip_Evaluare = 'Examen'
	AND Id_Student IN (SELECT TOP 3 Id_Student 
						FROM studenti.studenti_reusita
						WHERE Id_Disciplina = @ID_SUB 
						GROUP BY Id_Student
						ORDER BY AVG(CAST(Nota AS DECIMAL(4,2))) DESC)
	
	SELECT Id_Grupa AS Cod_Grup, Nume_Student, Prenume_Student, Disciplina, (Nota -1) AS Nota_Veche, Nota as Nota_Noua
	FROM (studenti.studenti_reusita A join studenti.studenti B ON A.Id_Student = B.Id_Student ) 
				JOIN plan_studii.discipline C ON A.Id_Disciplina = C.Id_Disciplina
	WHERE A.Id_Disciplina IN (SELECT Id_Disciplina FROM  plan_studii.discipline WHERE Disciplina = @SUBJECT)
			AND Tip_Evaluare = 'Examen'
			AND A.Id_Student IN (SELECT TOP 3 Id_Student 
								FROM studenti.studenti_reusita
								WHERE Id_Disciplina = @ID_SUB 
								GROUP BY Id_Student
								ORDER BY AVG(CAST(Nota AS DECIMAL(4,2))) DESC) 
	
	ORDER BY Nume_Student
END
GO
EXECUTE TASK5 @SUBJECT ='Programarea aplicatiilor Windows'
```

OUTPUT:

![task5 1](https://user-images.githubusercontent.com/24621285/49687349-576ff580-fb0a-11e8-8167-94c0ceecf352.PNG)
![task5 2](https://user-images.githubusercontent.com/24621285/49687350-58088c00-fb0a-11e8-9ed4-40b165557150.PNG)


## Task 6
Create user-defined functions based on the exercises (2 exercises) in Chapter 4. Input parameters must meet the criteria in the WHERE clauses of those exercises.

QUERY: 
```
---first
DROP FUNCTION IF EXISTS FUNCT1;
GO
CREATE FUNCTION FUNCT1 (@LETTER VARCHAR(255))
RETURNS TABLE
WITH ENCRYPTION
AS
	RETURN
	(SELECT *
	FROM studenti.studenti
	WHERE Nume_Student LIKE @LETTER);
GO
SELECT *
FROM FUNCT1('%u')


--second
DROP FUNCTION IF EXISTS FUNCT2;
GO
CREATE FUNCTION FUNCT2 (@MARK DECIMAL(4,2))
RETURNS TABLE
WITH ENCRYPTION
AS
	RETURN
	(SELECT Disciplina, AVG(Nota) AS AvgMark
		FROM plan_studii.discipline AS C, studenti.studenti_reusita AS D
		WHERE C.Id_Disciplina = D.Id_Disciplina and
		Tip_Evaluare = 'Examen'
		GROUP BY Disciplina
		HAVING AVG(Nota) > @Mark);
GO
SELECT *
FROM FUNCT2(7.0)
```

## Task 7 
Write the function that would calculate the student's age. Define the following function format: <Junction Name> (<Date _ Birth> _Student>).
 
 QUERY:
 ```
 DROP FUNCTION IF EXISTS TASK7;
GO
CREATE FUNCTION TASK7 (@DATE DATE)
RETURNS INT
	BEGIN
		DECLARE @AGE INT;
		SELECT @AGE = DATEDIFF(YEAR, @DATE,GETDATE())
		FROM studenti.studenti
		WHERE Data_Nastere_Student = @DATE
	RETURN @AGE
	END
GO
SELECT Nume_Student, Prenume_Student, dbo.TASK7(Data_Nastere_Student) AS VIRSTA
FROM studenti.studenti
GO
 ```
 
 OUTPUT:

![task7](https://user-images.githubusercontent.com/24621285/49687351-58088c00-fb0a-11e8-85b0-14c8efec37bf.PNG)

 
 ## Task 8
 Create a user-defined function that would return the data about a student's success. The following function format is defined: <Junction Name (<Student Name>). Show the table with the following fields:  Nume_Prenume_Student, Disticplina, Nota, Data_Evaluare.
  
  QUERY:
  ```
 DROP FUNCTION IF EXISTS TASK8;
GO
CREATE FUNCTION TASK8 (@NAME VARCHAR(50))
RETURNS INT
	BEGIN
		DECLARE @MARK INT;
		SELECT @MARK = Nota
		FROM studenti.studenti_reusita A JOIN studenti.studenti B
				ON A.Id_Student = B.Id_Student
		WHERE Nume_Student = @NAME
		RETURN @MARK
	END
GO

SELECT Nume_Student, Prenume_Student, Disciplina, dbo.TASK8(Nume_Student) as Nota, Data_Evaluare
FROM (studenti.studenti_reusita A JOIN studenti.studenti B 
		ON A.Id_Student = B.Id_Student) JOIN plan_studii.discipline C ON
		A.Id_Disciplina = C.Id_Disciplina
GO
 
 ```
 
 OUTPUT:
 
![task8](https://user-images.githubusercontent.com/24621285/49687352-58088c00-fb0a-11e8-9b6d-6fe1e26f2e9c.PNG)

 ## Task 9
 It is required to perform a user-defined function that would find the most lenient or weakest student in a group. Define the following function format: <junction name> (<GroupName>, <is_good>). The parameter <is_good> can accept the values "sarguincios" or "slab" respectively. The function returns a table with the following fields: Grupa, Nume_Prenume_Student, Nota Medie , is_good. Note Average to be accurate to 2 decimal places.
  
  QUERY:
  ```
  DROP FUNCTION IF EXISTS TASK9;
GO
CREATE FUNCTION TASK9 (@COD_GRUPA INT, @IS_GOOD VARCHAR(50))
RETURNS @TABEL TABLE
 (NUME VARCHAR(50), PRENUME VARCHAR(50), NOTA_MEDIE DECIMAL(10,2), IS_GOOD VARCHAR(50))
 AS
 BEGIN
	IF (@IS_GOOD = 'sirguincios')
	BEGIN 
		INSERT INTO @TABEL
		SELECT TOP 1 Nume_Student, Prenume_Student, AVG(CAST(Nota AS DECIMAL(10, 2))) AS Nota_Medie
		FROM studenti
-----TASK 9
DROP FUNCTION IF EXISTS TASK9;
GO
CREATE FUNCTION TASK9 (@COD_GRUPA INT, @IS_GOOD VARCHAR(50))
RETURNS @TABEL TABLE
 (NUME VARCHAR(50), PRENUME VARCHAR(50), NOTA_MEDIE DECIMAL(10,2), IS_GOOD VARCHAR(50))
 AS
 BEGIN
	IF (@IS_GOOD = 'sirguincios')
	BEGIN 
		INSERT INTO @TABEL
		SELECT TOP 1 Nume_Student, Prenume_Student, AVG(CAST(Nota AS DECIMAL(10, 2))) AS NOTA_MEDIE, @IS_GOOD AS IS_GOOD
		FROM studenti.studenti_reusita A JOIN studenti.studenti B 
		ON A.Id_Student = B.Id_Student
		WHERE Id_Grupa = @COD_GRUPA
		GROUP BY A.Id_Student, B.Nume_Student, B.Prenume_Student
		ORDER BY AVG(CAST(Nota AS DECIMAL(10, 2))) DESC
	END
	ELSE IF (@IS_GOOD = 'slab')
	BEGIN
		INSERT INTO @TABEL
		SELECT TOP 1 Nume_Student, Prenume_Student, AVG(CAST(Nota AS DECIMAL(10, 2))) AS NOTA_MEDIE , @IS_GOOD AS IS_GOOD
		FROM studenti.studenti_reusita A JOIN studenti.studenti B 
		ON A.Id_Student = B.Id_Student
		WHERE Id_Grupa = @COD_GRUPA
		GROUP BY A.Id_Student, B.Nume_Student, B.Prenume_Student
		ORDER BY AVG(CAST(Nota AS DECIMAL(10, 2))) ASC
	END
	RETURN
	END
GO
SELECT * FROM TASK9(1, 'sirguincios')
SELECT * FROM TASK9(1, 'slab')
  
  ```
   OUTPUT:
   

![task9](https://user-images.githubusercontent.com/24621285/49687353-58a12280-fb0a-11e8-90ac-c9286917412b.PNG)
