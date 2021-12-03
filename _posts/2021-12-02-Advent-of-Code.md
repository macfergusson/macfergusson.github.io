## Advent of Code

[This is a fun little thing.](https://adventofcode.com/)

Choose any programming/scripting language, and solve the puzzles each day.

I am, obviously, trying to do them all in t-SQL. The first day's puzzles were no problem due to the joys of windowing functions. Do you know about LAG and LEAD?


<details>
  <summary>Spoiler warning Day 1 Part 1 Solution</summary>

```tsql
USE [TestDB];
GO

/****** Object:  StoredProcedure [dbo].[advent1_sp]    Script Date: 12/3/2021 1:36:23 AM ******/
SET ANSI_NULLS ON;
GO

SET QUOTED_IDENTIFIER ON;
GO

ALTER PROCEDURE [dbo].[advent1_sp]
AS
BEGIN
    SELECT SUM(DepthCount.DepthCompare)
    FROM
    (
        SELECT ID,
               Depth,
               DepthData.LastDepth,
               DepthData.NextDepth,
               CASE
                   WHEN Depth > LastDepth THEN
                       1
                   ELSE
                       0
               END AS DepthCompare
        FROM
        (
            SELECT ID,
                   Depth,
                   LAG(Depth) OVER (ORDER BY ID) LastDepth,
                   LEAD(Depth) OVER (ORDER BY ID) NextDepth
            FROM dbo.advent1
        ) AS DepthData
    ) DepthCount;
END;
```

</details>





<details>
  <summary>Spoiler warning Day 1 Part 2 Solution</summary>

```tsql
  
  USE [TestDB];
GO

/****** Object:  StoredProcedure [dbo].[advent1part2_sp]    Script Date: 12/3/2021 1:36:27 AM ******/
SET ANSI_NULLS ON;
GO

SET QUOTED_IDENTIFIER ON;
GO

ALTER PROC [dbo].[advent1part2_sp]
AS
BEGIN
    SELECT SUM(Agg.Increases)
    FROM
    (
        SELECT CASE
                   WHEN Group3 > LAG(Group3) OVER (ORDER BY Group3.ID) THEN
                       1
                   ELSE
                       0
               END Increases
        FROM
        (
            SELECT ID,
                   Depth,
                   Depth + LAG(Depth) OVER (ORDER BY ID) + LAG(Depth, 2) OVER (ORDER BY ID) AS Group3
            FROM dbo.advent1
        ) AS Group3
    ) Agg;
END;
  ```

</details>
