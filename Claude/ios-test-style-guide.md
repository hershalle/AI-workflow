# iOS Test Style Guide

Read and follow `test-guide.md` alongside this Swift-specific guidance. Resolve it from the same installed workflow directory as this file.

- Use `@Suite("Type.symbol")` to name the focus.

## A complete example

This example uses CallApp's contact model. It checks that removing the latest call makes the displayed activity date fall back to the last search.

```swift
@testable import CallApp
import Foundation
import Testing

@Suite("RemoteContactEntity.latestSearchOrCallDate")
struct LatestSearchOrCallDateTests {
    @Test("Falls back to the last search when the most recent call is removed")
    func fallsBackToLastSearchWhenMostRecentCallRemoved() {
        let yesterdaySearch = Date(timeIntervalSinceReferenceDate: 0)
        let todayCall = Date(timeIntervalSinceReferenceDate: 86_400)
        let contact = RemoteContactEntity(
            firstName: "Alex",
            primaryPhoneNumber: CAPhoneNumber(id: "+15551234567"),
            lastSearchDate: yesterdaySearch,
            callsToThisContact: [todayCall]
        )

        contact.removeCallsToThisContact(matching: [todayCall])

        #expect(contact.latestSearchOrCallDate == yesterdaySearch)
    }
}
```

The suite names the production property. The test struct names the behavior being tested, and the test label describes the scenario. Setup, action, and expected result are separated by blank lines so a reader can follow the test without extra comments.
