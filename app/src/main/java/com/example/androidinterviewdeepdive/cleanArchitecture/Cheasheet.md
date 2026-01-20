

Here’s a **CLEAN, CRISP, LEAD-LEVEL CLEAN ARCHITECTURE CHEAT SHEET**
You can use this for **interviews, revision, slides, or Medium PDF**.

---

# 🧠 Clean Architecture – Cheat Sheet

*(Senior → Lead → Architect)*

---

## 🎯 Core Goal (Remember This First)

> **Clean Architecture is about controlling the cost of change by protecting business rules from details.**

Not folders.
Not frameworks.
Not patterns.

---

## 🧱 The Layers (Inside → Outside)

```
Entities (Business Truth)
UseCases (Business Flow)
Presentation (UI State)
Data (DB / API / SDKs)
```

### 🔑 Dependency Rule

> **Dependencies always point inward.**

* UI → Domain
* Data → Domain
* **Domain → Nothing**

---

## 🧠 Responsibility by Layer

### 🟡 Entity (Domain – Core)

**What it represents:**
✔ Business Truth (always true)

**Contains:**

* core business rules
* validations
* invariants

**Does NOT contain:**

* API calls
* DB logic
* workflows
* UI logic

**Example:**

```kotlin
fun isAdult(): Boolean = age >= 18
```

---

### 🟠 UseCase (Domain – Application Logic)

**What it represents:**
✔ Business Flow / Scenario

**Contains:**

* orchestration
* decision making
* order of operations

**Does NOT contain:**

* Android code
* UI logic
* formatting

**Example:**

```kotlin
registerUser()
applyDiscount()
checkoutOrder()
```

---

### 🔵 Presentation (UI + ViewModel)

**What it represents:**
✔ UI State & User Interaction

**Contains:**

* ViewModels
* UI state
* UI events

**Rules:**

* ViewModel ≠ Domain
* No Context, no R.string
* Stateless UI preferred

**Litmus Test:**

> If UI disappears, ViewModel makes no sense → Presentation layer

---

### 🟣 Data Layer (Implementation Detail)

**What it represents:**
✔ How data is fetched/stored

**Contains:**

* API calls
* DB (Room)
* SDKs (Firebase, Analytics)

**Implements:**

* Domain interfaces

---

## 🔁 Mappers (VERY IMPORTANT)

### Rule

> **Whenever data crosses a layer boundary, a mapper may be required.**

### Common Mappings

* DTO → Domain
* Entity → Domain
* Domain → Entity
* Domain → UI Model

### Golden Line

> **Duplication is cheaper than coupling.**

---

## 🧩 Business Truth vs Business Flow

| Concept        | Meaning           | Layer   |
| -------------- | ----------------- | ------- |
| Business Truth | Always true       | Entity  |
| Business Flow  | Context dependent | UseCase |

**Example**

* “GST is 18%” → Entity
* “Apply GST after discount” → UseCase

---

## 🔥 ViewModel Placement (Tricky Interview Point)

> Even if ViewModel is JVM-testable and Context-free…

### ✅ It still belongs to **Presentation Layer**

Why?

* Manages UI state
* Reacts to UI events
* Volatile by nature

> **Framework-independent ≠ Domain logic**

---

## 🔌 Cross-Cutting Concerns

### Correct Placement

* **Interface** → Domain
* **Implementation** → Data

**Examples:**

* Analytics
* Logging
* Auth
* Crash reporting

### Rule

> Cross-cutting concerns should **observe**, not **drive**, business logic.

---

## 🧪 Testing Strategy

| Layer            | Test Type   |
| ---------------- | ----------- |
| Entity / UseCase | Unit (JVM)  |
| ViewModel        | State tests |
| Data             | Integration |
| UI               | End-to-End  |

### Priority

> **Domain tests > UI tests**

---

## ⚠️ Common Anti-Patterns

❌ Business logic in ViewModel
❌ God Repository
❌ One model everywhere
❌ Entity calling Repository
❌ UseCase with no logic (unless intentional)

---

## 🧠 Pragmatism Rules (Lead-Level)

* Skip UseCase if no logic (document it)
* Bend Presentation/Data, never Domain
* Architecture should scale **teams**, not just code
* Undocumented shortcuts become permanent debt

---

## 🎯 Interview One-Liners (MEMORIZE)

* **“Entities define business truth; UseCases define business flow.”**
* **“Dependencies point inward to protect business rules.”**
* **“Duplication is cheaper than coupling.”**
* **“Architecture is about cost of change, not clean code.”**
* **“Frameworks are details.”**

---

## 🏁 Final Reminder

> **Seniors know the rules.
> Leads know when to bend them.
> Architects know what must never break.**

---

