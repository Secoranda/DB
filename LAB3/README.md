
# Laboratory Nr.3 "Creating and Modifying Tables in SQL Server Management Studio"
## Objectives
1. Defining columns and setting their basic properties;
2. Setting expanded column properties;
3. Setting primary keys and integrity constants
4. Changing the structure of a table

## Task 1 (Question)
Which from the numbers listed below can be entered in a decimal field (4.1)?
Answer: d) 116,2 -> 4 total digits with one digit after comma.

## Task 2 (Question)
Either [Col1] in the table below is type INT, and [Col2] is of the Decimal type (2.1):

![lab3pic](https://user-images.githubusercontent.com/24621285/45638523-18f00f00-bab6-11e8-8189-e71493b1f013.PNG)
*Picture Nr.1 - Table*

What kind of data must be [Col 3] to keep the result of the following Col1 * Col2 expression?
Answer: In case Col2 is also incrementing, the max number for decimal(2,1) will be 9.9.
Multiplying 9.9 with maxim value of int type, we'll get a decimal(12,1). 
So the maxim allowable number after multiply operation will be a decimal(12,1).



## Task 3
Create a database called the university with default properties. 
In this database, create 2 tables (groups, disciplines), the schemes of which are defined in theory.

Answer:


## Task 4

