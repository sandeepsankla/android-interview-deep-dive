🚀 Chapter 2 — Coroutine Builders & Control
--------

2.1 launch
------------

launch { doWork() }


Fire-and-forget

Returns Job

Exception propagates immediately

Use cases:
----------

UI events
Logging
Background side effects



2.2 async
---------

val result = async { compute() }.await()
Returns Deferred<T>


Exception thrown on await()
⚠️ Common bug

async { apiCall() } // await never called


→ Exception silently lost

Rule
Use async only when a result is required.



2.3 join() vs await() (NEW – IMPORTANT)
----

join()
val job = launch { work() }
job.join()


Waits for completion
Does NOT return result
Used with launch

await()
val result = async { compute() }.await()


Waits for completion
Returns result
Throws exception if failed

Comparison
join	await
Used with Job	Used with Deferred
No result	Returns result
Just waits	Waits + propagates error

Interview line

“join waits for completion, await waits for completion and returns the result or throws the exception.”
--



2.4 coroutineScope vs withContext
--
coroutineScope	withContext
Creates scope	Switches context
Waits for children	Waits for block
Same dispatcher	Can change dispatcher

Both suspend the caller.



2.5 supervisorScope
--


supervisorScope {
    launch { api1() }
    launch { api2() }
}


Child failure does NOT cancel siblings
Used for partial success UI

