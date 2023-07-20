## What Broke Today?

Dealing with performance issues in database systems can be quite tricky, especially when it involves read-only secondaries, cached execution plans, and ORM queries.

I'll try to provide some insights and tips based on a situation I encountered recently.

This was a tricky one to pin down. We were seeing error logs for ORM queries against a database object in one specific app database that people believed was a simple single table query, but only for specific clients in specific cases, and we couldn't catch the query misbehaving on the database host. However, it turned out this object was actually a view masking significant complexity, and it ran against our read-only replica.

Once I was able to catch the sessions running long and hitting the thirty-second timeout (did you know about this default in .NET calls to the database?) on the secondary replica, I was able to isolate an sql_handle and clear out the plan cache for that particular query without flushing everything in cache for the entire database or, worse, the whole server.

> MS Timeout documentation:
> - Troubleshoot Query Timeouts: [link](https://learn.microsoft.com/en-us/troubleshoot/sql/database-engine/performance/troubleshoot-query-timeouts)
> - SqlCommand.CommandTimeout Property: [link](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlcommand.commandtimeout?view=dotnet-plat-ext-7.0)

Some code snippets employed:

```sql
[sp_WhoIsActive]
    @filter = '<relevant sql login>'
  , @filter_type = 'login' 
  , @get_plans = 2 
  , @get_additional_info = 1 /* a way to get sql_handle for active queries */
GO

SELECT plan_handle, st.text  /* a way to see all current plans in cache and retrieve plan handles */
FROM sys.dm_exec_cached_plans
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS st
WHERE text LIKE N'%object_name%';
GO
  
DBCC FREEPROCCACHE(<sql_handle>)
GO
```

> Recommended tool: [Who is Active](http://whoisactive.com/)

### The Read-Only Replica Conundrum

In a general sense, this is a known issue with complex queries where, as the data and statistics change over time, the cached plan no longer becomes the most efficient option, and a new plan is needed for a particular query and set of parameters. This is related to the concept of parameter sniffing and the struggles that people often have understanding how that impacts things. However, on a read-only secondary replica, those stale plans don't necessarily get automatically removed and recreated the way you would expect from interacting with the primary replica database.

In this case, it's the same problem, however not as easy to find as other cases we've had, because normally this comes up with a stored procedure that we can verify is running poorly by manually executing it on the read-only node. Since this instead is client code dynamic SQL calling a view, the slightest formatting change or even different comments result in a new plan for that "new" query, so it appears to be running fine when we test it manually in SSMS.

The complications of a Read-only secondary: When you have read-only replicas in a SQL Server Always On Availability Group, it's crucial to remember that these replicas can have stale execution plans, even though the statistics from the primary will all be up to date. Nothing on the replica has marked the stale plans in cache as invalid, leading to performance issues.

How can you solve this?

#### DBCC FREEPROCCACHE – The Temptation

Running this command on its own clears the entire plan cache, which is a drastic measure and should be used with great caution. Keep it far away from your production environment in normal circumstances. While it can potentially resolve performance issues caused by outdated execution plans in cache, it can also adversely affect other queries and will lead to mass plan recompilations, causing a significant spike in resource consumption. However, as demonstrated above, it can also be used surgically to target a particular query hash or plan hash if you are able to find it.

> Learn more: [DBCC FREEPROCCACHE](https://learn.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-freeproccache-transact-sql?view=sql-server-ver16)

#### ORM Queries – A Love-Hate Relationship... Maybe Without the Love

ORM frameworks can sometimes generate complex and convoluted queries that don't perform optimally. ("Sometimes" is used here as an olive branch to my developer friends, however, I would be prone to using much stronger language about ORMs normally.) As a result, you might observe the classic "Slow in the App Fast in SSMS" behavior, where queries run smoothly in SSMS during testing but falter miserably in the application due to differing execution plans. To unravel the mysteries behind why these different query plans can exist simultaneously, I highly recommend studying Erland Sommerskog's article which should be considered mandatory reading for any aspiring DBA or performance tuner.

> Learn more: [Query Plan Mysteries](https://sommarskog.se/query-plan-mysteries.html)

#### OPTION (RECOMPILE) – A Double-Edged Sword

This is a query hint that you can embed in stored procedures that will force a new query plan to be calculated for that query each time. This increases CPU cost but may be worth it for wildly varying parameter values in complex logic. This is not something that you can embed in a view definition, however, and should certainly not be slathered around liberally on every query.

####  OPTION ( OPTIMIZE FOR UNKNOWN ); – AKA Optimize for Mediocre

This is a query hint that tells your database engine that you expect high variance in your input parameters and you'd like the most generic plan possible. Understand that this means you are purposefully asking for what is NOT the most optimal query plan for the current sniffed parameter values, which means you are likely to get a plan that is sorta kinda OK for a wide variety of situations but good for none of them. Again, this is not something that should be applied in a wanton fashion to all queries.

#### Cache Invalidation – An Ancient Rite

Force a cache invalidation: you can create and then drop an index on a relevant table or run an sp_recompile against one of the relevant objects on the primary. Because every transaction log action is sent across to the secondary and then rewound from the transaction log into the data there, this WILL cause the plan cache invalidation for impacted objects that you would have EXPECTED to happen.

#### Conclusion

Pick your poison! All of these options have their pros and cons, and if you're seeing this come up it is likely at most a temporary bandaid to get people to stop screaming. Looking at the complexity in your stored proc or view and finding ways to rewrite that logic is almost certainly what your long term response needs to be. Remember that performance tuning is often an iterative process, and it's essential to monitor and analyze the system's behavior over time to make well-informed decisions.