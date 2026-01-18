
>📘 Chapter 5: Dependency Injection (The Glue – Deep Dive)

Dependency Injection (DI) is a design pattern where an object receives its dependencies from the outside instead of creating them itself.

In Android, we primarily use Hilt (built on Dagger) because it provides:

Compile-time safety
High performance (no reflection)
Lifecycle-aware dependency management

DI answers one core question:
Who creates objects, and who uses them?
Clean Architecture demands these two responsibilities be separated.

1️⃣ Why Dependency Injection Is Mandatory in Clean Architecture

Without DI, objects are created manually:

val repo = UserRepositoryImpl(LocalDataSource(), RemoteDataSource())
val useCase = GetUserUseCase(repo)

❌ Problems With Manual Object Creation
1. Hard to Test
You cannot easily inject fake or mock implementations
Production code becomes tightly coupled to concrete classes

2. Dependency Rule Violation

Domain layer becomes aware of how Data layer objects are created
This breaks Dependency Inversion Principle (DIP)
Inner layers should not know how outer layers are constructed.

3. Object Graph Explosion

As the app grows:
Repositories depend on multiple data sources
UseCases depend on multiple repositories
ViewModels depend on multiple UseCases
Manual wiring becomes unmaintainable and error-prone.

✅ DI Solves This By

Centralizing object creation
Managing dependency graphs
Injecting implementations at runtime
Enforcing architectural rules at compile time

3. Object Graph Explosion

As the app grows:
Repositories depend on multiple data sources
UseCases depend on multiple repositories
ViewModels depend on multiple UseCases
Manual wiring becomes unmaintainable and error-prone.

✅ DI Solves This By

Centralizing object creation
Managing dependency graphs
Injecting implementations at runtime
Enforcing architectural rules at compile time

2️⃣ Hilt Architecture (Internal Understanding)

Hilt = Dagger + Android lifecycle integration

Internally, Hilt:
Builds a dependency graph at compile time
Verifies that the graph is complete and valid
Generates optimized code for injection
If any dependency is missing → compile-time error, not runtime crash.
This is one of Hilt’s biggest advantages.

3️⃣ Constructor Injection (Golden Rule)
>✅ Always Prefer Constructor Injection
class GetUserUseCase @Inject constructor(
private val repository: UserRepository
)

>Why Senior Engineers Prefer This

Dependencies are immutable
No hidden or optional dependencies
Simple JVM unit testing
No framework dependency
If constructor injection is possible, no Hilt module is needed.

4️⃣ Hilt Modules – When Are They Required?

Modules are needed only in two situations.

>🔹 Case 1: Interface → Implementation Mapping

Domain layer contains interfaces, not implementations.

@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {

    @Binds
    @Singleton
    abstract fun bindUserRepository(
        impl: UserRepositoryImpl
    ): UserRepository
}


📌 Important Rule:

@Binds requires:
An abstract function
An abstract module

>🔹 Case 2: Third-Party or Framework Classes

Example: Retrofit, Room, OkHttp

>@Provides
fun provideRetrofit(): Retrofit


You don’t control their constructors — therefore, you must use @Provides.

5️⃣ @Binds vs @Provides (Deep Comparison)
Aspect	          @Binds	                      @Provides
Performance	      Faster                          Slightly slower
Boilerplate	      Minimal	                      More code
Usage	          Interface → Implementation	  Object creation
Function Body	  ❌ No                           ✅ Yes


>🎯 Golden Rule
If both are possible, always prefer @Binds.

6️⃣ Scoping – Most Critical Missing Knowledge 🔥
❓ What Is a Scope?

A scope defines how long an object lives and whether the same instance is reused.

`Common Hilt Scopes (Must-Know)
Scope	                   Lifetime
@Singleton	               Entire app lifetime
@ActivityRetainedScoped	   Survives configuration changes
@ViewModelScoped	       ViewModel lifetime
@ActivityScoped	           Activity lifetime
@FragmentScoped	           Fragment lifetime`



🚨 Critical Rule (Interview Favorite)

Never inject a shorter-lived object into a longer-lived one

❌ Example (Invalid):

`@Singleton
class Repo @Inject constructor(
val viewModel: MyViewModel // ❌
)`


Hilt catches this at compile time.

`7️⃣ Dependency Flow in Clean Architecture
Retrofit / Room
↓
RepositoryImpl   (Data)
↓
Repository       (Domain Interface)
↓
UseCase          (Domain)
↓
ViewModel        (Presentation)`


🚫 Reverse dependency flow is strictly forbidden.

8️⃣ Context Injection – The Safe Way
❌ Never Inject Context into ViewModel or Domain
`class MyViewModel @Inject constructor(
val context: Context // ❌
)`


✅ Correct Pattern
`@Provides
fun provideDatabase(
@ApplicationContext context: Context
): AppDatabase`


📌 Rules:

>>Context belongs only in Data layer or Hilt modules
Domain & ViewModel must remain pure Kotlin



9️⃣ MissingBindingException – Why It Happens
Scenario 1: New Interface Causes App Crash

Reason

Interface exists
Implementation exists
❌ No binding defined in Hilt module
Hilt cannot guess implementations.
Fix
Add a @Binds module


🔟 DI & Testing (Often Ignored but Critical)
Hilt Testing Strategy

Replace production dependencies with fakes.

`@TestInstallIn(
components = [SingletonComponent::class],
replaces = [RepositoryModule::class]
)`


This enables isolated, deterministic tests.
>🎙️ Interview Power Statements

“DI separates object creation from object usage”
“Hilt enforces Dependency Inversion at compile time”
“Scopes prevent memory leaks and lifecycle bugs”
“Constructor injection improves immutability and testability”

🎯 Final Key Takeaways (Chapter 5)

`DI is mandatory, not optional
Constructor injection > Field injection
@Binds is preferred over @Provides
Incorrect scoping causes crashes or leaks
Context never reaches Domain or ViewModel
Hilt acts as a safety net for Clean Architecture`
