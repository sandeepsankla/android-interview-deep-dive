>>📘 Chapter 3: The Data Layer (Infrastructure Layer)

The Data Layer is responsible for providing data to the Domain layer by implementing the contracts (interfaces) defined in the Domain.

It is the outermost layer in Clean Architecture and depends inward.
Any change in API, database, or framework should ideally impact only this layer.
--------------------------------------------------------------------------------------------

Characteristics of the Data Layer
✅ Framework Dependent

This layer contains all implementation details:
Retrofit / Ktor
Room / DataStore
Firebase
Apollo (GraphQL)
These are replaceable technologies, which is why they live here.

✅ Hidden from UI

UI and ViewModel never talk directly to APIs or databases
Flow:
>UI → ViewModel → UseCase → Repository → Data Sources
This keeps the UI clean, testable, and stable.

✅ Source of Truth Management

The Data layer decides:
Local cache vs Remote network
Fresh vs stale data
Sync and offline strategies

📌 Key Rule:

The Repository is the single source of truth — not the API and not the database.

>2️⃣ The Four Pillars of the Data Layer

A. Data Transfer Objects (DTOs)
DTOs represent external data formats, usually API responses.

`data class UserDto(
@SerializedName("id") val userId: String,
@SerializedName("full_name") val name: String
)`


Rules:

Must match API JSON exactly
Can change frequently
Must never leak outside the Data layer
If backend changes → only DTOs change

>B. Data Sources (The Suppliers)

Data Sources are responsible for only one thing:
fetching or storing data
They contain no business logic.
Types:
>RemoteDataSource → Network (Retrofit, Ktor)
LocalDataSource → Storage (Room, DataStore)

📌 Rule:
Data Sources are dumb. Repositories are smart.
C. Repository Implementation (The Brain)

The Repository:

Implements the interface defined in the Domain layer
Coordinates multiple data sources
Decides cache vs network
Handles errors

>Enforces offline-first logic

>class UserRepositoryImpl(
private val remote: RemoteUserDataSource,
private val local: LocalUserDataSource
) : UserRepository {

    override suspend fun getUser(id: String): User {
        val cached = local.getUser(id)
        if (cached != null) return cached.toDomain()

        val remoteUser = remote.fetchUser(id)
        local.saveUser(remoteUser.toEntity())
        return remoteUser.toDomain()
    }
}`


📌 Rule:
The Domain layer never knows where the data comes from.

>D. Mappers (The Translators)
Mappers convert Data models into Domain models.
DTO → Domain
Entity (Room) → Domain
DTO → Entity

fun UserDto.toDomain(): User =
User(id = userId, name = name)


📌 Golden Rule:

Domain models must never depend on API or database structures.

3️⃣ Offline-First Data Flow
Step-by-step flow:
UseCase calls repository.getUser()
Repository checks Local DB
If valid data exists → return it
Else → fetch from Remote
Save Remote data to Local DB
Map to Domain model
Emit to UseCase

>This approach enables:
Offline support
Faster UI
Predictable data behavior

4️⃣ Error Handling (Best Practice)

Data Sources may throw framework-specific errors (HTTP, IO)
Repository must catch and translate them
Domain and UI should receive domain-safe results

📌 Rule:

Framework exceptions must never leak beyond the Data layer.

🎙️ Interview-Ready Answers

❓ Why not call API directly from ViewModel?
Answer:
A Repository abstracts, data sources from business logic. It allows caching, offline support, testing, and future changes without impacting 
the ViewModel or UseCases.
`🧠 Yaad rakhne ka formula
ViewModel = KYA chahiye
Repository = KAHAN se milega`

❓ Where should mapping logic live?
Answer:
Mapping belongs to the Data layer. This keeps the Domain layer pure and independent of external data formats.

🧠 Check-Point Questions (With Correct Answers)
🔹 API returns 401 Unauthorized

DataSource throws the raw error

Repository catches it and converts it into a domain-friendly result

🔹 DTO, Entity, and Domain have same fields — can we reuse one class?

❌ No

Reason:

Duplication is cheaper than coupling.
APIs, databases, and business rules evolve independently.

🎯 Key Takeaways

Data Layer is volatile
Repository is the decision maker
Data Sources are dumb suppliers
Mappers are the shield
Domain stays pure and stable