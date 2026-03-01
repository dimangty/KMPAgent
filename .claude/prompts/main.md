FIXED CLAUDE CODE PROMPT — KMP CUSTOM COMPONENTS ONLY

We will implement a Kotlin Multiplatform (Android + iOS) app from this
design: https://benchmarkee.ru/mobile/finance/ozon-bank

Reference template (READ ONLY):
/Users/dmitrijbykov/Documents/AndroidStudio/UnitUITest/TestingKMP

Constraints: - Implement ALL screens and navigation - Use mock network
layer (repository interfaces + fake implementation with delay) - UI
state uses classes describing screen UI components - Each screen in its
own file - Each UI component in its own file - Do NOT copy existing
template screens - Follow template architecture (navigation, state,
theming)

=========================================================== UI
ARCHITECTURE — STRICT (MUST FOLLOW)
===========================================================

This project uses Component-Driven UI Architecture.

Primitive Compose / UIKit / SwiftUI elements are FORBIDDEN in Screens.

ABSOLUTELY FORBIDDEN inside any Screen file:

Text Image Icon Spacer Button LazyColumn LazyRow Card
OutlinedTextField BasicText

SwiftUI primitives are ALSO FORBIDDEN in any Screen.swift file:

Text Image AsyncImage Label Spacer Divider Button Toggle Slider Stepper
ProgressView TextField SecureField TextEditor
NavigationStack TabView

AND any direct layout modifiers inside screens, e.g.:
.padding .frame .background .overlay .cornerRadius .font .foregroundStyle
.onTapGesture .toolbar .navigationTitle .navigationBarTitleDisplayMode


or any:

androidx.compose. platform.UIKit. SwiftUI.*

Screens are NOT ALLOWED to render layout or primitives.

=========================================================== Screens MUST
ONLY: ===========================================================

1.  Receive ScreenState
2.  Render ONLY:

ComponentRenderer(componentModel)

3.  Trigger events

=========================================================== ScreenState
HARD RULE ===========================================================

ScreenState MUST NOT contain:

String Int Float Boolean List Nullable primitive types

ScreenState MUST contain ONLY:

TextComponentModel ImageComponentModel ButtonComponentModel
CardComponentModel ListComponentModel InputComponentModel
HeaderComponentModel AvatarComponentModel BalanceComponentModel
TransactionItemComponentModel

=========================================================== EVERY UI
ELEMENT MUST BE:
===========================================================

Design element -> ComponentModel class ComponentModel -> Component
Composable Composable -> Located in /components Used in screen ONLY via
renderer

=========================================================== Required
structure ===========================================================

shared/ ├── ui/ │ ├── components/ │ │ ├── text/ │ │ │ TextComponent.kt │
│ │ TextComponentModel.kt │ │ ├── button/ │ │ │ ButtonComponent.kt │ │ │
ButtonComponentModel.kt │ │ ├── balance/ │ │ │ BalanceComponent.kt │ │ │
BalanceComponentModel.kt │ │ │ ├── renderer/ │ │ ComponentRenderer.kt

===========================================================
ComponentRenderer Pattern (MANDATORY)
===========================================================

Screens must render like:

@Composable fun HomeScreen(state: HomeScreenState) { Column {
state.components.forEach { ComponentRenderer(it) } } }

Primitive UI may exist ONLY inside:

shared/ui/components/**/*

=========================================================== Build
Violation Rule
===========================================================

If ANY Screen contains:

Text( Row( Column( Image( Button( LazyColumn(

You MUST:

1.  Extract new ComponentModel
2.  Extract new Component Composable
3.  Replace primitive with ComponentRenderer

===========================================================
IMPLEMENTATION REQUIREMENT
===========================================================

Before build phase:

-   Scan all *Screen.kt
-   Remove ALL primitive UI usage
-   Replace with ComponentRenderer
-   Create missing components if required

=========================================================== PROCESS
===========================================================

1)  Use the kmp-figma-builder subagent to:
    -   Inspect the reference template (read-only)
    -   Generate implementation specification:
        -   Screen inventory
        -   Navigation graph
        -   UI state classes
        -   Components per screen
        -   Mock network contract
        -   Exact file plan
2)  Then use the codex-kmp-implementer subagent to:

IMPLEMENTATION PHASE - Implement the generated specification - Create
all UI components (Use them in state. Dont use simple types in state) -
Create all required screens (IOS + Android) - Setup navigation - Setup
mock network layer - Remove old ios and android screens

AND THEN VALIDATION PHASE

Run Android build: ./gradlew :androidApp:assembleDebug

IF ANDROID BUILD FAILS: - Inspect build errors - Fix code or Gradle
configuration - Re-run Android build Repeat until Android build
succeeds.

Run iOS framework build: ./gradlew
:shared:linkDebugFrameworkIosSimulatorArm64

IF iOS BUILD FAILS: - Inspect build errors - Fix Kotlin Multiplatform
configuration or shared module code - Re-run iOS build Repeat until iOS
build succeeds.

WHEN BOTH BUILDS SUCCEED: Return unified diff patch only.

3)  Review the patch.

4)  Apply patch.

5)  After applying patch run final verification:

./gradlew :androidApp:assembleDebug ./gradlew
:shared:linkDebugFrameworkIosSimulatorArm64

Report final Android + iOS build status.
