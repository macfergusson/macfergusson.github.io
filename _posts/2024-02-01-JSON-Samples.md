# OPENJSON - What do you do when the first normal form is ignored?

The most fundamental rule of database design for OLTP workloads is to follow the principles of normalization. 

Everything else grows out of understanding something about this starting point.

First normal form (1NF) is a property of a relation in a relational database. 

A relation is in first normal form if and only if no attribute domain has relations as elements.

What does this mean in more casual English? Simply, a single column in a table should not store multiple values in a single row. This can be thought of as storing a table as a value.

One of the most common culprits of this violation is the storing of JSON or XML in a relational database, as the very nature of those is to package multiple elements of data in to a single string.

Unfortunately, we all have to work within the constraints of our circumstances, and some times we are required to do things that are objectively not a good relational database design.

So, then what? If you are forced in to storing data as raw JSON in SQL Server, it won't be long before someone needs to query against that data. Ideally, you would only ever insert it and retrieve it whole, but, again, once it exists, it will eventually be queried. (Hence my hard stance that it is a terrible idea.)

But, if we must, we must. And so, SQL Server has the OPENJSON function.

### T-SQL Samples and Demo

```sql
DECLARE @json NVARCHAR(2048) = N'{
   "String_value": "John",
   "DoublePrecisionFloatingPoint_value": 45,
   "DoublePrecisionFloatingPoint_value": 2.3456,
   "BooleanTrue_value": true,
   "BooleanFalse_value": false,
   "Null_value": null,
   "Array_value": ["a","r","r","a","y"],
   "Object_value": {"obj":"ect"}
}';

SELECT * FROM OPENJSON(@json);
```

Note that as far as the database is concerned, this is a single large string of text data. It is the act of passing it in to the OPENJSON function that allows us to query specific elements out of it.

```sql
DECLARE @json NVARCHAR(4000) = N'{  
      "path": {  
            "to":{  
                 "sub-object":["en-GB", "en-UK","de-AT","es-AR","sr-Cyrl"]  
                 }  
              }  
 }';

SELECT [key], value
FROM OPENJSON(@json,'$.path.to."sub-object"')
```

```sql
DECLARE @json NVARCHAR(MAX) = N'[  
  {  
    "Order": {  
      "Number":"SO43659",  
      "Date":"2011-05-31T00:00:00"  
    },  
    "AccountNumber":"AW29825",  
    "Item": {  
      "Price":2024.9940,  
      "Quantity":1  
    }  
  },  
  {  
    "Order": {  
      "Number":"SO43661",  
      "Date":"2011-06-01T00:00:00"  
    },  
    "AccountNumber":"AW73565",  
    "Item": {  
      "Price":2024.9940,  
      "Quantity":3  
    }  
  }
]'  
   
SELECT *
FROM OPENJSON ( @json )  
WITH (   
              Number   VARCHAR(200)   '$.Order.Number',  
              Date     DATETIME       '$.Order.Date',  
              Customer VARCHAR(200)   '$.AccountNumber',  
              Quantity INT            '$.Item.Quantity',  
              [Order]  NVARCHAR(MAX)  AS JSON  
 )
```

```sql
CREATE TABLE JsonTable (
    ID INT IDENTITY(1,1),
    JsonColumn NVARCHAR(MAX)
);

INSERT INTO JsonTable (JsonColumn) 
VALUES (N'[
    {
        "item": ["value1", "value2"]
    },
    {
        "item": ["value3", "value4"]
    }
]');

SELECT  *
FROM  JsonTable jt;

SELECT  *
FROM 
    JsonTable jt
CROSS APPLY 
    OPENJSON(jt.JsonColumn) 
    WITH (item NVARCHAR(MAX) AS JSON) AS outer_applied;
	
SELECT  *
FROM 
    JsonTable jt
CROSS APPLY 
    OPENJSON(jt.JsonColumn) 
    WITH (item NVARCHAR(MAX) AS JSON) AS outer_applied
CROSS APPLY 
    OPENJSON(outer_applied.item) AS j;
	
SELECT 
    jt.ID
    , j.value AS itemValue
FROM 
    JsonTable jt
CROSS APPLY 
    OPENJSON(jt.JsonColumn) 
    WITH (item NVARCHAR(MAX) AS JSON) AS outer_applied
CROSS APPLY 
    OPENJSON(outer_applied.item) AS j;
```

If this link is still valid, all of the above statements are pre-set and runnable:
https://dbfiddle.uk/o4veVzFs

### Conclusion

Note that these will likely all end up being table scans in your query plan, so it can rapidly reach unpleasant levels of performance drag. If at all possible, filter out your rows of JSON based on some other properly implemented column first, such as a date range to narrow it down.

Another consideration is that you can possibly convince the SQL Server engine to make use of an index on a calculated column that is based on a particular OPENJSON query. This will, of course, entail slower writes to as the index will need to be maintained based on changes in the data, but we're already throwing good performance considerations out the window here. Sacrifices must be made.