# JSON

#### JSON Serializable Protocol

{% code title="JSONSerializable.swift" %}
```swift
import UIKit

protocol JSONSerializable: Codable {

    func toJSONString() -> String?
    static func fromJSONString(_ str: String) -> Self?
    static func arrayFromJSONString(_ str: String) -> [Self]?

    func serialized() -> [String: Any]?
    static func deserialize(from dict: [String: Any]) -> Self?
    static func deserializeArray(from arr: [[String: Any]]) -> [Self]?

    func encoded() -> Data?
    static func encode(array: [Self]) -> Data?
    static func decode(from data: Data) -> Self?
    static func decodeArray(from data: Data) -> [Self]?
}

extension JSONSerializable {

    // MARK: - String
    func toJSONString() -> String? {
        guard let data = encoded() else { return nil }
        return String(data: data, encoding: .utf8)
    }

    static func fromJSONString(_ str: String) -> Self? {
        let data = Data(str.utf8)
        return decode(from: data)
    }

    static func arrayFromJSONString(_ str: String) -> [Self]? {
        let data = Data(str.utf8)
        return decodeArray(from: data)
    }


    // MARK: - Dictionary
    func serialized() -> [String: Any]? {
        guard let data = encoded() else { return nil }
        return try? JSONSerialization.jsonObject(with: data, options: .allowFragments) as? [String: Any]
    }

    static func deserialize(from dict: [String: Any]) -> Self? {
        guard let data = try? JSONSerialization.data(withJSONObject: dict, options: .fragmentsAllowed) else { return nil }
        return decode(from: data)
    }

    static func deserializeArray(from arr: [[String: Any]]) -> [Self]? {
        guard let data = try? JSONSerialization.data(withJSONObject: arr, options: .fragmentsAllowed) else { return nil }
        return decodeArray(from: data)
    }


    // MARK: - Data
    func encoded() -> Data? {
        return try? JSONEncoder().encode(self)
    }

    static func encode(array: [Self]) -> Data? {
        return try? JSONEncoder().encode(array)
    }

    static func decode(from data: Data) -> Self? {
        return try? JSONDecoder().decode(Self.self, from: data)
    }

    static func decodeArray(from data: Data) -> [Self]? {
        return try? JSONDecoder().decode([Self].self, from: data)
    }
}

```
{% endcode %}

