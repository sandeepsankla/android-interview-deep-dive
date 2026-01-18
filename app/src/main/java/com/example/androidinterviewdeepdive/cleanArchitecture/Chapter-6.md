
📘 Chapter 6: Data Mapping Strategy (The Transformation)
In Clean Architecture, we aim for Data Isolation. This means a backend (API) change must not break the UI. To achieve this, we introduce Mappers — controlled transformation points between layers.

"Mappers are the shock absorbers of your architecture."

1️⃣ The Four Types of Models (The "Why")
In a production-ready app, a single concept like User exists in multiple representations, each with a clear responsibility.
1. UserDto (Remote Model): Matches backend contract exactly. Uses @SerializedName. (Volatile)
2. UserEntity (Local Model): Represents database schema. Uses Room @Entity. (Volatile)
3. User (Domain Model): Represents business truth. Pure Kotlin. No framework annotations. (Stable)
4. UserUiModel (Presentation Model): Formatted specifically for what the UI needs. (Volatile)

2️⃣ Mapping Directions — The Golden Rule
Mapping always happens in the outer layer. Inner layers (Domain) must remain unaware of the outside world.
A. Data Layer Mappers (The Shield)
These protect the Domain from infrastructure changes.
1. DTO --> Domain: Convert API data into business-safe models.
2. DTO --> Entity: Prepare API data for persistence (saving to Room).
3. Entity --> Domain: Convert cached data into business models.

B. Presentation Layer Mappers (The Formatter)
These adapt business data for UI needs.
1. Domain --> UiModel: Formatting (dates, currency, text) and UI-specific flags.
2. Rule: Your Composable should never have to call .uppercase() or format a Date. 
3. The Mapper should provide a ready-to-display String.


3️⃣ Real-World Implementation: Extension Functions
Senior developers prefer Kotlin Extension Functions because they are compile-time safe, fast (no reflection), and highly readable.

Kotlin

// --- Data Layer Mappers ---

// API to Domain
`fun UserDto.toDomain(): User = User(
id = remoteId,
name = "$firstName $lastName",
email = email
)`

// Domain to DB Entity
`fun User.toEntity(): UserEntity = UserEntity(
dbId = id,
name = name
)`

// --- Presentation Layer Mapper ---

// Domain to UI Model
`fun User.toUiModel(): UserUiModel = UserUiModel(
displayName = name.uppercase(),
displayEmail = "Contact: $email",
badgeColor = if (isPremium) Color.Gold else Color.Gray
)`

🎙️ The "Cost of Change" Defense (Interview Gold)
Interviewer: "Why not use a single User model everywhere? It's less code."

Senior Answer:

"Duplication is cheaper than coupling. If I reuse the API model in the UI and tomorrow the backend renames 
user_name to login_handle, I’d have to refactor 50+ UI files. With Mappers, I change one line in the
Data Layer, and the rest of the app remains stable. It turns a 'breaking change' into a 'minor update'."

🧠 Check-Point Scenarios
`Scenario1: `
Offline App StartupUserEntity (Room) --> Mapper --> User (Domain) --> ViewModel --> UserUiModel --> UI.
Domain and UI never see the Room Entity.

`Scenario 2:` Using Domain Model Directly in ComposeAnswer: It's okay for very simple apps, but risky.
The moment the UI needs specific formatting or colors, a UserUiModel is mandatory to keep the Domain "pure."


🚫 Senior Red Flags (Common Mistakes)
Formatting dates inside a Composable.\
Passing UserDto into a ViewModel.
Putting Room annotations (@Entity) inside the Domain layer.
Making the Domain layer depend on a Mapper class.


🎯 Key Takeaways
Mappers = Insurance: They isolate changes.
Domain is Pure: It doesn't know about JSON or SQL.
Outer Layers are Responsible: Mapping happens at the entry/exit points of the layer.