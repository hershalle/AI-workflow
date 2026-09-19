# iOS Development Style Guide

Apply alongside `development-guide.md` for iOS/Swift work only.

## Architecture & Principles

- Keep logic inside SwiftUI views (MVC-like). Introduce a ViewModel only when:
  - State is shared across multiple views, OR
  - Logic cannot reasonably stay inside the View
- When using MVVM, ViewModel must be `@Observable`. Never use `ObservableObject`.

## Framework Usage

- Use SwiftUI for all UI.
- Use QuickLayout only for UIKit interoperability, not as a primary abstraction.

## Resources & Localization

- Assets (images/colors): use Apple's native `Image(.foo)` / `Color(.foo)`. Never `R.image.*` / `R.color.*`.
- Strings: use a String Catalog (`Localizable.xcstrings`), never R.swift. The English text is the key.
  - SwiftUI call sites take plain literals: `Text("Cancel")`, `Button("Delete")`, `.navigationTitle("Journals")`, `.accessibilityLabel("Photo")`.
  - When a `String` is required, use `String(localized: "Delete \(count) photos?")`.
  - A ternary between two literals must be typed `LocalizedStringKey` (a computed property), otherwise the extractor misses one branch.
  - Never hand-add keys to the catalog. Xcode's compiler extraction (`SWIFT_EMIT_LOC_STRINGS`) populates it on build. Plural forms are set in the catalog editor with "Vary by Plural" on the `%lld` key; never hand-roll pluralization like `"photo\(count == 1 ? "" : "s")"`.
  - Text that must not be translated (user content, identifiers, debug labels) goes through `Text(verbatim:)` or a `String` variable so it never reaches the catalog.
- Everything else (files, fonts, storyboards, etc.): use R.swift (`R.file.*`, …) and force-unwrap the result. It is guaranteed to exist at build time.

## Navigation (Strict)

- Do NOT use `NavigationLink`.
- Always use programmatic navigation.
- Never simulate a navigation bar (a custom `HStack` with a centered title and side buttons). When a screen genuinely needs a title/toolbar bar, use a `NavigationStack`'s native bar - including inside sheets. Don't add a `NavigationStack` solely to get a bar a view doesn't need: a sheet that only confirms an action (no navigation, no toolbar) should be plain content with buttons.
- Put the `NavigationStack` where the navigation lives, not where the bar is drawn. A view that drives its own flow (pushes destinations) embeds `NavigationStack(path:)`. A single-screen view presented in a sheet stays pure content - the presenter wraps it in `NavigationStack` inside the `.sheet {}` closure. Either way the presented view declares its own `.navigationTitle`/`.toolbar`; they resolve against the enclosing stack.

### A sheet with a title and toolbar

Suppose `EditProfileView` is a single screen with a navigation title and a Save toolbar button. The presenter supplies its stack:

```swift
.sheet(isPresented: $isEditingProfile) {
    NavigationStack {
        EditProfileView()
    }
}
```

`EditProfileView` declares `.navigationTitle` and `.toolbar` on its content. It does not create another stack. This lets the same screen also be pushed inside an existing stack. A sheet with only a confirmation message and buttons does not need a stack.

For a flow that pushes several destinations, the view that owns the flow also owns `NavigationStack(path:)` and changes that path to navigate.

## Naming Conventions

- Use prefixes like `CA` only to extend system components or avoid naming conflicts.
- Use `Manager` as the default abstraction name unless a more precise name improves clarity.
- Booleans use `is/has/shows` prefixes.
- Static factories: `MyType.make()` style.
- Keep argument labels in function declarations so calls clearly describe each value. Avoid `_` unless following an established Swift convention, such as `init(_:)`. For example, prefer `search(query: String)` over `search(_ query: String)`.
- Prefer the parameter name as the external label over prepositional labels like `for`, `with`, `from`. Example: `analyticsNetworkName(nativeAd:)` - not `analyticsNetworkName(for nativeAd:)`. Use prepositional labels only when following an established Apple API pattern (e.g. `String(contentsOf:)`).
- Don't fold the argument's noun into the function name to justify dropping the label - move it into the label. Example: `isPhoneNumber(query:)` - not `isPhoneNumberQuery(_:)`.

## Code Style

- Xcode-generated file header comments (the default block at the top of new Swift files) are acceptable - leave them in place.
- Avoid `// MARK:` - structure code instead.
- When logging errors, log the error object directly - never use `localizedDescription`.
- Nested type definitions (enums, structs, etc.) go at the very top of the enclosing type, before properties and methods.

## State & Data Flow

- Avoid excessive use of `@Environment` - use only for dependencies that belong to the entire view tree (e.g. session).
- Use `@Entry` macro when extending `EnvironmentValues`, `Transaction`, `ContainerValues`, or `FocusedValues`.
- Maintain clear separation between DB models and UI models.
- Prefer value types for UI models.

## Concurrency

- Use `async/await` as the default concurrency model.
- Target Swift 6.2+ with default MainActor isolation, `@concurrent`, and `nonisolated`.
- To call MainActor-isolated state from a `@Sendable` closure (e.g. `Timer.scheduledTimer`, `DispatchQueue`, completion handlers), hop with `Task { @MainActor in … }`. Do NOT use `MainActor.assumeIsolated` - if the closure ever fires off the main thread the app crashes at runtime, and the compiler can't warn you because you explicitly opted out of the check. A future refactor can silently introduce the crash.

