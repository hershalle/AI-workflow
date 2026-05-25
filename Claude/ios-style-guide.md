---
name: Shai's iOS Development Style Guide
description: Comprehensive coding style, architecture, and decision rules for iOS/SwiftUI projects — includes naming, architecture, navigation, state, concurrency, and anti-patterns
type: feedback
---

## Architecture & Principles
- KISS and DRY above all else.
- Keep logic inside SwiftUI views (MVC-like). Introduce a ViewModel only when:
  - State is shared across multiple views, OR
  - Logic cannot reasonably stay inside the View
- When using MVVM, ViewModel must be `@Observable`. Never use `ObservableObject`.
- Avoid over-engineering — start simple and evolve only when needed.

**Why:** Simplicity is the primary goal. Layers must justify their existence.
**How to apply:** Before adding a ViewModel or abstraction, ask: "Does this truly need to be extracted?"

## Framework Usage
- Use SwiftUI for all UI.
- Use QuickLayout only for UIKit interoperability, not as a primary abstraction.
- Do not expose third-party frameworks directly — wrap behind facades.

**Why:** Avoid coupling to third-party APIs throughout the codebase.
**How to apply:** Always create a thin wrapper/facade over external libraries.

## R.swift Usage
- Assets (images/colors): use Apple's native `Image(.foo)` / `Color(.foo)`. Never `R.image.*` / `R.color.*`.
- Everything else (files, strings, fonts, storyboards, etc.): use R.swift (`R.file.*`, `R.string.*`, …).
- Force-unwrap R.swift results — they are guaranteed to exist at build time.

**Why:** Apple's generated asset symbols are first-class and integrate with SwiftUI previews and Xcode tooling. R.swift remains the right tool for non-asset resources where Apple has no equivalent. Force-unwrapping is safe because R.swift only generates symbols for resources that exist.
**How to apply:** Reach for `Image(.foo)` / `Color(.foo)` first. Fall back to `R.*` only for resource types Apple doesn't cover natively, and unwrap with `!`.

## Navigation (Strict)
- Do NOT use `NavigationLink`.
- Always use programmatic navigation.
- Never simulate a navigation bar (e.g. custom HStack with a title and buttons). Always use a `NavigationStack`'s native navigation bar, even inside bottom sheets.

**Why:** Programmatic navigation is more testable, predictable, and composable.
**How to apply:** Use navigation state (e.g., a path or route enum) and push/pop imperatively.

## Naming Conventions
- Clear and descriptive, but as short as possible — no abbreviations.
- Use prefixes like `CA` only to extend system components or avoid naming conflicts.
- Use `Manager` as the default abstraction name unless a more precise name improves clarity.
- Variables/properties: camelCase. Booleans use `is/has/shows` prefix.
- Types/Enums: PascalCase. Enum cases: camelCase.
- Static factories: `MyType.make()` style.
- Never omit external parameter labels (`_`) unless following an established Swift/Apple convention (e.g. `init(_ value:)`).
- Prefer the parameter name as the external label over prepositional labels like `for`, `with`, `from`. Example: `analyticsNetworkName(nativeAd:)` — not `analyticsNetworkName(for nativeAd:)`. Use prepositional labels only when following an established Apple API pattern (e.g. `String(contentsOf:)`).

**Why:** Consistent, readable naming following Apple conventions.
**How to apply:** Match these conventions for all new code.

## Code Style
- Code must be self-explanatory.
- Avoid comments unless absolutely necessary.
- Xcode-generated file header comments (the default block at the top of new Swift files) are acceptable — leave them in place.
- Avoid `// MARK:` — structure code instead.
- Prefer enums over raw strings for keys and configuration.
- Prefer composition over inheritance.
- Avoid unnecessary abstractions — every layer must justify its existence.
- When logging errors, log the error object directly — never use `localizedDescription`.
- Nested type definitions (enums, structs, etc.) go at the very top of the enclosing type, before properties and methods.

**Why:** Clean, readable code without scaffolding noise.
**How to apply:** If you feel the urge to write a comment or MARK, consider restructuring the code instead.

