# Table View - Fetched Results Controller

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

{% code title="ItemsViewController.swift" %}
```swift
import CoreData
import UIKit

class ItemsViewController: UITableViewController {

    static var maxCount = 0

    lazy var controller: NSFetchedResultsController<Item> = {
        let request: NSFetchRequest<Item> = Item.fetchRequest()
        request.sortDescriptors = [NSSortDescriptor(key: #keyPath(Item.count), ascending: false)]
        let controller = NSFetchedResultsController(fetchRequest: request, managedObjectContext: context, sectionNameKeyPath: nil, cacheName: nil)
        controller.delegate = self
        return controller
    }()

    override func viewDidLoad() {
        super.viewDidLoad()

        refreshControl = UIRefreshControl()
        refreshControl?.addTarget(self, action: #selector(refreshControlValueChanged(_:)), for: .valueChanged)

        navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addBarButtonTouched(_:)))
        navigationItem.rightBarButtonItem = editButtonItem

        //tableView.register(UITableViewCell.self, forCellReuseIdentifier: "ItemCell")


        do {
            try controller.performFetch()
        }
        catch let error as NSError {
            print("Fetching error: \(error), \(error.userInfo)")
        }
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
    }

    // MARK: - Actions

    @objc func refreshControlValueChanged(_ sender: UIRefreshControl) {
    }

    @objc func addBarButtonTouched(_ sender: UIBarButtonItem) {
        let item = Item(context: context)
        let id = UUID()
        let title = "Item \(id.uuidString.prefix(4))"
        item.id = id
        item.title = title
        item.group = "Group A"
        Self.maxCount += 1
        item.count = Int64(Self.maxCount)
        save()
    }
}

// MARK: - UITableViewDataSource
extension ItemsViewController {

    override func numberOfSections(in tableView: UITableView) -> Int {
        return controller.sections?.count ?? 0
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return controller.sections?[section].numberOfObjects ?? 0
    }

    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "ItemCell", for: indexPath)
        let item = controller.object(at: indexPath)
        cell.textLabel?.text = item.title
        cell.detailTextLabel?.text = "\(item.group ?? "-") Count \(item.count)"
        return cell
    }

    // Editing
    override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
        return true
    }

    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            let item = controller.object(at: indexPath)
            delete(item)
            save()
        }
        else if editingStyle == .insert {
        }
    }

    // Rarranging
    override func tableView(_ tableView: UITableView, canMoveRowAt indexPath: IndexPath) -> Bool {
        return true
    }

    override func tableView(_ tableView: UITableView, moveRowAt fromIndexPath: IndexPath, to: IndexPath) {
    }
}


// MARK: - UITableViewDelegate
extension ItemsViewController {

    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let item = controller.object(at: indexPath)
        item.count += 1
        save()
    }
}

// MARK: - NSFetchedResultsControllerDelegate
extension ItemsViewController: NSFetchedResultsControllerDelegate {

    func controllerWillChangeContent(_ controller: NSFetchedResultsController<NSFetchRequestResult>) {
        tableView.beginUpdates()
    }

    func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>,
                    didChange anObject: Any,
                    at indexPath: IndexPath?,
                    for type: NSFetchedResultsChangeType, newIndexPath: IndexPath?) {

    switch type {
    case .insert:
        tableView.insertRows(at: [newIndexPath!], with: .automatic)
    case .delete:
        tableView.deleteRows(at: [indexPath!], with: .automatic)
    case .update:
        tableView.reloadRows(at: [indexPath!], with: .automatic)
    case .move:
        tableView.deleteRows(at: [indexPath!], with: .automatic)
        tableView.insertRows(at: [newIndexPath!], with: .automatic)
    @unknown default:
        print("Unexpected NSFetchedResultsChangeType")
      }
    }

    func controllerDidChangeContent(_ controller: NSFetchedResultsController<NSFetchRequestResult>) {
        tableView.endUpdates()
    }

}

```
{% endcode %}

{% code title="Item+CoreDataProperties.swift" %}
```swift
import Foundation
import CoreData

extension Item {

    @nonobjc public class func fetchRequest() -> NSFetchRequest<Item> {
        return NSFetchRequest<Item>(entityName: "Item")
    }

    @NSManaged public var id: UUID?
    @NSManaged public var title: String?
    @NSManaged public var group: String?
    @NSManaged public var count: Int64
}
```
{% endcode %}



