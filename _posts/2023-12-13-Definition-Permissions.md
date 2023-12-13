## Uncovering the SQL Permission Conundrum: When ChatGPT's Oracle Fails in MS SQL

In the riveting world of MS SQL permissions, we decided to put ChatGPT's sagely advice to the test. 

### According to ChatGPT

> No, the VIEW DEFINITION permission in a database context does not grant the ability to see the queries inside a stored procedure (sproc). VIEW DEFINITION is a permission that allows a user to view the metadata and definition of an object within the database, such as views, stored procedures, and functions. This permission enables a user to see the structure and properties of the object but not the actual code or queries contained within it.
> To view the queries inside a stored procedure, you would typically need EXECUTE permission on the stored procedure. However, it's essential to understand that granting EXECUTE permission on a stored procedure allows a user to execute the procedure but doesn't necessarily grant the ability to view the source code or queries within it.
> If you want to allow users to view the code of a stored procedure, you may need to grant them ALTER PROCEDURE or ALTER ANY PROCEDURE permissions, but this should be done cautiously as it provides broader access and may pose security risks. Always follow the principle of least privilege, granting only the minimum permissions required for a user to perform their tasks.

If we ignore the inherent contradiction in that statement (what is a stored proc definition if not the queries it contains?) then you would assume that this privilege is nigh useless. We have to allow anyone to modify and execute the stored proc for them to have any visibility on what it can do.

But let's test it, shall we?

### The Setup

```sql
USE [master] ;

IF EXISTS ( SELECT * FROM [sys].[server_principals] AS [SP] WHERE [SP].[name] = 'testLogin' )
    BEGIN
        DROP LOGIN [testLogin] ;
    END ;

DROP DATABASE IF EXISTS [TestDB4] ;

CREATE LOGIN [testLogin] WITH PASSWORD = 'Test112asd13456.stuff' ;
GO

CREATE DATABASE [TestDB4] ;
GO

USE [TestDB4] ;
GO

CREATE TABLE [TestTable] ( [id] INT IDENTITY, [string] VARCHAR(10)) ;

INSERT INTO [dbo].[TestTable] ( [string] )
VALUES
( 'stuff' )
, ( 'things' ) ;
GO

CREATE OR ALTER PROCEDURE [TestProc]
AS
    BEGIN
        SELECT 'This is a stored procedure' AS [ColumnName] ;
    END ;
GO

CREATE USER [testUser] FOR LOGIN [testLogin] ;

ALTER ROLE [db_datareader] ADD MEMBER [testUser] ;
GO
```

Alrighty, we've got a nice little throwaway test case here that we can muck around with.

### The Test

Now, for our specific privilege question:

```sql
GRANT VIEW DEFINITION TO [testUser] ;

EXECUTE AS LOGIN = 'testLogin' ;

SELECT TOP 1
       *
FROM   [dbo].[TestTable] AS [A] ;

SELECT TOP 1
       *
FROM   [sys].[procedures] AS [P] ;

EXEC [sys].[sp_helptext] @objname = 'TestProc' ;

REVERT ;
GO
```

We execute some queries as the test user with limited access, and everything here runs smoothly. Strangely enough, we can even see the definition of the stored procedure!

Now let's strip away that privilege:
```sql
DENY VIEW DEFINITION TO [testUser] ;

EXECUTE AS LOGIN = 'testLogin' ;

SELECT TOP 1
       *
FROM   [dbo].[TestTable] AS [A] ;

SELECT TOP 1
       *
FROM   [sys].[procedures] AS [P] ;

EXEC [sys].[sp_helptext] @objname = 'TestProc' ;

REVERT ;
GO
```

There's the error you would expect from someone that is not allowed to view the internals of a stored proc!

It seems our SQL script was determined to shatter the illusions of ChatGPT's all-knowing oracle. 

In conclusion, as we navigate the maze of database permissions, let’s remember that while ChatGPT is an insightful guide, it's not the sole hero in our SQL epic. Real-world testing, like our little SQL script adventure, adds that extra pinch of reality to keep us on our toes. Cheers to the daring souls who dare to challenge the chatbot oracle and unravel the mysteries of MS SQL!

This blog post written with assistance from ChatGPT for extra irony.