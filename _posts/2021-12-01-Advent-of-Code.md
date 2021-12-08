## Advent of Code Challenge

[This is a fun little thing.](https://adventofcode.com/)

Choose any programming/scripting language, and solve the puzzles each day.

I am, obviously, trying to do them all in t-SQL. The first day's puzzles were no problem due to the joys of windowing functions. Do you know about LAG and LEAD?


<details>
  
  <summary>Spoiler warning Day 1 Part 1 Solution</summary>

<pre><code>

USE [TestDB];
GO
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

</code></pre>

</details>





<details>
  
  <summary>Spoiler warning Day 1 Part 2 Solution</summary>

<pre><code>
  
  USE [TestDB];
GO


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


</code></pre>

</details>
