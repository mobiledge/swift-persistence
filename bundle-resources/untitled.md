# Bundle Resources

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

{% code title="BundleHelper.swift" %}
```swift
import Foundation

/**
 Loads a BundleLoadable type from bundle.
 File name must be same as the classname.
 File extension must be 'json'.
 */
protocol BundleLoadable: Codable {
    static func loadFromBundle() -> Self?
    static func loadArrayFromBundle() -> [Self]?
}

extension BundleLoadable {
    static func loadFromBundle() -> Self? {
        let resource = String(describing: self)
        let ext = "json"
        return BundleHelper.loadFromBundle(resource: resource, withExtension: ext)
    }
    static func loadArrayFromBundle() -> [Self]? {
        let resource = String(describing: self)
        let ext = "json"
        return BundleHelper.loadArrayFromBundle(resource: resource, withExtension: ext)
    }
}

/**
 Loads a Codable type from bundle.
 */
class BundleHelper {

    static func loadFromBundle<T: Codable>(resource: String, withExtension ext: String) -> T? {
        guard let data = loadDataFromBundle(resource: resource, withExtension: ext) else {
            return nil
        }
        do {
            let decoded = try JSONDecoder().decode(T.self, from: data)
            return decoded
        }
        catch {
            print(error)
            return nil
        }
    }

    static func loadArrayFromBundle<T: Codable>(resource: String, withExtension ext: String) -> [T]? {
        guard let data = loadDataFromBundle(resource: resource, withExtension: ext) else {
            return nil
        }
        do {
            let decoded = try JSONDecoder().decode([T].self, from: data)
            return decoded
        }
        catch {
            print(error)
            return nil
        }
    }

    static func loadDataFromBundle(resource: String, withExtension ext: String) -> Data? {
        guard let url = Bundle.main.url(forResource: resource, withExtension: ext) else {
            print("Couldn't find resource.")
            return nil
        }
        do {
            let data = try Data(contentsOf: url, options: .mappedIfSafe)
            return data
        }
        catch {
            print(error)
            return nil
        }
    }
}

```
{% endcode %}



