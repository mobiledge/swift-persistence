# Storyboard

{% code title="StoryboardInstantiable.swift" %}
```swift
import UIKit

protocol StoryboardInstantiable {
    static func fromStoryboard() -> Self
}

extension StoryboardInstantiable where Self: UIViewController {

    static func fromStoryboard() -> Self {
        let sb = UIStoryboard(name: "Main", bundle: nil)
        let identifier = String(describing: self)
        guard let vc = sb.instantiateViewController(identifier: identifier) as? Self else {
            fatalError()
        }
        return vc
    }
}
```
{% endcode %}

