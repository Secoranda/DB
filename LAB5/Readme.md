

# Laboratory Nr.5

## Objectives
T-SQL: Procedural Instructions

## Task 1
1. Add IF/ELSE statements to the code

Code:

```
declare @N1 int, @N2 int, @N3 int;
declare @mai_mare int;
set @N1 = 60* rand();
set @N2 = 60* rand();
set @N3 = 60* rand();

IF @N1 > @N2 AND @N1 > @N3 SET @mai_mare = @N1
ELSE IF @N2 > @N1 AND @N2 > @N3 SET @mai_mare = @N2
ELSE SET @mai_mare = @N3

print @N1;
print @N2;
print @N3;
print 'Mai mare =' + cast(@mai_mare as varchar(2));
```
Output:

![1](https://user-images.githubusercontent.com/24621285/48149530-fbc10b00-e2c4-11e8-92ac-208627b56646.PNG)

2. Display the first 10 rows of the students' marks on the first evaluation on Databases course, except for marks as 6 and 8

Code:
```
declare @subject char(20), @test char (20), @contor int, @mark1 int, @mark2 int
set @subject = 'Baze de date'
set @test = 'Testul 1'
set @mark1 =6
set @mark2 = 8


select  top 10 Nume_Student, Prenume_Student, Nota
		from studenti, studenti_reusita, discipline
		where 
		 studenti.Id_Student = studenti_reusita.Id_Student
		and
		studenti_reusita.Id_Disciplina = discipline.Id_Disciplina
		and
		Tip_Evaluare =  @test
		and
		Disciplina = @subject
		and
		Nota = iif(Nota <> @mark1 AND Nota <> @mark2, Nota, NULL)
	
```

Output:

![2](https://user-images.githubusercontent.com/24621285/48149420-ae449e00-e2c4-11e8-8ceb-8d8048faed86.PNG)

3. Rewrite the 1st task using CASE:

Code:
```
DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @mai_mare INT;

SET @N1 = 60 * RAND();
SET @N2 = 60 * RAND();
SET @N3 = 60 * RAND();

SET @mai_mare = 

CASE
	WHEN @N1 > @N2 AND @N1 > @N3 THEN @N1
	WHEN @N2 > @N1 AND @N2 > @N3 THEN @N2
	WHEN @N3 > @N1 AND @N3 > @N2 THEN @N3
	ELSE NULL
END

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@mai_mare AS VARCHAR(2)); 
```
Output:

![4](https://user-images.githubusercontent.com/24621285/48149423-ae449e00-e2c4-11e8-9215-374cd278e125.PNG)

4.Rewrite the 1st and 2nd tasks with try/catch blocks and RAISERROR:

4.1 Task ONE



Code:
```

DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE INT;
SET @N1 = 60 * RAND();
SET @N2 = 60 * RAND();
SET @N3 = 60 * RAND();

BEGIN TRY
IF (@N1 = @N2 and @N1 = @N3 and @N2 = @N3) RAISERROR('Eroare', 16,1);
IF @N1 > @N2
 SELECT  @mai_mare  = @N1
ELSE
 SELECT  @mai_mare  = @N2
IF @N3 > @mai_mare
 SELECT @mai_mare = @N3
 ELSE RAISERROR('SOMETHING IS WRONG', 11, 1)

END TRY

BEGIN CATCH
PRINT 'ERROR LINE: ' + CAST(ERROR_LINE() AS VARCHAR(20));
PRINT 'ERROR NUMBER: ' + CAST(ERROR_NUMBER() AS VARCHAR(20));
END CATCH;

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(20));
```

Output:

![3](https://user-images.githubusercontent.com/24621285/48149422-ae449e00-e2c4-11e8-8210-236ddb993736.PNG)

4.2 TASK TWO 



Code:
```
declare @subject char(20), @test char (20), @contor int, @mark1 int, @mark2 int
set @subject = 'Baze de date'
set @test = 'Testul 1'
set @mark1 =6
set @mark2 = 8

begin try
select  top 10 Nume_Student, Prenume_Student, Nota
		from studenti, studenti_reusita, discipline
		where 
		 studenti.Id_Student = studenti_reusita.Id_Student
		and
		studenti_reusita.Id_Disciplina = discipline.Id_Disciplina
		and
		Tip_Evaluare =  @test
		and
		Disciplina = @subject
		and
		Nota = iif(Nota <> @mark1 AND Nota <> @mark2, Nota, NULL)
end try	

begin catch
	
	print 'Error number : ' + cast(error_number() as varchar(20))
	print 'Error Line : ' + cast(error_line() as varchar(20))
end catch
```
