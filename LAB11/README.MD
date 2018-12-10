
# Laboratory Nr.11

## Objectives
CREATING AND USING TRIGGERS

## Task 1

Create a Backup_labll file. Complete a full database backup of the university in this folder. The backup file is called exercitiull.bak. Write the respective SQL statement.

QUERRY:
```
EXEC sp_addumpdevice 'DISK', 'TASK1', 'C:\Users\User\Desktop\LAB11\exercitiul1.bak'
GO

BACKUP DATABASE universitatea
TO TASK1 WITH FORMAT, NAME = N'universtitatea-Full Database backup'
GO
```

## Task 2

Write the instruction for a differential backup of the university database. The file of the backup copy is called the exercitiul2.bak.

QUERRY:
```
EXEC sp_addumpdevice 'DISK', 'TASK2','C:\Users\User\Desktop\LAB11\exercitiul2.bak'
GO 
BACKUP DATABASE universitatea
TO TASK2 WITH FORMAT, NAME = N'universitatea-Differential Database Backup'
GO

```

## Task 3
Write the instruction for a backup of the university's database transaction log. The backup file is called the exercitiul3.bak.

QUERRY:
```
EXEC sp_addumpdevice 'DISK', 'backup_Log', 'C:\Users\User\Desktop\LAB11\exercitiul3.bak'

GO
BACKUP LOG universitatea TO backup_Log
GO
```

## Task 4
Perform the consecutive restoration of all backup copies created. Recovery must be done in a new university_labll database. . The new database files are in the BD_labll folder. Write the respective SQL statements

QUERRY:
```
IF EXISTS (SELECT * FROM master.sys.databases WHERE name='universitatea_lab11')
DROP DATABASE universitatea_lab11;
GO
  RESTORE DATABASE universitatea_lab11
  FROM DISK = 'C:\Users\User\Desktop\LAB11\exercitiul1.bak'
  WITH MOVE 'universitatea' TO 'C:\Users\User\Desktop\LAB11\BD_lab11\data.mdf',
  MOVE 'universitatea' TO 'C:\Users\User\Desktop\LAB11\BD_lab11\log.ldf',
  NORECOVERY
GO
  RESTORE LOG universitatea_lab11
  FROM DISK = 'C:\Users\User\Desktop\LAB11\exercitiul3.bak'
  WITH NORECOVERY
GO
RESTORE DATABASE universitatea_lab11
  FROM DISK = 'C:\Users\User\Desktop\LAB11\exercitiul2.bak'
  WITH 
  NORECOVERY
GO
```