## State & Data Flow
- Avoid excessive use of `@Environment` — use only for dependencies that belong to the entire view tree (e.g. session).
- Use `@Entry` macro when extending `EnvironmentValues`, `Transaction`, `ContainerValues`, or `FocusedValues`.
- Maintain clear separation between DB models and UI models.
- Prefer value types for UI models.

## Concurrency
- Use `async/await` as the default concurrency model.
- Target Swift 6.2+ mindset (default MainActor isolation, `@concurrent`, `nonisolated`).
- Avoid legacy patterns unless strictly required.
- To call MainActor-isolated state from a `@Sendable` closure (e.g. `Timer.scheduledTimer`, `DispatchQueue`, completion handlers), hop with `Task { @MainActor in … }`. Do NOT use `MainActor.assumeIsolated` — if the closure ever fires off the main thread the app crashes at runtime, and the compiler can't warn you because you explicitly opted out of the check. A future refactor can silently introduce the crash.

**Why:** Static enforcement beats runtime assertions. A one-tick async hop is negligible; a production crash isn't.
**How to apply:** When the compiler complains "Main actor-isolated … can not be referenced from a Sendable closure", wrap the body in `Task { @MainActor in … }` rather than reaching for `assumeIsolated`.

## Testing & Previews
- Follow preview-driven development.
- Every view must have at least one preview, preferably multiple states.
- Every type should have mock data.
- Use a centralized `MockGenerator`.

## Managers Pattern
- `Manager` is the standard abstraction name.
- Prefer stateless managers when possible.
- For complex managers:
  - Top-level struct (never instantiated)
  - Nested lowercase structs as sub-namespaces
  - Static methods only
  - Extensions in separate files (`Type+Domain.swift`)
  - Nested enums for configuration
  - Sub-namespaces may cross-reference each other

## Decision Rules
- If unsure → choose the simplest solution (KISS).
- If adding abstraction → justify it explicitly.
- If using a third-party API → wrap it in a facade.

## AI Assistant Rules
- Never add AI signatures, attribution comments, or "generated by" notes in code.
- Never commit or stage files without explicit user request.
- Never run destructive git operations (reset --hard, force push, branch -D, clean, etc.) without explicit user approval.
- Never edit Storyboard (`.storyboard`) or XIB (`.xib`) files directly. These are generated, complex XML files. Instead, instruct the user step-by-step how to make the change in Interface Builder (which scene, which object, which inspector, which property).

## Anti-Patterns (Avoid)
- Premature abstractions
- Overuse of protocols
- Wrapper types without clear value
- Splitting files only for organization
- Introducing layers "just in case"
- `ObservableObject`
- `NavigationLink`
- `// MARK:` comments
- ViewModels without justification
- `error.localizedDescription` in logs
- Caseless `enum` used as a namespace or to prevent instantiation (use `struct` with `private init()` instead)

## Access Control
- `private` for implementation details.
- Implicit `internal` for view structs.
- `fileprivate` for file-scoped types.

## Error Handling (`try` vs `try?`)
- **Functions should throw** — let the caller decide how to handle the error.
- **Callers should catch and log** — not swallow silently.
- **Never show errors to the user unless the entire operation fails** — partial failures (e.g. one item in a loop) should only be logged.
- Use `try?` only when:
  - Decoding a union type (try one branch, fall back to another)
  - The fallback value is semantically correct and failure is truly uninteresting (e.g. mock/test code)
- Use `do/catch` with logging when:
  - The error is useful for debugging but the operation can continue with a default (e.g. metadata extraction with a fallback value)
  - Processing a loop where one failure shouldn't stop the rest
- Use `try` (and let it throw) when:
  - The caller needs to know about the failure
  - Failure means the whole operation should abort

## Guard vs If-Let
- **Guard**: early exit, validation, unwrapping needed for the rest of scope.
- **If-let**: optional binding used only within a limited scope.

## Switch Patterns
- Always place `let` immediately after `case`, not inside the associated value list.
  - Prefer: `case let .foo(x):`
  - Avoid: `case .foo(let x):`

## Enum Patterns
- Associated values for flexible data.
- Computed properties on enums (`var displayName`, `var color`).
- `unknown(String)` case for future compatibility with `supportedOrDefault` computed property.
- Only use `enum` when there are actual cases. Never use a caseless `enum` as a namespace or to prevent instantiation — use a `struct` with a `private init()` instead.