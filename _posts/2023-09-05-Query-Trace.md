## Query Trace

The SQL Server Profiler GUI is often used to track down how an application is querying the database, but it's aging and can have unfortunate impact on database performance.

We can instead create the trace directly and then review the output at our leisure, so it is best used to dump a trace file to disk for a very brief period of time to test one specific thing.

Don't just leave this running, and try to avoid using it on a busy prod server, even without the overhead of the GUI involved.

Modern SQL Server also has Extended Events which can be a good replacement for this approach.

### The code!


```sql
/* Create a trace */
DECLARE
    @QueryStmt INT
  , @TraceID INT
  , @MaxFileSize BIGINT
  , @FilePath NVARCHAR(256);

SET @MaxFileSize = 1024 /* in MB */;

/* If you are writing from remote server to local drive, use UNC path and make sure server has write access to your network share */
SET @FilePath = N'D:\MSSQL\Trace\My_trace_' + CONVERT(CHAR(8), GETDATE(), 112) + '_' + REPLACE(CONVERT(VARCHAR(10), GETDATE(), 108), ':', ''); /* File extension .trc automatically added. Folder path must be pre-created. File name must not exist. */

EXEC @QueryStmt = sys.sp_trace_create
    @TraceID OUTPUT
  , 0
  , @FilePath /* File extension .trc automatically added. Folder path must be pre-created. File name must not exist. */
  , @MaxFileSize
  , NULL;

IF ( @QueryStmt <> 0 )
    GOTO error;

/* Set the events for the trace */

/*
10-RPC:Completed
11-RPC:Starting
12-SQL:BatchCompleted

sp_trace_setevent @trace_id, @event_id, @column_id, @on  
*/

EXEC sys.sp_trace_setevent @TraceID, 10, 1, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 6, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 11, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 12, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 13, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 15, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 16, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 17, 1;
EXEC sys.sp_trace_setevent @TraceID, 10, 35, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 1, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 6, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 11, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 12, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 13, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 15, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 16, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 17, 1;
EXEC sys.sp_trace_setevent @TraceID, 11, 35, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 1, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 6, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 11, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 12, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 13, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 15, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 16, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 17, 1;
EXEC sys.sp_trace_setevent @TraceID, 12, 35, 1;

/* done setting events */

/* Set the filters for the trace
sp_trace_setfilter @traceid, @columnid, @logical_operator, @comparison_operator, @value
*/

/* Ignore your own session */
DECLARE @MySPID INT;

SELECT
    @MySPID = @@SPID;

EXEC sys.sp_trace_setfilter @TraceID, 12, 0, 1, @MySPID;

/* Set database to trace */
DECLARE @DB_ID INT;

SELECT
    @DB_ID = databases.database_id
FROM sys.databases
WHERE databases.name = 'myDB';

EXEC sys.sp_trace_setfilter @TraceID, 3, 0, 0, @DB_ID;

/* look for specific query text */
EXEC sys.sp_trace_setfilter @TraceID, 1, 0, 6, N'% query text %';

/* ignore connection reset row spam */
EXEC sys.sp_trace_setfilter @TraceID, 1, 0, 1, N'exec sp_reset_connection ';

/* done setting filters */

/* Set the trace status to start */
EXEC sys.sp_trace_setstatus @TraceID, 1;

/* display trace id for reference. you'll need this to stop and shut down the trace. */
SELECT
    @TraceID AS TraceID_Started;

GOTO finish;

error:
SELECT
    @QueryStmt AS ErrorCode;

finish:
GO

/*

/* Set the trace status to stop */
DECLARE @TraceID INT = 2

EXEC sys.sp_trace_setstatus @TraceID, 0;

/* close trace and remove from server. trace must be stopped first. */
EXEC sys.sp_trace_setstatus @TraceID, 2;

SELECT @TraceID AS TraceID_Ended;

*/
```