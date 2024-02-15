### Quick Hit List of Best Practices for SQL Queries

#### Writing Efficient and Secure SQL Queries

1. **Parameterized Queries:** Prevent SQL injection by using parameterized queries for dynamic SQL commands.

2. **Select Required Columns Only:** Specify only necessary columns instead of using `SELECT *` to improve performance.

3. **Joins Over Subqueries:** Prefer joins over subqueries for better optimization and performance.

4. **Effective Indexing:** Use indexes strategically to enhance query performance but avoid excessive indexing to prevent slowdowns in data modification operations.

5. **Avoid Functions on Indexed Columns:** Refrain from using functions on indexed columns in the WHERE clause to maintain index efficiency.

6. **Batch Operations:** Use batching for large-scale inserts and updates to reduce server load.

7. **SET NOCOUNT ON:** Start stored procedures with `SET NOCOUNT ON;` to improve performance by not returning row affected messages.

8. **Minimize Cursor Use:** Avoid cursors for better performance, opting for set-based operations where possible.

#### UI and Data Handling Considerations

9. **UI Feedback for User Input:** Ensure the UI adequately informs users about input restrictions and validations.

10. **Optimized Views:** Be cautious with nested views and Common Table Expressions (CTEs) as they can impact query efficiency. Ensure views don't carry unnecessary data.

11. **Temporary Tables:** Temp tables are session-scoped; explicit dropping is not required but does no harm.

12. **Efficient String Searching:** Favor "starts with" logic over broad wildcard searches for better performance.

13. **Use Range Comparisons:** Prefer inequalities for numeric data types like dates or monetary values over string searches.

14. **Avoid Fuzzy Matching on Numeric Data:** If product requirements permit, do not use fuzzy matching or string searches for numeric comparisons.

#### SQL Coding Practices

15. **Readable Formatting:** Use whitespace and line breaks to enhance query readability.

16. **Explicit Field Selection:** Always specify fields in SELECT statements rather than using `SELECT *`.

17. **Specify JOIN Types and Conditions:** Clearly define JOIN types and conditions to avoid ambiguity.

18. **Alias Tables:** Use table aliases for clearer and more concise SQL code.

19. **Transaction Management:** Understand the use of `SET XACT_ABORT` for expected transaction behavior.

20. **Data Types and Comparisons:** Ensure proper data types for all fields and match parameter/variable data types to column types to avoid performance issues.

21. **Minimize Function Use:** Avoid relying on functions within queries to prevent performance degradation.

#### Performance and Reliability

22. **Avoid NOLOCK/READ UNCOMMITTED:** Remove these hints to ensure data consistency.

23. **Include ELSE in CASE Statements:** Always handle unexpected results in CASE statements.

24. **Use Table Variables Wisely:** Prefer table variables for small datasets and temp tables for larger ones.

25. **Avoid SQL Anti-patterns:** This includes avoiding implicit type conversions, large IN clauses, non-parameterized queries, improper transaction management, non-SARGable predicates, and leading wildcard LIKE comparisons.

26. **Reorder Joins and Applies:** Optimize join order by starting with the most restrictive conditions.

#### Developer Responsibility

27. **Question and Push Back:** Developers should question and sometimes push back against feature suggestions that compromise efficiency or performance, as optimizing code is their responsibility, not that of product owners or project managers.

By incorporating these practices, developers can write SQL queries that are not only efficient and secure but also maintainable and scalable. Regularly reviewing and optimizing SQL code based on these guidelines can significantly enhance database and application performance.