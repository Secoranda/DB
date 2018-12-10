
# Laboratory Nr.11

## Objectives

DATABASE RECOVERY 

## Task 1

Create a Backup_labll file. Complete a full database backup of the university in this folder. The backup file is called exercitiull.bak. Write the respective SQL statement.

QUERRY:
```
EXEC sp_addumpdevice 'DISK', 'TASK1', 'C:\Users\Daniela\Desktop\LAB11\exercitiul1.bak'
GO

BACKUP DATABASE universitatea
TO TASK1 WITH FORMAT, NAME = N'universtitatea-Full Database backup'
GO
```
OUTPUT:

![task1](https://user-images.githubusercontent.com/24621285/49750859-a5c0f800-fcb4-11e8-8eb5-07650f8ec77c.PNG)


## Task 2

Write the instruction for a differential backup of the university database. The file of the backup copy is called the exercitiul2.bak.

QUERRY:
```
EXEC sp_addumpdevice 'DISK', 'TASK2','C:\Users\Daniela\Desktop\LAB11\exercitiul2.bak'
GO 
BACKUP DATABASE universitatea
TO TASK2 WITH FORMAT, NAME = N'universitatea-Differential Database Backup'
GO

```

OUTPUT:

![task2](https://user-images.githubusercontent.com/24621285/49750856-a5286180-fcb4-11e8-8750-c34b86b3cf82.PNG)


## Task 3
Write the instruction for a backup of the university's database transaction log. The backup file is called the exercitiul3.bak.

QUERRY:
```
EXEC sp_addumpdevice 'DISK', 'backup_Log', 'C:\Users\Daniela\Desktop\LAB11\exercitiul3.bak'

GO
BACKUP LOG universitatea TO backup_Log
GO
```
OUTPUT:


![task3](https://user-images.githubusercontent.com/24621285/49750857-a5c0f800-fcb4-11e8-8288-f29fcc8b3af4.PNG)
 
## Task 4
Perform the consecutive restoration of all backup copies created. Recovery must be done in a new university_labll database. . The new database files are in the BD_labll folder. Write the respective SQL statements

QUERRY:
```
IF EXISTS (SELECT * FROM master.sys.databases WHERE name='universitatea_lab11')
DROP DATABASE universitatea_lab11;
GO
  RESTORE DATABASE universitatea_lab11
  FROM DISK = 'C:\Users\Daniela\Desktop\LAB11\exercitiul1.bak'
  WITH MOVE 'universitatea' TO 'C:\Users\Daniela\Desktop\LAB11\BD_lab11\data.mdf',
  MOVE 'universitatea_log' TO 'C:\Users\Daniela\Desktop\LAB11\BD_lab11\log.ldf',
  NORECOVERY
GO
  RESTORE LOG universitatea_lab11
  FROM DISK = 'C:\Users\Daniela\Desktop\LAB11\exercitiul3.bak'
  WITH NORECOVERY
GO
RESTORE DATABASE universitatea_lab11
  FROM DISK = 'C:\Users\Daniela\Desktop\LAB11\exercitiul2.bak'
  WITH 
  NORECOVERY
GO
```
![task4 1](https://user-images.githubusercontent.com/24621285/49751835-2ed92e80-fcb7-11e8-86e8-6aa170dde0fd.PNG)
![task4 2](https://user-images.githubusercontent.com/24621285/49751836-2ed92e80-fcb7-11e8-9772-cb504b5f7135.PNG)
![tsk4 3](https://user-images.githubusercontent.com/24621285/49751837-2f71c500-fcb7-11e8-865d-21eca3ee3abd.PNG)

![taskss](https://user-images.githubusercontent.com/24621285/49751928-72cc3380-fcb7-11e8-9f38-58ec676bde75.PNG)

