# JSON Helpers



{% code title="JSONHelpers.swift" %}
```swift
import UIKit
import Foundation
import XCTest


protocol JSONDictionaryConvertible: JSONDataConvertible {
    func toJSONDictionary() -> [String: Any]?
    static func fromJSONDictionary(_ dict: [String: Any]) -> Self?
}

extension JSONDictionaryConvertible {
    func toJSONDictionary() -> [String: Any]? {
        guard let data = toData() else { return nil }
        return try? JSONSerialization.jsonObject(with: data, options: .allowFragments) as? [String: Any]
    }

    static func fromJSONDictionary(_ dict: [String: Any]) -> Self? {
        guard let data = try? JSONSerialization.data(withJSONObject: dict, options: .fragmentsAllowed) else { return nil }
        return fromData(data)
    }
}

class JSONDictionaryConvertibleTests: XCTestCase {

    override func setUp() {
        super.setUp()
    }

    func testDataConversion() {

        let p1 = Person.any()
        let dict = p1.toJSONDictionary()!
        let p2 = Person.fromJSONDictionary(dict)

        XCTAssertEqual(p1, p2)
    }
}

protocol JSONDataConvertible: Codable {
    func toData() -> Data?
    static func fromData(_ data: Data) -> Self?
}

extension JSONDataConvertible {

    func toData() -> Data? {
        return try? JSONEncoder().encode(self)
    }

    static func fromData(_ data: Data) -> Self? {
        return try? JSONDecoder().decode(Self.self, from: data)
    }
}

class JSONDataConvertibleTests: XCTestCase {

    override func setUp() {
        super.setUp()
    }

    func testDataConversion() {

        let p1 = Person.any()
        let data = p1.toData()!
        let p2 = Person.fromData(data)

        XCTAssertEqual(p1, p2)
    }
}

struct Person: JSONDataConvertible, JSONDictionaryConvertible {
    let first: String
    let last: String
    let address: String

    static func any() -> Person {
        return Person(first: "Johnny", last: "Appleseed", address: "1230 Cameron Ave")
    }
}

extension Person: Equatable {

    static func == (lhs: Self, rhs: Self) -> Bool {
        return lhs.first == rhs.first && lhs.last == rhs.last && lhs.address == rhs.address
    }
}

JSONDataConvertibleTests.defaultTestSuite.run()
JSONDictionaryConvertibleTests.defaultTestSuite.run()

```
{% endcode %}

