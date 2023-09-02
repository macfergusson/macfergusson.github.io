## Dynamic SQL to Search All Tables and Columns In a Database For a Particular String

Don't do this. No, really, this is about the slowest thing you can possibly do to your relational database.

That being said, some times people just REALLY have a need to do something that is going to not scale well in performance to a large dataset.

### At Least Be Safe About It

```sql
USE [MyDB]
GO

SET NOCOUNT ON;
GO

DECLARE @SearchTerm NVARCHAR(100)
 , @TableName NVARCHAR(128)
 , @ColumnName NVARCHAR(128)
 , @SearchTermSafe NVARCHAR(104)
 , @sql NVARCHAR(MAX)

SET @SearchTerm = 'macfergusson';

DROP TABLE IF EXISTS #Results;

 CREATE TABLE #Results (
  ColumnName NVARCHAR(370)
  , ColumnValue NVARCHAR(3630)
  );

SET @TableName = '';
SET @SearchTermSafe = QUOTENAME('%' + @SearchTerm + '%', '''');

WHILE @TableName IS NOT NULL
BEGIN
 SET @ColumnName = ''
 SET @TableName = (
   SELECT MIN(QUOTENAME(s.name) + '.' + QUOTENAME(t.name))
   FROM sys.tables t
   INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
   WHERE type = 'U'
    AND is_ms_shipped = 0
    AND QUOTENAME(s.name) + '.' + QUOTENAME(t.name) > @TableName
   );

 WHILE (@TableName IS NOT NULL)
  AND (@ColumnName IS NOT NULL)
 BEGIN
  SET @ColumnName = (
    SELECT MIN(QUOTENAME(c.name))
    FROM sys.columns c
    WHERE object_id = object_id(@TableName)
     /* add logic to make smarter column filtering based on input search term */
	 AND system_type_id IN ( 56 , 106 , 167 , 175 , 231 , 239 )
     AND QUOTENAME(c.name) > @ColumnName
    )

  IF @ColumnName IS NOT NULL
  BEGIN
   SET @sql = 'SELECT ''' + @TableName + '.' + @ColumnName + ''', LEFT(' + @ColumnName + ', 3630) FROM ' + @TableName + ' WHERE ' + @ColumnName + ' LIKE ' + @SearchTermSafe

   INSERT INTO #Results
   EXEC sp_executesql @sql
  END
 END
END

SELECT ColumnName
 , ColumnValue
FROM #Results;

DROP TABLE IF EXISTS #Results;
```