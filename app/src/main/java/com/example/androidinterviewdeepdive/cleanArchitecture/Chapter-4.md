📘 Chapter 4: The Presentation Layer (The UI & State)
The Presentation Layer is the "Face" of the application. Its job is to observe the Domain and 
render it for the user. It is highly volatile because UI frameworks 
(like the shift from XML to Compose) change the most.

1️⃣ Characteristics of the Presentation LayerFramework Dependent: 
Tied to androidx.lifecycle, Compose, Fragments, and ViewModels.

>Business Logic Free: It should never decide "Is this user an admin?"
It should only ask "What should I show for an admin?"State-Driven: 

In modern Android, the UI is a Function of State ($UI = f(State)$).

2️⃣ The Three Pillars of Presentation
>A. The ViewModel (The State Producer)The bridge between the UseCase and the UI.

Role: Executes UseCases and transforms the result into a single UiState.
Senior Rule: ViewModel should never import android.content.Context or R.string. 
This ensures it can be unit tested on the JVM.

>B. The UI Model (Domain $\to$ UI Mapping)
Sometimes the Domain User entity has a birthDate: Date, but the UI needs age: String.
Senior Practice: Don't do this formatting in the Composable. Create a UserUiModel that has 
the formatted data. This makes the UI "dumb" and easy to preview.

>C. The View (The State Consumer)Whether it's Compose or XML, the View should only  
listen and notify.
Listen: Observe the StateFlow from the ViewModel.
Notify: Send User Actions (clicks, swipes) to the ViewModel.

3️⃣ Senior Concept: One-Way Data Flow (UDF)This is the "Bread and Butter" of Senior Android Interviews.
Events (Up): UI sends an Event (e.g., onLoginClicked).Logic (Process): ViewModel handles the event,
calls UseCase.State (Down): ViewModel updates the UiState.Render (View): U
UI observes the new state and updates itself.

4️⃣ Error Handling & Localization (The "String" Trap)Junior Way: UseCase returns "Network Error". 
ViewModel shows it. (Hard to localize).Senior Way: UseCase returns a Sealed Class ErrorType.Network. 
ViewModel maps this to R.string.network_error.

>5️⃣ Code Example: The State MachineKotlin// 1. Define the State
data class UserState(
val isLoading: Boolean = false,
val user: UserUiModel? = null,
val errorMessage: Int? = null // Store String Resource ID, not raw String
)

// 2. The ViewModel
class UserViewModel(private val getUserUseCase: GetUserUseCase) : ViewModel() {

    private val _state = MutableStateFlow(UserState())
    val state = _state.asStateFlow()

    fun loadUser(id: String) {
        viewModelScope.launch {
            _state.update { it.copy(isLoading = true) }
            val result = getUserUseCase(id)
            // Handle Result and Update State
        }
    }
}
🎙️ Interview-Ready Q&A (Presentation Layer)
>❓Where should the formatting logic (e.g., Currency, Dates) live?

Answer: "Formatting logic is a Presentation concern. It should live in the 
ViewModel or a dedicated UI Mapper.
The UseCase should return raw data (like a Double or Long), 
and the Presentation layer decides how to display it based on the user's locale."

>❓How do you handle "One-time events" like Navigation or Snakbars in Compose?

Answer: "We use a separate Effect stream (like a Channel or SharedFlow). 
While State represents what is on the screen, Effects represent one-time actions that shouldn't be 
re-triggered on configuration changes.

"🧠 
Check-Point Answers (Test Yourself!)
🔹Scenario 1: Conversion logic (Kelvin to Celsius).Answer: If the app always uses Celsius, it's a Domain Rule (UseCase). 
If the user can toggle between F and C on the screen, it's a Presentation Logic (ViewModel/Mapper).

🔹 Scenario 2: Should Composable take ViewModel?
Answer: No. For better testing and Previews, the main Composable should be Stateless 
(take UiState and onEvent lambda). A "Screen" wrapper can handle the ViewModel.

🎯 Key TakeawaysViewModel manages State, not UI.
Mappers are for Formatting.Stateless UI is easier to test.