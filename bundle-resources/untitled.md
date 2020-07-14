# BundleResource \(1/3\)

* [x] Protocol Implementation
* [ ] Tests
* [ ] UI



{% code title="BundleResource.swift" %}
```swift
import Foundation

protocol BundleResource: JSONSerializable {
    static func fromBundle() -> Self?
    static func allFromBundle() -> [Self]
}

extension BundleResource {
    static func fromBundle() -> Self? {
        guard let data = dataFromBundle() else { return nil }
        return fromJSONData(data)
    }

    static func allFromBundle() -> [Self] {
        guard let data = dataFromBundle() else { return [] }
        return arrayFromJSONData(data)
    }

    // MARK: - Private
    private static var bundleURL: URL? {
        let name = String(describing: self)
        let ext = "json"
        return Bundle.main.url(forResource: name, withExtension: ext)
    }

    private static func dataFromBundle() -> Data? {
        guard let bundleURL = bundleURL else { return nil }
        return try? Data(contentsOf: bundleURL, options: .mappedIfSafe)
    }
}

```
{% endcode %}

