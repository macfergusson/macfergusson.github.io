### Quick Hit List of Best Practices for SQL Queries

#### Writing Efficient and Secure SQL Queries

**Parameterized Queries:** Prevent SQL injection by using parameterized queries for dynamic SQL commands.

**Select Required Columns Only:** Specify only necessary columns instead of using `SELECT *` to improve performance.

**Joins Over Subqueries:** Prefer joins over subqueries for better optimization and performance. Avoid large lists of values fed to IN clauses.

**Effective Indexing:** Use indexes strategically to enhance query performance but avoid excessive indexing to prevent slowdowns in data modification operations. Around 5 indexes per table is a good guideline.

**Avoid Functions on Indexed Columns:** Refrain from using functions in the WHERE clause or in JOIN conditions.

**Batch Operations:** Use batching for large-scale inserts and updates to reduce server load.

**Minimize Cursor Use:** Avoid cursors for better performance, opting for set-based operations where possible.

**SET NOCOUNT ON:** Start stored procedures with `SET NOCOUNT ON;` to improve performance by not returning row affected messages.

#### UI and Data Handling Considerations

**UI Feedback for User Input:** Ensure the UI adequately informs users about input restrictions and validations. If they can only search for a 10 character string, does it warn them when they put in 12 characters?

**Optimized Views:** Be cautious with nested views and Common Table Expressions (CTEs) as they can impact query efficiency. Ensure views don't carry unnecessary objects that aren't being used for your purpose, as this introduces extra baggage to the execution plan.

**Temporary Tables:** Temp tables are session-scoped; explicit dropping is not required, but isn't likely to hurt.

**Efficient String Searching:** Favor "starts with" logic over broad wildcard searches for better performance. Leading wildcards are the enemy of efficient querying.

**Use Range Comparisons:** Prefer inequalities for numeric data types like dates or monetary values over string searches.

**Avoid Fuzzy Matching on Numeric Data:** If product requirements permit, do not use fuzzy matching or string searches for numeric comparisons.

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

**Use Table Variables Wisely:** Prefer table variables for small datasets and temp tables for larger ones. Around 50 rows should be your upper limit for a table variable.

**Reorder Joins and Applies:** Optimize join order by starting with the most restrictive conditions where possible.

#### Developer Responsibility

**Question and Push Back:** Keep in mind that product owners and project managers aren't supposed to understand what makes efficient and fast code in the app and in the database. It isn't their job, it's our job. So it is our responsibility to question things and some times even push back against feature suggestions that will make things worse instead of better.

By incorporating these practices, developers can write SQL queries that are not only efficient and secure but also maintainable and scalable. Regularly reviewing and optimizing SQL code based on these guidelines can significantly enhance database and application performance.