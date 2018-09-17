
# Laboratory Nr.3 
# "Creating and Modifying Tables in SQL Server Management Studio"
## Objectives
1. Defining columns and setting their basic properties;
2. Setting expanded column properties;
3. Setting primary keys and integrity constants
4. Changing the structure of a table

## Task 1 (Question)
Which from the numbers listed below can be entered in a decimal field (4.1)?
\
Answer: 
\
d) 116,2 -> 4 total digits with one digit after comma.

## Task 2 (Question)
Either [Col1] in the table below is type INT, and [Col2] is of the Decimal type (2.1):

![lab3pic](https://user-images.githubusercontent.com/24621285/45638523-18f00f00-bab6-11e8-8189-e71493b1f013.PNG)
\
*Picture Nr.1 - Table*

What kind of data must be [Col 3] to keep the result of the following Col1 * Col2 expression?
\
Answer: 
\
In case Col2 is also incrementing, the max number for decimal(2,1) will be 9.9.
Multiplying 9.9 with maxim value of int type, we'll get a decimal(12,1). 
So the maxim allowable number after multiply operation will be a decimal(12,1).



## Task 3
Create a database called the university with default properties. 
In this database, create 2 tables (groups, disciplines), the schemes of which are defined in theory.
\
Answer:
\
![lab3_4](https://user-images.githubusercontent.com/24621285/45639196-fced6d00-bab7-11e8-938b-d9366c3a76ad.PNG)
\
*Picture Nr.2.1 - Table 1 (Disciplines)*

![lab3_5](https://user-images.githubusercontent.com/24621285/45639197-fced6d00-bab7-11e8-8053-9a414fa5e603.PNG)
\
*Picture Nr.2.2 - Table 1 (Groups)*

## Task 4
Insert the records into the respective database tables.
\
Answer:
\
![lab3_1](https://user-images.githubusercontent.com/24621285/45639192-fc54d680-bab7-11e8-904f-c1e3e8bd2d96.PNG)
\
*Picture Nr.3.1 - Table 1 (Disciplines)*
\
![lab3_2](https://user-images.githubusercontent.com/24621285/45639194-fc54d680-bab7-11e8-9f3d-fbcdc675a029.PNG)
\
*Picture Nr.3.2 - Table 1 (Groups)*

## Conclusion
In this laboratory work was created 2 data bases and were inserted the needed data:

![lab3_3 png](https://user-images.githubusercontent.com/24621285/45639195-fced6d00-bab7-11e8-8704-1c03c422ad0e.PNG)
\
*Picture Nr.4 - Data Bases*

See attached the Back Up File in the same folder.
