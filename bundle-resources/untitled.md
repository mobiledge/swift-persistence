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

### Example

```swift
guard let states = State.loadArrayFromBundle() else {
        fatalError()
}
```

{% code title="State.swift" %}
```swift
import Foundation

struct State: BundleLoadable {
    let name: String
    let abbreviation: String
}
```
{% endcode %}

{% code title="State.json" %}
```bash
[
    {
        "name": "Alabama",
        "abbreviation": "AL"
    },
    {
        "name": "Alaska",
        "abbreviation": "AK"
    },
    {
        "name": "American Samoa",
        "abbreviation": "AS"
    },
    {
        "name": "Arizona",
        "abbreviation": "AZ"
    },
    {
        "name": "Arkansas",
        "abbreviation": "AR"
    },
    {
        "name": "California",
        "abbreviation": "CA"
    },
    {
        "name": "Colorado",
        "abbreviation": "CO"
    },
    {
        "name": "Connecticut",
        "abbreviation": "CT"
    },
    {
        "name": "Delaware",
        "abbreviation": "DE"
    },
    {
        "name": "District Of Columbia",
        "abbreviation": "DC"
    },
    {
        "name": "Federated States Of Micronesia",
        "abbreviation": "FM"
    },
    {
        "name": "Florida",
        "abbreviation": "FL"
    },
    {
        "name": "Georgia",
        "abbreviation": "GA"
    },
    {
        "name": "Guam",
        "abbreviation": "GU"
    },
    {
        "name": "Hawaii",
        "abbreviation": "HI"
    },
    {
        "name": "Idaho",
        "abbreviation": "ID"
    },
    {
        "name": "Illinois",
        "abbreviation": "IL"
    },
    {
        "name": "Indiana",
        "abbreviation": "IN"
    },
    {
        "name": "Iowa",
        "abbreviation": "IA"
    },
    {
        "name": "Kansas",
        "abbreviation": "KS"
    },
    {
        "name": "Kentucky",
        "abbreviation": "KY"
    },
    {
        "name": "Louisiana",
        "abbreviation": "LA"
    },
    {
        "name": "Maine",
        "abbreviation": "ME"
    },
    {
        "name": "Marshall Islands",
        "abbreviation": "MH"
    },
    {
        "name": "Maryland",
        "abbreviation": "MD"
    },
    {
        "name": "Massachusetts",
        "abbreviation": "MA"
    },
    {
        "name": "Michigan",
        "abbreviation": "MI"
    },
    {
        "name": "Minnesota",
        "abbreviation": "MN"
    },
    {
        "name": "Mississippi",
        "abbreviation": "MS"
    },
    {
        "name": "Missouri",
        "abbreviation": "MO"
    },
    {
        "name": "Montana",
        "abbreviation": "MT"
    },
    {
        "name": "Nebraska",
        "abbreviation": "NE"
    },
    {
        "name": "Nevada",
        "abbreviation": "NV"
    },
    {
        "name": "New Hampshire",
        "abbreviation": "NH"
    },
    {
        "name": "New Jersey",
        "abbreviation": "NJ"
    },
    {
        "name": "New Mexico",
        "abbreviation": "NM"
    },
    {
        "name": "New York",
        "abbreviation": "NY"
    },
    {
        "name": "North Carolina",
        "abbreviation": "NC"
    },
    {
        "name": "North Dakota",
        "abbreviation": "ND"
    },
    {
        "name": "Northern Mariana Islands",
        "abbreviation": "MP"
    },
    {
        "name": "Ohio",
        "abbreviation": "OH"
    },
    {
        "name": "Oklahoma",
        "abbreviation": "OK"
    },
    {
        "name": "Oregon",
        "abbreviation": "OR"
    },
    {
        "name": "Palau",
        "abbreviation": "PW"
    },
    {
        "name": "Pennsylvania",
        "abbreviation": "PA"
    },
    {
        "name": "Puerto Rico",
        "abbreviation": "PR"
    },
    {
        "name": "Rhode Island",
        "abbreviation": "RI"
    },
    {
        "name": "South Carolina",
        "abbreviation": "SC"
    },
    {
        "name": "South Dakota",
        "abbreviation": "SD"
    },
    {
        "name": "Tennessee",
        "abbreviation": "TN"
    },
    {
        "name": "Texas",
        "abbreviation": "TX"
    },
    {
        "name": "Utah",
        "abbreviation": "UT"
    },
    {
        "name": "Vermont",
        "abbreviation": "VT"
    },
    {
        "name": "Virgin Islands",
        "abbreviation": "VI"
    },
    {
        "name": "Virginia",
        "abbreviation": "VA"
    },
    {
        "name": "Washington",
        "abbreviation": "WA"
    },
    {
        "name": "West Virginia",
        "abbreviation": "WV"
    },
    {
        "name": "Wisconsin",
        "abbreviation": "WI"
    },
    {
        "name": "Wyoming",
        "abbreviation": "WY"
    }
]

```
{% endcode %}

