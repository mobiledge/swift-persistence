# Predicate

#### Filtering by parent \`Category\`

```swift
    override func viewDidLoad() {
        super.viewDidLoad()

        guard let category = category else {
            return
        }
        let predicate = NSPredicate(format: "category = %@", category)
        fetchedResultsController.fetchRequest.predicate = predicate
        performFetch()
    }
```

