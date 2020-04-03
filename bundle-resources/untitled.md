# Untitled

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

{% code title="BundleHelper.swift" %}
```swift
/**
 Loads a Codable type from bundle.
 */
class BundleHelper {

    static func loadFromBundle<T: Codable>(resource: String, withExtension ext: String) -> T? {
        guard let url = Bundle.main.url(forResource: resource, withExtension: ext) else {
            return nil
        }
        do {
            let data = try Data(contentsOf: url, options: .mappedIfSafe)
            let arr = try JSONDecoder().decode(T.self, from: data)
            return arr
        }
        catch {
            print(error)
            return nil
        }
    }
}

```
{% endcode %}

{% code title="BundleLoadable.swift" %}
```swift
/**
 Loads a BundleLoadable type from bundle.
 File name nust be same as the classname.
 File extension must be 'json'.
 */
protocol BundleLoadable: Codable {
    static func loadFromBundle() -> Self?
}

extension BundleLoadable {
    static func loadFromBundle() -> Self? {
        let resource = String(describing: self)
        let ext = "json"
        return BundleHelper.loadFromBundle(resource: resource, withExtension: ext)
    }
}
```
{% endcode %}

{% hint style="info" %}
 Super-powers are granted randomly so please submit an issue if you're not happy with yours.
{% endhint %}

Once you're strong enough, save the world:

{% code title="hello.sh" %}
```bash
# Ain't no code for that yet, sorry
echo 'You got to trust me on this, I saved the world'
```
{% endcode %}



