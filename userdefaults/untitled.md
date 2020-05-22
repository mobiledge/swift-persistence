# User Defaults Persistable Protocol

{% code title="UserDefaultsPersistable.swift" %}
```swift
import UIKit

protocol UserDefaultsPersistable: JSONSerializable, Identifiable {

    static var errors: [Error] {get set}
    static var key: String {get}

    // Create
    static func create(from dict: [String: Any])

    // Read
    static func all() -> [Self]
    static func find(id: Self.ID) -> Self?

    // Update
    static func save(array: [Self])
    func save()

    // Delete
    static func destroyAll()

    // Misc
    static func count() -> Int
}

extension UserDefaultsPersistable {

    static var key: String {
        String(describing: self)
    }


    // MARK: - Create
    static func create(from dict: [String: Any]) {
        guard let obj = Self.deserialize(from: dict) else { return }
        obj.save()
    }

    // MARK: - Fetch
    static func all() -> [Self] {
        guard
            let data = fetchData(forKey: key),
            let arr = decodeArray(from: data) else {
            return [Self]()
        }
        return arr
    }

    static func find(id: Self.ID) -> Self? {
        return all().first(where: { $0.id == id })
    }


    // MARK: - Save
    static func save(array: [Self]) {
        guard let data = encode(array: array) else { return }
        saveData(data, forKey: key)
    }

    func save() {
        var arr = Self.all()
        arr.append(self)
        Self.save(array: arr)
    }


    // MARK: - Destroy
    static func destroyAll() {
        deleteData(forKey: key)
    }


    // Misc
    static func count() -> Int {
        return all().count
    }


    // MARK: - Get/Set data from UserDefaults
    static func saveData(_ data: Data, forKey key: String) {
        UserDefaults.standard.set(data, forKey: key)
    }

    static func fetchData(forKey key: String) -> Data? {
        guard let data = UserDefaults.standard.data(forKey: key) else {
            let error = UserDefaultsResourceError.failedToFindResource(key: key)
            errors.append(error)
            return nil
        }
        return data
    }

    static func deleteData(forKey key: String) {
        UserDefaults.standard.removeObject(forKey: key)
    }


}

enum UserDefaultsResourceError: Error {
    case failedToFindResource(key: String)
}

```
{% endcode %}