For example, a timer callback that updates a main-actor-owned countdown can hand that update to the main actor:

```swift
Timer.scheduledTimer(withTimeInterval: 1, repeats: true) { [weak self] _ in
    Task { @MainActor in
        self?.updateCountdown()
    }
}
```

The task asks the main actor to perform the update. `assumeIsolated` would only assert that the callback was already running there.

## Testing & Previews

- Follow preview-driven development.
- Every view must have at least one preview, preferably multiple states.
- Every type should have mock data.
- Use a centralized `MockGenerator`.

## Managers Pattern

- Prefer stateless managers when possible.
- For complex managers:
  - Top-level struct (never instantiated)
  - Nested lowercase structs as sub-namespaces
  - Static methods only
  - Extensions in separate files (`Type+Domain.swift`)
  - Nested enums for configuration
  - Sub-namespaces may cross-reference each other

Here, a namespace is a type used to group related operations. The static structure above produces call sites such as `NetworkManager.contacts.fetch(...)`: `NetworkManager` is the top-level group, `contacts` is a smaller group, and `fetch` is an operation. There is no manager instance at that call site. `Type+Domain.swift` names an extension file holding one group of related operations.

### Forwarding lifecycle events

For example, the app observes SwiftUI's `scenePhase` and forwards relevant changes to its managers:

```swift
.onChange(of: scenePhase) { _, newValue in
    switch newValue {
    case .active:
        SingularManager.shared.sceneIsActive()
    case .background:
        BackgroundTaskManager.sceneIsInBackground()
    case .inactive:
        break
    @unknown default:
        break
    }
}
```

The app reports “the scene is active” or “the scene is in the background.” Each manager decides what work to do in response. The app does not need to know how the manager handles that event.

The relationship gives the message its meaning. App-wide managers can receive relevant app lifecycle events. A receiver dedicated to one screen, such as `HomeViewModel`, can receive `didAppear()` because it already knows which screen it handles.

Forward events that belong to the receiver's responsibility. A general manager usually should not receive screen-specific messages such as `homeDidAppear()` just because Home can call it.

## AI Assistant Rules

- Never edit Storyboard (`.storyboard`) or XIB (`.xib`) files directly. These are generated, complex XML files. Instead, instruct the user step-by-step how to make the change in Interface Builder (which scene, which object, which inspector, which property).

## Access Control

- Implicit `internal` for view structs.
- `fileprivate` for file-scoped types.

## Error Handling (`try` vs `try?`)

- **Functions should throw** - let the caller decide how to handle the error.
- Use `try?` only when:
  - Decoding a union type (try one branch, fall back to another)
  - The fallback value is semantically correct and failure is truly uninteresting (e.g. mock/test code)
- Use `do/catch` with logging when:
  - The error is useful for debugging but the operation can continue with a default (e.g. metadata extraction with a fallback value)
  - Processing a loop where one failure shouldn't stop the rest
- Use `try` (and let it throw) when:
  - The caller needs to know about the failure
  - Failure means the whole operation should abort

## Remote Config

- Remote Config flag definitions are a contract. Never write code to protect against a mistake on the Remote Config side, and never flag one in review.

**Why:** Config is team-owned. Guarding against our own misconfiguration adds noise and hides a mistake that should be fixed in the console.
**How to apply:** Assume the key is there. Tolerant decoding is for server-owned data, not config.

## Switch Patterns

- Always place `let` immediately after `case`, not inside the associated value list.
  - Prefer: `case let .foo(x):`
  - Avoid: `case .foo(let x):`
- Prefer an exhaustive `switch` over `if case` / `guard case` when matching an enum: adding a case then fails to compile and forces an explicit decision at every site. Don't add `default` to silence it (framework enums still need `@unknown default`).

## Enum Patterns

- Handle unrecognized external values without losing the original value. Use the project’s existing approach, such as `SafeRawValue` or an `unknown(String)` enum case, and provide a supported fallback where needed.
- Only use `enum` when there are actual cases. Never use a caseless `enum` as a namespace or to prevent instantiation - use a `struct` with a `private init()` instead.

For example, a server response might contain a display style introduced after this app version shipped. The following is one possible implementation using `unknown(String)`; use `SafeRawValue` instead where that is the project’s existing approach:

```swift
enum DisplayStyle {
    case compact
    case expanded
    case unknown(String)

    var supportedOrDefault: DisplayStyle {
        switch self {
        case .compact, .expanded:
            self
        case .unknown:
            .compact
        }
    }
}
```

The app can display the compact layout while retaining the original server value for diagnostics. This illustrates server-data compatibility; the Remote Config contract above is separate.

## Localization Review

- Flag plain `String` values built from literals and shown to the user, hand-rolled pluralization, and literal ternaries typed as `String` as optional missing localization.
- A literal in `Text("…")`, `Button("…")`, `Label("…")`, or `.accessibilityLabel("…")` is localized by the String Catalog. Do not flag it.
