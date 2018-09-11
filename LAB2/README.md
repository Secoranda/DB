# Laboratory work Nr.2 "Creating and Maintaining the Database"
## Objectives
1. Instalation of the software
2. Ability to work with database software

## Task 1

Create a data base located physic in folder MyDocuments\Data, setting a 16MD primary base file extension with the 128 MD increment limit and the 64MD log with the 1024MB increment limit. For secondary files, define a new Default Filegroup, setting the 64MB of secondary files with the limit of 1024MB.

![lab2_ex1](https://user-images.githubusercontent.com/24621285/45372266-383df680-b5f5-11e8-807f-0aa615fe2e32.PNG)
*Picture NR.1 - first data base*


## Task 2
Create a database where the log file is physically placed on the MyDocuments \ Log map, the log file name in the operating system environment must be different from the logical one defined in the physical schema. It is important for the database to become obsolete from the logical one defined in the physical scheme. It is important that the database created is compatible with MS SQL Server 2017 and is accessible to only one user within a time frame.

![lab2_ex2](https://user-images.githubusercontent.com/24621285/45373112-468d1200-b5f7-11e8-9449-771d69439491.PNG)
\
*Picture Nr.2 - second data base*

## Task 3

Create the database maintenance plan in the task 1. The unused space of the database files must be removed when it reaches 2000Mb. The released space must be returned to the operating system. This operation must run every Friday at 00:00. The maintenance plan execution report must be saved in the MyDocuments \ SQL_event_logs dock. Initiate plan execution. After execution, check the results in the log file.

![lab2_ex3_1](https://user-images.githubusercontent.com/24621285/45373227-8c49da80-b5f7-11e8-96da-bb6e1ec51f63.PNG)
\
*Picture NR.3.1 - Maintenance plan implimentation*


![lab2_ex3_2](https://user-images.githubusercontent.com/24621285/45373228-8c49da80-b5f7-11e8-956e-09defd01618e.PNG)
\
*Picture NR.3.2 - Maintenance plan execution*


![lab2_ex3_3](https://user-images.githubusercontent.com/24621285/45373229-8c49da80-b5f7-11e8-859f-811dbb4aac49.PNG)
\
*Picture NR.3.3 - Maintenance plan execution report*

## Task 4

Create the database maintenance plan built in task 2. The name of the plan will be: "Re-indexing the Index". Under this plan, the system has to re-index the indexes only on the base tables (excluding the views) of all the schemas that have the database in question. The free space on the page must be 10%. Sorting indexes should be done in tempdb. After rebuilding, you must follow the collection of complete statistics about reconstructed indexes. The third step of the plan should be the task of deleting history about Backup-Restore operations that took place on SQL Server. You must delete the history that is older than 6 weeks. This plan must be executed every first Sunday of the month. Create the MyDocuments \ SQL_reports folder. The plan execution report must be added to this file. The mening process - to be logged in an extended way. Initiate plan execution. After execution, check the results in the generated log file.

![lab2_ex4_1](https://user-images.githubusercontent.com/24621285/45373380-ff535100-b5f7-11e8-8d31-50375b9763c0.PNG)
\
*Picture NR.4.1 - Maintenance plan implimentation*

![lab2_ex4_2](https://user-images.githubusercontent.com/24621285/45373381-ff535100-b5f7-11e8-9794-2cf7b7922122.PNG)
\
*Picture NR.4.3 - Maintenance plan execution*

![lab2_ex4_3](https://user-images.githubusercontent.com/24621285/45373382-ff535100-b5f7-11e8-9701-40c15e0af417.PNG)
\
*Picture NR.4.3 - Maintenance plan execution report*
