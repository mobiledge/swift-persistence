# JSONSerializable

* [x] Protocol Implementation
* [ ] Tests
* [ ] UI

{% code title="JSONSerializable.swift" %}
```swift
import UIKit

protocol JSONSerializable: Codable {

    func toJSONString() -> String?
    static func fromJSONString(_ str: String) -> Self?
    static func arrayFromJSONString(_ str: String) -> [Self]

    func toJSONDictionary() -> [String: Any]?
    static func fromJSONDictionary(_ dict: [String: Any]) -> Self?
    static func arrayFromJSONArray(_ arr: [[String: Any]]) -> [Self]

    func toJSONData() -> Data?
    static func fromJSONData(_ data: Data) -> Self?
    static func arrayFromJSONData(_ data: Data) -> [Self]
}

extension JSONSerializable {

    // MARK: - String
    func toJSONString() -> String? {
        guard let data = toJSONData() else { return nil }
        return String(data: data, encoding: .utf8)
    }

    static func fromJSONString(_ str: String) -> Self? {
        let data = Data(str.utf8)
        return fromJSONData(_: data)
    }

    static func arrayFromJSONString(_ str: String) -> [Self] {
        let data = Data(str.utf8)
        return arrayFromJSONData(_: data)
    }


    // MARK: - Dictionary
    func toJSONDictionary() -> [String: Any]? {
        guard let data = toJSONData() else { return nil }
        return try? JSONSerialization.jsonObject(with: data, options: .allowFragments) as? [String: Any]
    }

    static func fromJSONDictionary(_ dict: [String: Any]) -> Self? {
        guard let data = try? JSONSerialization.data(withJSONObject: dict, options: .fragmentsAllowed) else { return nil }
        return fromJSONData(_: data)
    }

    static func arrayFromJSONArray(_ arr: [[String: Any]]) -> [Self] {
        guard let data = try? JSONSerialization.data(withJSONObject: arr, options: .fragmentsAllowed) else { return [] }
        return arrayFromJSONData(_: data)
    }


    // MARK: - Data
    func toJSONData() -> Data? {
        return try? JSONEncoder().encode(self)
    }

    static func fromJSONData(_ data: Data) -> Self? {
        return try? JSONDecoder().decode(Self.self, from: data)
    }

    static func arrayFromJSONData(_ data: Data) -> [Self] {
        return (try? JSONDecoder().decode([Self].self, from: data)) ?? []
    }
}
```
{% endcode %}



