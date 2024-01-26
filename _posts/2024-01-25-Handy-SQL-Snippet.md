# The Power of Dynamic SQL - A Quick Look at a Useful T-SQL Snippet

When running on a single logical database spread across multiple physical databases on the same server, querying multiple databases efficiently can be a challenging task. Today, I want to share and explain a T-SQL snippet that simplifies this process, showcasing the power of dynamic SQL in database management.

### The T-SQL Snippet Explained

Our sample uses a combination of dynamic SQL and a system stored procedure. Let's break it down:

```sql
DECLARE @command VARCHAR(1000);

SELECT
    @command = 'IF ''?'' LIKE ''myDB%''
BEGIN 
USE ?
SELECT        COUNT(*)
FROM        [dbo].[myTable] AS [TargetTable]   
END';

EXEC [sys].[sp_MSforeachdb] @command;
```

#### Key Components:

1. **Variable Declaration**: `DECLARE @command VARCHAR(1000);` 
   - We start by declaring a variable `@command` that will store our dynamic SQL command.

2. **Dynamic SQL Command**: 
   - The command string is set to execute a conditional block (`IF...BEGIN...END`).
   - It checks if the current database (`?`) matches a naming pattern (`myDB%`).
   - If the condition is true, it switches to that database (`USE ?`) and runs a query to count rows in a specified table (`[dbo].[myTable]`).

3. **Execution with `sp_MSforeachdb`**:
   - The magic happens with `EXEC [sys].[sp_MSforeachdb] @command;`.
   - `sp_MSforeachdb` is a system stored procedure that iterates through each database on the SQL Server instance.
   - The `@command` is executed for each database where the condition is met.
   - Note that this system proc is NOT A DOCUMENTED FEATURE, and thus is subject to unannounced changes. However, it has been stable for a long time.
   
### The Usefulness of This Snippet

1. **Batch Operations Across Multiple Databases**:
   - Perfect for scenarios where you have multiple databases with similar structures or naming conventions.
   - Reduces the need to manually change database context or write repetitive scripts.

2. **Conditional Execution**:
   - The `IF` condition ensures that the command only runs on databases that match a specific pattern.
   - This adds a layer of safety and precision, avoiding unintended operations on irrelevant databases.

3. **Efficiency and Time-Saving**:
   - Automates what would otherwise be a tedious and error-prone process.
   - Especially useful in large-scale environments with many databases.

4. **Dynamic SQL Flexibility**:
   - The snippet can be modified to perform different types of operations, not just count rows.
   - Dynamic SQL allows for complex and adaptable scripting.

### Conclusion

This T-SQL snippet is a powerful tool in a database developer's arsenal, particularly for those managing multiple databases. It demonstrates the efficiency and flexibility of using dynamic SQL combined with system stored procedures. By automating repetitive tasks and ensuring operations are performed only on relevant databases, it saves time, reduces errors, and enhances overall database management.

Remember, with great power comes great responsibility. Always test your scripts in a safe environment before applying them to production databases.