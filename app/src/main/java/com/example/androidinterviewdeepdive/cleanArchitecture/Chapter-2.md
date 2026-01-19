>📘 Chapter 2: The Domain Layer — The Heart of Clean Architecture

The Domain layer is the most stable, central, and valuable part of an application.
It contains the business logic — the rules that define what the application does, independent of how it is presented or where data comes from.

If the Domain layer is weak or polluted, no architecture can save the app.

>1. Characteristics of the Domain Layer

# 1.1 Pure Kotlin / Java

The Domain layer must be framework-independent.
It must NOT depend on:
Android SDK (Context, Bundle, etc.)
Retrofit
Room
Coroutines dispatchers tied to Android
Dependency Injection frameworks
If you see import android.* in Domain code, it is a code smell.

# 1.2 Independent & Central

The Domain layer:
Sits at the center
Knows nothing about UI or database
Does not depend on any outer layer

Instead:

UI and Data layers depend on the Domain
This is what makes the Domain stable, while other layers remain replaceable.

>1.3 Highly Testable

Because the Domain layer is:

Pure Kotlin
Free from Android
Free from I/O concerns

It can be:

Unit tested in milliseconds
Tested on the JVM
Tested without emulators or instrumentation
Fast tests encourage better test coverage, which directly improves product quality.

>2. The Three Core Components of the Domain Layer

Think of these as the three pillars of the Domain.

>>A. Entities (Business Models)

Entities represent the core business objects of the application.

Examples:

Banking app → Account, Transaction

E-commerce app → Order, CartItem

Food delivery app → Restaurant, MenuItem

Important Rule

>>Domain Entities are:

Plain Old Kotlin Objects (POKOs)

Independent of database or network concerns

They are NOT:

Room @Entity classes
Retrofit response models
UI models
An Entity represents business meaning, not storage or transport format.

>>B. Use Cases (Interactors)

A Use Case represents one specific business action that the user can perform.

Examples:

AuthenticateUserUseCase
GetTransferHistoryUseCase
PlaceOrderUseCase

Why Use Cases Exist

Use Cases:

Enforce Single Responsibility Principle
Make business logic explicit
Act as a clear boundary between UI and Domain
If business rules for “Transferring Money” change, only one file should change.
This is what makes the architecture “scream” its intent.

Senior Insight (Important)

# Use Cases are not mandatory, but business boundaries are.

For trivial logic, repositories may suffice.
For complex or evolving logic, Use Cases become essential.

Skipping Use Cases does not justify moving business logic into ViewModel.

>>C. Repository Contracts (Interfaces)

This is the most misunderstood part of Clean Architecture.
The Domain layer defines what it needs, not how it is fulfilled.

`Example
interface UserRepository {
suspend fun getUserProfile(userId: String): User
}`


The Domain layer says:

“I need a User.”
It does not care whether that user comes from:

`Network
Local database
Cache
File system`

That decision belongs to the Data layer.

>>3. Dependency Inversion in Action (The HDMI Analogy)

In senior interviews, you must explain Dependency Inversion clearly.

How it works:

Domain layer defines the interface → (HDMI Port)
Data layer implements the interface → (HDMI Cable / Device)
Dependency direction points inward

>>Data → Domain
UI   → Domain


The Domain layer never points outward.

This inversion keeps the Domain pure and stable, even as implementations change.

`4. Real-World Kotlin Example
   Domain Entity
   data class User(
   val id: String,
   val name: String,
   val email: String
   )`

>Repository Contract
interface UserRepository {
suspend fun getUserProfile(userId: String): User
}

>>Use Case
class GetUserProfileUseCase(
private val repository: UserRepository
) {
suspend operator fun invoke(userId: String): Result<User> {
return if (userId.isBlank()) {
Result.failure(IllegalArgumentException("Invalid User ID"))  // instead of hardcored string it must be a sealed class
} else {
Result.success(repository.getUserProfile(userId))
}
}
}

Why this belongs in Domain

Validation is a business rule
Repository is abstracted
No Android dependency
Fully unit testable

>>🎙 Interview-Ready Answers (Chapter 2)
Q: Why do we use interfaces for repositories in the Domain layer?

Answer:

We use interfaces to apply the Dependency Inversion Principle.
By defining repository contracts in the Domain layer, business logic remains independent of implementation details like Retrofit or Room.
This enables easy replacement of data sources and allows unit testing using mocks without Android dependencies.

>>Q: Can a Use Case call another Use Case?

Answer:

Yes. While Use Cases should remain granular, complex business flows can be composed of multiple simpler Use Cases.
For example, a CheckoutUseCase may internally invoke ValidateCartUseCase and ProcessPaymentUseCase.

🧠 Check-Point Questions (Test Yourself)
Scenario 1

You are building a Weather App.
The API returns temperature in Kelvin.

Where should the conversion to Celsius happen?

Entity
Use Case
ViewModel

(Correct answer:
>"If Celsius is a core requirement of the business, I would handle the conversion in the Domain Layer (Entity) to
ensure consistency across all features. However, if the unit depends on user preference, I would keep the data 
raw in the Domain and perform the conversion in the Presentation Layer to keep the UI flexible."

Scenario 2

The UserRepository interface is defined in the Domain layer,
but UserRepositoryImpl lives in the Data layer.

How does the Domain layer receive the implementation?
(Correct answer: Dependency Injection at the application boundary.)

🎯 Key Takeaways

Domain is the “What”
It defines what the app does, not how it does it.

No Android imports allowed
Android dependencies in Domain are a design violation.

Granular logic wins
One Use Case = one business task.

Stable vs Volatile
Domain is stable.
UI and Data are volatile.