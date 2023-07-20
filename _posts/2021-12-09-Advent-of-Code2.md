## Advent of Code Challenge Day 3

Been a bit busy, so catching up on the puzzles.

### --- Day 3: Binary Diagnostic ---
The submarine has been making some odd creaking noises, so you ask it to produce a diagnostic report just in case.

The diagnostic report (your puzzle input) consists of a list of binary numbers which, when decoded properly, can tell you many useful things about the conditions of the submarine. The first parameter to check is the power consumption.

You need to use the binary numbers in the diagnostic report to generate two new binary numbers (called the gamma rate and the epsilon rate). The power consumption can then be found by multiplying the gamma rate by the epsilon rate.

Each bit in the gamma rate can be determined by finding the most common bit in the corresponding position of all numbers in the diagnostic report. For example, given the following diagnostic report:

00100

11110

10110

10111

10101

01111

00111

11100

10000

11001

00010

01010

Considering only the first bit of each number, there are five 0 bits and seven 1 bits. Since the most common bit is 1, the first bit of the gamma rate is 1.

The most common second bit of the numbers in the diagnostic report is 0, so the second bit of the gamma rate is 0.

The most common value of the third, fourth, and fifth bits are 1, 1, and 0, respectively, and so the final three bits of the gamma rate are 110.

So, the gamma rate is the binary number 10110, or 22 in decimal.

The epsilon rate is calculated in a similar way; rather than use the most common bit, the least common bit from each position is used. So, the epsilon rate is 01001, or 9 in decimal. Multiplying the gamma rate (22) by the epsilon rate (9) produces the power consumption, 198.

Use the binary numbers in your diagnostic report to calculate the gamma rate and epsilon rate, then multiply them together. What is the power consumption of the submarine? (Be sure to represent your answer in decimal, not binary.)

<details>
<summary>Spoiler warning Day 3 Part 1 Solution</summary>
<pre><code>
USE TestDB;
GO

DROP TABLE IF EXISTS advent3;

CREATE TABLE advent3
(
    RecordID INT IDENTITY,
    DiagString CHAR(12)
);
GO
;
WITH DiagColumns
AS (SELECT SUBSTRING(DiagString, 1, 1) AS Col1,
           SUBSTRING(DiagString, 2, 1) AS Col2,
           SUBSTRING(DiagString, 3, 1) AS Col3,
           SUBSTRING(DiagString, 4, 1) AS Col4,
           SUBSTRING(DiagString, 5, 1) AS Col5,
           SUBSTRING(DiagString, 6, 1) AS Col6,
           SUBSTRING(DiagString, 7, 1) AS Col7,
           SUBSTRING(DiagString, 8, 1) AS Col8,
           SUBSTRING(DiagString, 9, 1) AS Col9,
           SUBSTRING(DiagString, 10, 1) AS Col10,
           SUBSTRING(DiagString, 11, 1) AS Col11,
           SUBSTRING(DiagString, 12, 1) AS Col12
    FROM advent3),
     ParseResult
AS (SELECT CASE
               WHEN ColumnSums.Col1Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol1,
           CASE
               WHEN ColumnSums.Col1Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol1,
           CASE
               WHEN ColumnSums.Col2Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol2,
           CASE
               WHEN ColumnSums.Col2Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol2,
           CASE
               WHEN ColumnSums.Col3Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol3,
           CASE
               WHEN ColumnSums.Col3Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol3,
           CASE
               WHEN ColumnSums.Col4Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol4,
           CASE
               WHEN ColumnSums.Col4Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol4,
           CASE
               WHEN ColumnSums.Col5Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol5,
           CASE
               WHEN ColumnSums.Col5Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol5,
           CASE
               WHEN ColumnSums.Col6Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol6,
           CASE
               WHEN ColumnSums.Col6Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol6,
           CASE
               WHEN ColumnSums.Col7Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol7,
           CASE
               WHEN ColumnSums.Col7Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol7,
           CASE
               WHEN ColumnSums.Col8Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol8,
           CASE
               WHEN ColumnSums.Col8Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol8,
           CASE
               WHEN ColumnSums.Col9Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol9,
           CASE
               WHEN ColumnSums.Col9Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol9,
           CASE
               WHEN ColumnSums.Col10Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol10,
           CASE
               WHEN ColumnSums.Col10Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol10,
           CASE
               WHEN ColumnSums.Col11Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol11,
           CASE
               WHEN ColumnSums.Col11Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol11,
           CASE
               WHEN ColumnSums.Col12Sum < 500 THEN
                   '0'
               ELSE
                   '1'
           END AS GammaCol12,
           CASE
               WHEN ColumnSums.Col12Sum < 500 THEN
                   '1'
               ELSE
                   '0'
           END AS EpsilonCol12
    FROM
    (
        SELECT SUM(CONVERT(TINYINT, DiagColumns.Col1)) AS Col1Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col2)) AS Col2Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col3)) AS Col3Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col4)) AS Col4Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col5)) AS Col5Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col6)) AS Col6Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col7)) AS Col7Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col8)) AS Col8Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col9)) AS Col9Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col10)) AS Col10Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col11)) AS Col11Sum,
               SUM(CONVERT(TINYINT, DiagColumns.Col12)) AS Col12Sum
        FROM DiagColumns
    ) AS ColumnSums )
SELECT ParseResult.GammaCol1 + ParseResult.GammaCol2 + ParseResult.GammaCol3 + ParseResult.GammaCol4
       + ParseResult.GammaCol5 + ParseResult.GammaCol6 + ParseResult.GammaCol7 + ParseResult.GammaCol8
       + ParseResult.GammaCol9 + ParseResult.GammaCol10 + ParseResult.GammaCol11 + ParseResult.GammaCol12 AS Gamma,
	   --941
       
	   ParseResult.EpsilonCol1 + ParseResult.EpsilonCol2 + ParseResult.EpsilonCol3 + ParseResult.EpsilonCol4
       + ParseResult.EpsilonCol5 + ParseResult.EpsilonCol6 + ParseResult.EpsilonCol7 + ParseResult.EpsilonCol8
       + ParseResult.EpsilonCol9 + ParseResult.EpsilonCol10 + ParseResult.EpsilonCol11 + ParseResult.EpsilonCol12 AS Epsilon
	   --3154
FROM ParseResult;

SELECT 941 * 3154;

</code></pre>
</details>