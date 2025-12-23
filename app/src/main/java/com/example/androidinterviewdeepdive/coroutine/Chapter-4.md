
🔗 Chapter 4 — Composition & Concurrency (CLARIFIED)
--


4.1 Sequential Execution (DEFAULT)
--

`val a = api1()
val b = api2(a)`

Why sequential?

Single coroutine
No child coroutines
Each suspend waits for previous

Timeline:

api1 ----finish----> api2
-


Used when:

Calls depend on each other
Order matters

4.2 Concurrent Execution (EXPLICIT)
--


`coroutineScope {
val a = async { api1() }
val b = async { api2() }

    val r1 = a.await()
    val r2 = b.await()
}`


Timeline:
--

api1 --------\
-> await
api2 --------/

Why explicit?

Prevent accidental parallelism
Avoid resource explosion
Easier debugging