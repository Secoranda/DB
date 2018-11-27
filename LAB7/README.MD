

# Laboratory Nr.7

## Objectives
Diagrams, Schemes and Synonyms.

## Task 1-5
1. Create the diagram of university database, that will include the following tables: Profesori, Studenti, Studenti_reusita, Grupe, Discipline.
2. Add the referential constraints with tables studenti and profesori for columns Sef_grupa and Prof_indrumator from table grupe
3. Add the table orarul to the diagram.
4. Add secondary keys to the table orarul.
5. Also define FK-PK constraints to the table orarul

Result:

Task 4:
![four1](https://user-images.githubusercontent.com/24621285/49070101-8dbc9380-f233-11e8-839c-471aa79e5907.PNG)
![four2](https://user-images.githubusercontent.com/24621285/49070214-cf4d3e80-f233-11e8-8921-b08fa63d82c8.PNG)

Task 5:

![five](https://user-images.githubusercontent.com/24621285/49070256-ebe97680-f233-11e8-9946-587927a63a9c.PNG)
















## Task 6
Create 3 new schemas in the universitatea database: cadre_didactice, plan_studii and studenti. Transfer the table profesori to the schema cadre_didactice, tables orarul, discipline to the schema plan_studii, and the tables studenti, studenti_reusita to the schema studenti

# Query
 ```
 ALTER SCHEMA cadre_didactice TRANSFER dbo.profesori
ALTER SCHEMA plan_studii TRANSFER dbo.orarul
ALTER SCHEMA plan_studii TRANSFER dbo.discipline
ALTER SCHEMA studenti TRANSFER dbo.studenti
ALTER SCHEMA studenti TRANSFER dbo.studenti_reusita
 ```
 
 Result:
 
 ![six](https://user-images.githubusercontent.com/24621285/49069971-40d8bd00-f233-11e8-9c82-3e73920f96ad.PNG)









 ## Task 7
 Modify 3 querries from the 4th lab with renamed table names.
 
 # Query
 ```
 
SELECT * 
FROM studenti.studenti
WHERE Nume_Student LIKE '%u'

SELECT Disciplina, AVG(Nota) AS AvgMark
FROM plan_studii.discipline AS C, studenti.studenti_reusita AS D
WHERE C.Id_Disciplina = D.Id_Disciplina and
Tip_Evaluare = 'Examen'
GROUP BY Disciplina
HAVING AVG(Nota) >7.0
ORDER BY Disciplina DESC

SELECT Cod_Grupa, COUNT(DISTINCT Id_Student) AS Studenti_in_Grupa
FROM grupe AS C
INNER JOIN studenti.studenti_reusita AS D ON C.Id_Grupa = D.Id_Grupa
GROUP BY Cod_Grupa HAVING COUNT(DISTINCT Id_Student) > 24
  ```
   ## Task 8
Create synonyms to simplify the previous querries.
 # Query
 ```
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
Result:

![eight1](https://user-images.githubusercontent.com/24621285/49069976-42a28080-f233-11e8-9bcc-5a2283fc6e56.PNG)









