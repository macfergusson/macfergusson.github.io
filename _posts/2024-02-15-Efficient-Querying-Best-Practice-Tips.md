### Quick Hit List of Best Practices for SQL Queries

#### Writing Efficient and Secure SQL Queries

**Parameterized Queries:** Prevent SQL injection by using parameterized queries for dynamic SQL commands.
Instead of:
```csharp
string query = "SELECT * FROM users WHERE username = '" + username + "'";
```
Use parameterized queries:
```csharp
string query = "SELECT * FROM users WHERE username = @Username";
SqlCommand command = new SqlCommand(query, connection);
command.Parameters.AddWithValue("@Username", username);
```

**Select Required Columns Only:** Always explicitly specify columns instead of using `SELECT *`. Don't `SELECT` columns that aren't being used.

**Joins Over Subqueries:** Prefer joins over subqueries for better optimization and performance. Avoid large lists of values fed to IN clauses.
Instead of using a subquery:
```sql
SELECT * FROM Orders WHERE CustomerID IN (SELECT CustomerID FROM Customers WHERE City = 'London');
```
Use a join:
```sql
SELECT Orders.* FROM Orders JOIN Customers ON Orders.CustomerID = Customers
```

**Effective Indexing:** Use indexes strategically to enhance query performance but avoid excessive indexing to prevent slowdowns in data modification operations. Around 5 indexes per table is a good starting guideline, and anything beyond that requires careful consideration.

**Avoid Functions on Indexed Columns:** Refrain from using functions in the WHERE clause or in JOIN conditions.

**Batch Operations:** Use batching for large-scale inserts and updates to reduce server load. Relational databases are designed for set based operations on data.

**Minimize Cursor Use:** Avoid cursors for better performance, opting for set-based operations where possible.

**SET NOCOUNT ON:** Start stored procedures with `SET NOCOUNT ON;` to improve performance by not returning "row affected" messages.
```sql
(615 rows affected)
(1 rows affected)
(15 rows affected)
```

#### UI and Data Handling Considerations

**UI Feedback for User Input:** Ensure the UI adequately informs users about input restrictions and validations. If they can only search for a 10 character string, does it warn them when they put in 12 characters? Limitations of parameters in stored procs should be reflected in the UI if possible, preventing unexpected behaviors.

**Optimized Views:** Be cautious with nested views and Common Table Expressions (CTEs) as they can impact query efficiency. Ensure views don't carry unnecessary objects that aren't being used for your purpose, as this introduces extra baggage to the execution plan.

**Temporary Tables:** Temp tables are session-scoped; explicit dropping is not required, but isn't likely to hurt.

**Efficient String Searching:** Favor "starts with" logic over broad wildcard searches for better performance. Leading wildcard string searches are the enemy of efficient querying.
Instead of:
```sql
SELECT * FROM Employees WHERE Name LIKE '%Doe%';
```
Use:
```sql
SELECT * FROM Employees WHERE Name LIKE 'Doe%';
```

**Use Range Comparisons:** Prefer range inequality searches for numeric data types like dates or monetary values instead of string searches. If we can accomplish what we need with equality comparisons, even better!
```sql
SELECT * FROM Orders WHERE OrderDate >= '2020-01-01' AND OrderDate <= '2020-12-31';
```

#### SQL Coding Practices

**Explicit Field Selection:** Always specify fields in SELECT statements rather than using `SELECT *`.

**Specify JOIN Types and Conditions:** Clearly define JOIN types and conditions to avoid ambiguity.

**Alias Tables:** Use table aliases for clearer and more concise SQL code, but name them something legible. Always qualify your columns with the object they are referencing.

**Transaction Management:** Understand the use of `SET XACT_ABORT` for expected transaction behavior.

**Data Types and Comparisons:** Ensure proper data types for all fields and match parameter/variable data types to column types to avoid performance issues from implicit conversions.

**Minimize Function Use:** Avoid relying on functions within queries to prevent performance degradation.

#### Performance and Reliability

**Avoid NOLOCK/READ UNCOMMITTED:** Remove these hints to ensure data consistency.

**Include ELSE in CASE Statements:** Always handle unexpected results in CASE statements.

**Use Table Variables Wisely:** Prefer table variables for small datasets and temp tables for larger ones. Consider around 50 rows of data to be that breakpoint.

**Reorder Joins and Applies:** Optimize join order by starting with the most restrictive conditions first, where possible.

#### Developer Responsibility

**Question and Push Back:** Keep in mind that product owners and project managers aren't supposed to understand what makes efficient and fast code in the app and in the database. It isn't their job, it's our job. So it is our responsibility to question things and some times even push back against feature suggestions that will make things worse instead of better.

By incorporating these practices, developers can write SQL queries that are not only efficient and secure but also maintainable and scalable.