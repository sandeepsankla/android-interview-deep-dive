
⛔ Chapter 3 — Cancellation & Exceptions (DEEP FIX)
--


3.1 Cancellation is Cooperative
--

Cancellation is a request, not a force.

`job.cancel()
`

Coroutine must cooperate to stop.

`3.2 isActive — CRITICAL CONCEPT
`

❗ Important truth
--
viewModelScope.cancel() does NOT forcibly stop code


If coroutine:
Has no suspend point
Has no isActive check

👉 It keeps running.


`❌ Bug
viewModelScope.launch(Dispatchers.Default) {
while (true) {
heavyCalculation()
}
}`

`✅ Correct
while (isActive) {
heavyCalculation()
}`


`or
ensureActive()
`


3.3 CancellationException — WHY rethrow is REQUIRED (NEW)
--

What is CancellationException?

A signal, not an error
Indicates coroutine was intentionally stopped

`Why rethrow is mandatory?
catch (e: CancellationException) {
throw e
}
`
If you DON’T rethrow:
-
Cancellation stops propagating
Parent coroutine thinks work completed
Coroutine becomes uncancellable
Leads to leaks, ANRs, zombie work


🧠 Analogy (VERY IMPORTANT)-
-

Think of fire alarm 🚨

Fire alarm rings → everyone must exit

If one person says:

“I’ll ignore this alarm”

Building safety breaks

CancellationException is that alarm.
Swallowing it means ignoring evacuation.

Golden Rule
--
CancellationException must always be rethrown.


3.4 Timeouts
---

`withTimeout(1000) { work() }
`
Throws TimeoutCancellationException
Subclass of CancellationException

Safer:
--

`withTimeoutOrNull(1000)
`