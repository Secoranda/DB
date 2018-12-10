

# Laboratory Nr.10

## Objectives
CREATING AND USING TRIGGERS

## Task 1
Change the new recorder trigger so that if the audience updates, an information message appears, which, besides discipline and time, will display the affected group code, the day, the block, the old audience, and the new audience.

QUERY:
```
DROP TRIGGER IF EXISTS inreg_n2;
GO

CREATE TRIGGER  inreg_n2  ON plan_studii.orarul
	AFTER UPDATE
	AS
	IF UPDATE(Auditoriu)
	BEGIN
		SELECT DISTINCT( 'Lectia la disciplina " ' + UPPER(d.Disciplina)+
		'" de la ora ' + CAST(inserted.Ora AS VARCHAR(5)) + ' pentru grupa ' +  CAST(g.Cod_Grupa AS CHAR(6)) +
		' a fost transferat in aula ' + CAST (inserted.Auditoriu AS CHAR(3)) + ' din aula ' +  CAST (deleted.Auditoriu AS CHAR(3)))
		FROM ((inserted INNER  JOIN plan_studii.discipline d ON inserted.Id_Disciplina = d.Id_Disciplina) INNER JOIN grupe g ON inserted.Id_Grupa =  g.Id_Grupa)
			INNER JOIN plan_studii.orarul s ON inserted.Id_Disciplina = s.Id_Disciplina,
			deleted INNER JOIN plan_studii.orarul p ON deleted.Id_Disciplina = p.Id_Disciplina,
			plan_studii.orarul f
		WHERE f.Auditoriu <> inserted.Auditoriu and deleted.Auditoriu <>inserted.Auditoriu
	END
GO
```
OUTPUT:

