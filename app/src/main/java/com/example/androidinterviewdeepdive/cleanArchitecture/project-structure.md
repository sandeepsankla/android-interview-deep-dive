
#  📂 The Clean Architecture Blueprint (Folder Structure)

root/
├── app/                        # Dependency Injection Setup (Hilt)
├── core/                       # Shared logic, Common UI components, Theme
└── features/
└── feature_user/           # Example Feature: User Profile
├── domain/             # <--- THE HEART (Pure Kotlin)
│   ├── model/          # Entities (e.g., User.kt)
│   ├── repository/     # Repository Interfaces
│   └── use_case/       # Business Logic (e.g., GetUser.kt)
│
├── data/               # <--- THE INFRASTRUCTURE (Android)
│   ├── mapper/         # DTO/Entity to Domain Mappers
│   ├── repository/     # Repository Implementations
│   └── source/
│       ├── local/      # Room (Entities, DAOs, Database)
│       └── remote/     # Retrofit (API Interface, DTOs)
│
└── presentation/       # <--- THE FACE (UI)
├── components/     # Stateless Composables (Reusable UI)
├── state/          # UI State classes
└── viewmodel/      # ViewModel (Logic for UI)root/
├── app/                        # Dependency Injection Setup (Hilt)
├── core/                       # Shared logic, Common UI components, Theme
└── features/
└── feature_user/           # Example Feature: User Profile
├── domain/             # <--- THE HEART (Pure Kotlin)
│   ├── model/          # Entities (e.g., User.kt)
│   ├── repository/     # Repository Interfaces
│   └── use_case/       # Business Logic (e.g., GetUser.kt)
│
├── data/               # <--- THE INFRASTRUCTURE (Android)
│   ├── mapper/         # DTO/Entity to Domain Mappers
│   ├── repository/     # Repository Implementations
│   └── source/
│       ├── local/      # Room (Entities, DAOs, Database)
│       └── remote/     # Retrofit (API Interface, DTOs)
│
└── presentation/       # <--- THE FACE (UI)
├── components/     # Stateless Composables (Reusable UI)
├── state/          # UI State classes
└── viewmodel/      # ViewModel (Logic for UI)