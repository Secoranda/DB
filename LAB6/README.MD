
# Laboratory Nr.6

## Objectives
Creation of tables and indexes

## Task 1
 Write a T-Sql instruction, that will populate the column "Adresa _ Postala _ Profesor" from table "profesori" with value "mun.Chisinau", where the column value is NULL.

Code:

```
UPDATE profesori
SET Adresa_Postala_Profesor = 'mun.Chisinau'
WHERE Adresa_Postala_Profesor IS NULL;

SELECT * FROM profesori

```

Result:

![one](https://user-images.githubusercontent.com/24621285/49031299-2c32f100-f16f-11e8-9054-549bf5579d9c.PNG)


## Task 2
Modify the table named "grupe" according to the following conditions:
1."Cod_Grupa" field should accept unique values and should not accept unknown values
2.Take in consideration that the primary key is already defined on Id_Group column 

Code:
```
ALTER TABLE grupe 
ALTER COLUMN Cod_Grupa CHAR(6) not null;

ALTER TABLE grupe
ADD UNIQUE(Cod_Grupa);

SELECT Cod_Grupa
FROM grupe
```
Result:

![two](https://user-images.githubusercontent.com/24621285/49031297-2b9a5a80-f16f-11e8-81cf-98de6b87f995.PNG)


## Task 3
Add 2 columns: Sef_Grupa ; Prof_Indrumator of type Int, to the table named grupe. Populate the given column fields with values according to the following criteria:

1. Seful_grupei should have the best average mark from his group on all disciplines and all types of evaluation.
2. Prof_Indrumator column should teach a maximum number of disciplines for this group. If there are more candidates that correspond to this conditions, then get the teacher with the minimal Id from the teachers that correspond to the previous criteria. The values inthis columns should be unique
3. Write ALTER, INSERT, UPDATE instruction, necessary for creating the given fields, for selecting the corresponding candidates and data insertion. 
Code:
```
ALTER TABLE grupe
ADD Sef_grupa INT, Prof_Indrumator INT;

UPDATE grupe
SET Sef_grupa =	(	SELECT TOP 1 Id_Student
  				FROM studenti_reusita R
  				WHERE grupe.Id_Grupa = R.Id_Grupa
  				GROUP BY Id_Student
  				ORDER BY AVG(CONVERT(FLOAT, Nota)) DESC)
SELECT * 
FROM grupe

UPDATE grupe
SET Prof_Indrumator = (	SELECT TOP 1 Id_Profesor
  					FROM studenti_reusita R
  					WHERE grupe.Id_Grupa = R.Id_Grupa
  					GROUP BY Id_Profesor
  					ORDER BY COUNT(Id_Disciplina) DESC, Id_Profesor DESC)
SELECT * 
FROM grupe
```
Result:

![three 1](https://user-images.githubusercontent.com/24621285/49031295-2b9a5a80-f16f-11e8-9442-1b361a8ba1bf.PNG)
![three 2](https://user-images.githubusercontent.com/24621285/49031296-2b9a5a80-f16f-11e8-9a7a-3e4dd379af1c.PNG)


## Task 4
Write a T-SQL instruction, that will increase the marks of all group-leads with one point:

```
UPDATE studenti_reusita 
SET Nota += 1 WHERE Nota <> 10 AND Id_Student IN (SELECT Sef_grupa FROM grupe)

SELECT NOTA, Id_Student 
FROM studenti_reusita
WHERE Id_Student IN (SELECT Sef_grupa FROM grupe)
```

Result:
There are three Sef_grupa, because there are 3 groups. So the output will be the leaders' marks off all tests, exams etc (table is just to big).

## Task 5
Create a table named "profesori_new", with columns: Id_Profesor, Nume_Profesor, Prenume_Profesor, Localitate, Adresa_1, Adresa_2

1. Id_Profesor - primary key, CLUSTERED
2. Localitate - have default value mun.Chisinau
3. Insert data from table "profesori" to the new table, according to the followingmapping rules: ⋅⋅1. Id_Profesor = Id_Profesor ⋅⋅2. Nume_Profesor = Nume_Profesor ⋅⋅3. Prenume_Profesor = Prenume_Profesor ⋅⋅4. Adresa_Postala_Profesor = Localitate ⋅⋅5. Adresa_Postala_Profesor = Adresa_1 (street) ⋅⋅6. Adresa_Postala_Profesor = Adresa_2 (house number) 

```
CREATE TABLE profesori_new (
	Id_Profesor INT,
	Nume_Profesor VARCHAR(60) NOT NULL,
	Prenume_Profesor VARCHAR(60) NOT NULL,
	Localitate VARCHAR(255) DEFAULT('mun.Chisinau'),
	Adresa_1 VARCHAR(255),
	Adresa_2 VARCHAR(255),
	PRIMARY KEY CLUSTERED (Id_Profesor))

INSERT INTO profesori_new(Id_Profesor, Nume_Profesor, Prenume_Profesor, Localitate, Adresa_1, Adresa_2)
SELECT	Id_Profesor,
		Nume_Profesor,
		Prenume_Profesor,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, 1, CHARINDEX(', str.', Adresa_Postala_Profesor) - 1)
			WHEN CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, 1, CHARINDEX(', bd.', Adresa_Postala_Profesor) - 1)
			ELSE SUBSTRING(Adresa_Postala_Profesor, 1, LEN(Adresa_Postala_Profesor))
		END Localitate,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(	Adresa_Postala_Profesor,
								CHARINDEX(', str.', Adresa_Postala_Profesor) + 2,
								CHARINDEX(',', SUBSTRING(	Adresa_Postala_Profesor,
															CHARINDEX(', str.', Adresa_Postala_Profesor) + 2, 99)) - 1)
			WHEN CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(	Adresa_Postala_Profesor,
								CHARINDEX(', bd.', Adresa_Postala_Profesor) + 2,
								CHARINDEX(',', SUBSTRING(	Adresa_Postala_Profesor,
															CHARINDEX(', bd.', Adresa_Postala_Profesor) + 2, 99)) - 1)
		END Adresa_1,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0 OR CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, PATINDEX('%[0-9]%', Adresa_Postala_Profesor), 10)
		END Adresa_2

FROM profesori

SELECT * 
FROM profesori

```

Result: 

![five](https://user-images.githubusercontent.com/24621285/49031298-2c32f100-f16f-11e8-9658-8af82c3cdac8.PNG)




## Task 6
Insert the next data in "orar" table, for the group CIB171, with Id_Group = 1, for Monday. 
All lessons will take place in studies block 'B' 
(ld_Disciplina = 107, Id_Profesor= 101, Ora ='08:00', Auditoriu = 202); 
(Id_Disciplina = 108, Id_Profesor= 101, Ora ='11:30', Auditoriu = 501);
(ld_Disciplina = 119, Id_Profesor= 117, Ora ='13:00', Auditoriu = 501);

```
CREATE TABLE orarul(Id_Grupa SMALLINT DEFAULT(1),
					 Id_Disciplina INT NOT NULL,
					Id_Profesor INT,
					Ora TIME,
					Auditoriu INT,
					Bloc CHAR(2) NOT NULL DEFAULT('B'),
					Zi CHAR(20));


INSERT INTO orarul(Id_Disciplina, Id_Profesor, Zi, Ora , Auditoriu)
VALUES	(107, 101, 'Luni', '08:00', 202),
		(108, 101, 'Luni', '11:30',501),
		(119, 117, 'Luni', '13:30',501);

SELECT * 
FROM orarul
```

Result:

![six](https://user-images.githubusercontent.com/24621285/49031293-2b9a5a80-f16f-11e8-9118-513945132d31.PNG)


## Task 7
Write T-SQL instructions neccessary for populating the "orar" table for INF171 group, for Monday. T
he data to be inserted should be collected from the database tables 
lectie #1 (Ora ='08:00', Disciplina = 'Structuri de date si algoritmi', Prof esor ='Bivol Ion')
lectie #2 (Ora ='11 :30', Disciplina = 'Programe aplicative', Profesor ='Mircea Sorin')
lectie #3 (Ora ='13:00', Disciplina ='Baze de date', Profesor = 'Micu Elena') 

```
INSERT INTO  orarul(Id_Disciplina, Id_Profesor, Id_Grupa, Zi , Ora, Auditoriu)
VALUES (	
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Structuri de date si algoritmi'),		
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Bivol' AND Prenume_Profesor = 'Ion'),
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			 'Luni', '08:00', 101
		),
		(
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Programe aplicative'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Mircea' AND Prenume_Profesor = 'Sorin'),
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),		
			'Luni','11:30', 101 
		),
		(
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Baze de date'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Micu' AND Prenume_Profesor = 'Elena'),
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			'Luni','13:00', 101
		)

SELECT * 
FROM orarul
```
Result:

![seven](https://user-images.githubusercontent.com/24621285/49031290-2b01c400-f16f-11e8-9832-d62c926b2038.PNG)



## Task 8
Write the queries necessary for creating te indexes on tables from "University" database to ensure a higher performance to the queries from laboratory work number 4. Analyse the results.The indexes must be physically placed into userdatafgroup file group.
```

```



