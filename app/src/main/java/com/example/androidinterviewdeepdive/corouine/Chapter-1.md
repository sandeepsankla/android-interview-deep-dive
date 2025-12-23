🧩 Chapter 1 — Foundations of Coroutines
------


1.1 What are Coroutines?
------------------------

Coroutines are lightweight units of execution, not threads.

They do not own threads

They borrow threads only while executing

They release threads when suspended

Threads are scheduled by the OS
Coroutines are scheduled by the Kotlin runtime



1.2 Threads vs Coroutines
-------------------------------

Aspect	Thread	Coroutine
Creation cost	Heavy	Very light
Memory	High	Low
Blocking	Blocks OS thread	Suspends execution
Cancellation	Unsafe	Cooperative
Lifecycle	Manual	Structured




1.3 Main Thread vs Worker Threads
---------------------------------------

Main thread → UI, input, rendering (must be fast)

Worker threads → network, DB, CPU work

Coroutines allow thousands of tasks to share a few threads, preventing ANRs and memory issues.




1.4 What does suspend REALLY mean?
-----------------------------------
❌ Myth

suspend = background / async ❌

✅ Reality

suspend means:

This function can PAUSE execution without blocking the thread and RESUME later from the same point.

Key clarifications:

suspend does NOT create threads

suspend does NOT imply background execution

It only allows non-blocking suspension

Internals (high-level)

Function → state machine

Execution state → Continuation

Stack → unwound on suspension

Interview line

“The suspend keyword enables non-blocking suspension by compiling the function into a state machine.”




1.5 delay() vs Thread.sleep() (ADVANTAGES)
-------------------------------------------
Thread.sleep()
Thread.sleep(1000)


Blocks thread

Wastes dispatcher threads

Causes UI freeze / ANR

Not cancellable

delay()
delay(1000)

Advantages of delay()

Non-blocking – thread is released

Cancellation-aware

Fair scheduling – other coroutines can run

Lifecycle-safe

viewModelScope.launch {
delay(1000)
updateUI()
}


If ViewModel is destroyed → coroutine cancels automatically.




1.6 withContext {} — waiting for result (IMPORTANT)
------------------------------------------------------------
viewModelScope.launch {
println("Before")

    val result = withContext(Dispatchers.IO) {
        fetchFromNetwork()
    }

    println("Result = $result")
}

What happens step-by-step

Coroutine starts on Main

withContext(IO) suspends coroutine

Work runs on IO thread

Result produced

Coroutine resumes on Main

Next line executes

Important truths

withContext does NOT launch a new coroutine

It suspends and waits

It returns the result synchronously to the coroutine