![task1](https://user-images.githubusercontent.com/24621285/49704808-8c6e6c00-fc20-11e8-8fdf-0040d2e9b690.PNG)



## Task 2
Create the trigger, which would ensure the correct (consecutive) population success of the students and students_reusita tables, and would allow the avoidance of key errors.

QUERY:
```
DROP TRIGGER IF EXISTS inreg_n3;
GO

CREATE TRIGGER  inreg_n3  ON studenti.studenti
INSTEAD OF INSERT 
AS 
	BEGIN

		DECLARE @IDST INT;
		DECLARE @NAME VARCHAR(50) ;
		DECLARE @SURNAME VARCHAR(50) ;
		DECLARE @DATE DATE;
		DECLARE @ADDRESS VARCHAR(500);
		IF EXISTS (SELECT 1 FROM inserted)
			BEGIN
			SELECT @IDST = Id_Student FROM inserted
			SELECT @NAME = Nume_Student FROM inserted
			SELECT @SURNAME = Prenume_Student FROM inserted
			SELECT @DATE = Data_Nastere_Student FROM inserted
			SELECT @ADDRESS = Adresa_Postala_Student FROM inserted
		
			IF EXISTS(SELECT Id_Student FROM studenti.studenti WHERE Id_Student = @IDST)
			BEGIN
				PRINT('THIS STUDENT ALREADY WAS ENTERED INTO STUDENTI TABLE')
				END
			ELSE

			INSERT INTO studenti.studenti (Id_Student, Nume_Student,Prenume_Student,Data_Nastere_Student,Adresa_Postala_Student)
			VALUES (@IDST,@NAME,@SURNAME,@DATE,@ADDRESS)
			IF EXISTS (SELECT 1 FROM inserted)
				BEGIN
				
				INSERT INTO studenti.studenti_reusita(Id_Student,Id_Disciplina,Id_Profesor,Id_Grupa,Tip_Evaluare,Nota,Data_Evaluare)
				VALUES (@IDST,105,110,1,N'EXAMEN', NULL,NULL)
				PRINT('NEW STUDENT ADDED TO STUNDETI_REUSITA')
				END
				ELSE
				BEGIN
					PRINT ('ERROR ON FOREIGN KEYS')
				END
				PRINT('NEW STUDENT ADDED IN STUDENTI')
			END
			
		ELSE

			PRINT ('ERROR - CHECK INPUT')
	END
GO
```
OUTPUT:

![tsk2](https://user-images.githubusercontent.com/24621285/49704812-8d070280-fc20-11e8-9b17-50134d6f1b6f.PNG)


## Task 3
Create a trigger that would prohibit lowering scores in the student_reusita table and changing values in the Values_Data field, where the values in this field are null. The trigger must only be launched if the data of students in group "CIB 1 71" are affected. A warning message will be displayed in case of an attempt to violate the constraint.


QUERRY:
 ```
 DROP TRIGGER IF EXISTS inreg_n4;
GO

CREATE TRIGGER inreg_n4 ON studenti.studenti_reusita
FOR UPDATE 
AS
BEGIN
		DECLARE @ID INT;
		SELECT @ID = (SELECT Id_Student FROM inserted)
			IF UPDATE(Nota)
				BEGIN
					IF EXISTS(SELECT Id_Student FROM studenti.studenti_reusita a JOIN grupe b ON a.Id_Grupa = b.Id_Grupa WHERE Cod_Grupa = 'CIB171' and Id_Student = @ID)
					BEGIN
						IF EXISTS(SELECT * FROM studenti.studenti_reusita s INNER JOIN inserted i ON s.Id_Disciplina = i.Id_Disciplina
									INNER JOIN deleted d ON i.Id_Disciplina = d.Id_Disciplina
								 WHERE s.Nota<>i.Nota and d.Nota<>i.Nota and i.Nota < d.Nota )
								 BEGIN
									 PRINT('RESTRICTION TO DECREASE MARK')
									 ROLLBACK TRANSACTION	 
								 END
					END
				END
			IF UPDATE(Data_Evaluare)
				BEGIN
				IF EXISTS(SELECT Id_Student FROM studenti.studenti_reusita a JOIN grupe b ON a.Id_Grupa = b.Id_Grupa WHERE Cod_Grupa = 'CIB171' and Id_Student = @ID )
					BEGIN
					IF EXISTS(SELECT * FROM studenti.studenti_reusita s INNER JOIN inserted i ON s.Id_Disciplina = i.Id_Disciplina
								INNER JOIN deleted d ON i.Id_Disciplina = d.Id_Disciplina
							 WHERE s.Data_Evaluare<>i.Data_Evaluare and  d.Data_Evaluare<>i.Data_Evaluare and d.Data_Evaluare IS NOT NULL)
							BEGIN
								PRINT('RESTRICTION TO CHANGE DATE IS INSERTED ONCE')
								ROLLBACK TRANSACTION	
							END	
					END	 
				END
	END
GO   
 ```
 
 OUTPUT:
 ![3 1](https://user-images.githubusercontent.com/24621285/49704807-8c6e6c00-fc20-11e8-9194-afc5af5f75b9.PNG)
![task3 0](https://user-images.githubusercontent.com/24621285/49704809-8d070280-fc20-11e8-9872-0d33d547063f.PNG)
![task3](https://user-images.githubusercontent.com/24621285/49704810-8d070280-fc20-11e8-83e8-30b59d0ef8dc.PNG)



 ## Task 4
 Create a DDL trigger that would prohibit modifying the ld_Disciplinary column in the university table tables with the message display.
 
 QUERRY:
 ```
 
CREATE TRIGGER inreg_n5
ON DATABASE
FOR ALTER_TABLE
AS 
BEGIN
		DECLARE @MODCOLUMN VARCHAR(max);
		SET @MODCOLUMN = (SELECT EVENTDATA().value('(/EVENT_INSTANCE/AlterTableActionList/Alter/Columns/Name)[1]','varchar(max)'));
		IF @MODCOLUMN = 'Id_Disciplina'
			BEGIN
				PRINT ('NO RIGHT TO CHANGE ID_DISCIPLINA COLUMN')
				ROLLBACK;
			END
END
GO
 ```
 
 ## Task 5
 Create a DDL trigger that would prohibit changing the schema of the database outside work hours.
 
 QUERRY:
 ```
 DROP TRIGGER IF EXISTS intreg_n6 ON DATABASE
GO

CREATE TRIGGER intreg_n6 ON DATABASE
FOR ALTER_TABLE
AS

IF (DATEPART(HOUR, GETDATE()) NOT BETWEEN 7 and 17)AND (DATEPART(DAY, GETDATE()) NOT IN (1,5))
BEGIN 
	PRINT('NOW IS NON-WORKINT TIME')
	ROLLBACK
END
 ```
 OUTPUT:
 

![task5](https://user-images.githubusercontent.com/24621285/49704838-ca6b9000-fc20-11e8-95ee-ad4bac8b582c.PNG)

 ## Task 6
 Create a DDL trigger that, when changing the properties of the ld_Professor column in a table, would make similar changes automatically in the rest of the tables.
 
 QUERRY:
 ```
 DROP TRIGGER IF EXISTS lastone ON DATABASE
GO

CREATE TRIGGER lastone
ON DATABASE
FOR ALTER_TABLE
AS

  DECLARE @IDTEACHER VARCHAR(50)
  DECLARE @ACTION1 VARCHAR(500)
  DECLARE @ACTION2 VARCHAR(500)
  DECLARE @ACTION3 VARCHAR(100)
  SELECT @IDTEACHER=EVENTDATA().value('(/EVENT_INSTANCE/AlterTableActionList/*/Columns/Name)[1]', 'nVARCHAR(100)') 
  
  IF @IDTEACHER='Id_Profesor'
  BEGIN
	  SELECT @ACTION1 = EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]','nVARCHAR(max)') 
	  SELECT @ACTION3 = EVENTDATA().value('(/EVENT_INSTANCE/ObjectName)[1]','nVARCHAR(max)') 
	  SELECT @ACTION2 = REPLACE(@ACTION1, @ACTION3, 'profesori');
		EXECUTE (@ACTION2) 
	  SELECT @ACTION2 = REPLACE(@ACTION1, @ACTION3, 'orarul');
		 EXECUTE (@ACTION2) 
	  SELECT @ACTION2 = REPLACE(@ACTION1, @ACTION3, 'studenti_reusita');
		EXECUTE (@ACTION2)   
	  PRINT 'DATA WAS MOTIFIED'   
  END 
GO
 ```
