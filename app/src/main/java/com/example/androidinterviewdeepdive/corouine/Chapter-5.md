
🌐 Chapter 5 — Scope, Context & Dispatchers
--


5.1 CoroutineScope
--

Defines coroutine lifetime.

Common scopes:
--
viewModelScope
lifecycleScope
applicationScope


5.2 CoroutineContext
--

Immutable set of elements:

Job
Dispatcher
CoroutineName
Dispatchers.IO + SupervisorJob()


| Dispatcher         | Use                |
| ------------------ | ------------------ |
| Main               | UI                 |
| IO                 | Blocking I/O       |
| Default            | CPU work           |
| limitedParallelism | Prevent starvation |
