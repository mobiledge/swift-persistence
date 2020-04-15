# Sections

## Getting Super Powers

This code is based on the default XCode template for Master-Detail View Controller with Core Data & Storyboard selected

{% code title="ItemsViewController.swift" %}
```swift
import CoreData
import UIKit

class ItemsTableViewController: UITableViewController, NSFetchedResultsControllerDelegate {

    var group: Group {
        if let _group = _group {
            return _group
        }
        let group = Group(context: context)
        group.id = UUID()
        group.title = "Group 4"
        _group = group
        return group
    }
    var _group: Group?

    override func viewDidLoad() {
        super.viewDidLoad()

        refreshControl = UIRefreshControl()
        refreshControl?.addTarget(self, action: #selector(refreshControlValueChanged(_:)), for: .valueChanged)

        navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addBarButtonTouched(_:)))
        navigationItem.rightBarButtonItem = editButtonItem
    }

    // MARK: - Actions

    @objc func refreshControlValueChanged(_ sender: UIRefreshControl) {
    }

    @objc func addBarButtonTouched(_ sender: UIBarButtonItem) {
        let item = Item(context: context)
        let id = UUID()
        let count = fetchedResultsController.fetchedObjects?.count ?? 0
        let title = "Item \(count + 1)"
        item.id = id
        item.title = title
        item.group = group
        save()
    }

    // MARK: - Table View

    override func numberOfSections(in tableView: UITableView) -> Int {
        return fetchedResultsController.sections?.count ?? 0
    }

    override func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        let sectionInfo = fetchedResultsController.sections![section]
        return sectionInfo.name
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        let sectionInfo = fetchedResultsController.sections![section]
        return sectionInfo.numberOfObjects
    }

    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "ItemCell", for: indexPath)
        let item = fetchedResultsController.object(at: indexPath)
        configureCell(cell, withItem: item)
        return cell
    }

    override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
        // Return false if you do not want the specified item to be editable.
        return true
    }

    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            let context = fetchedResultsController.managedObjectContext
            context.delete(fetchedResultsController.object(at: indexPath))

            do {
                try context.save()
            } catch {
                // Replace this implementation with code to handle the error appropriately.
                // fatalError() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                let nserror = error as NSError
                fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
            }
        }
    }

    func configureCell(_ cell: UITableViewCell, withItem item: Item) {
        cell.textLabel!.text = item.title
    }

    // MARK: - Fetched results controller

    var fetchedResultsController: NSFetchedResultsController<Item> {
        if _fetchedResultsController != nil {
            return _fetchedResultsController!
        }

        let fetchRequest: NSFetchRequest<Item> = Item.fetchRequest()

        // Set the batch size to a suitable number.
        fetchRequest.fetchBatchSize = 20

        // Edit the sort key as appropriate.
        let sectionDescriptor = NSSortDescriptor(key: #keyPath(Item.group.title), ascending: false)
        let cellDescriptor = NSSortDescriptor(key: #keyPath(Item.title), ascending: false)
        fetchRequest.sortDescriptors = [sectionDescriptor, cellDescriptor]

        // Edit the section name key path and cache name if appropriate.
        // nil for section name key path means "no sections".
        let aFetchedResultsController = NSFetchedResultsController(fetchRequest: fetchRequest,
                                                                   managedObjectContext: context,
                                                                   sectionNameKeyPath: #keyPath(Item.group.title),
                                                                   cacheName: "Master")
        aFetchedResultsController.delegate = self
        _fetchedResultsController = aFetchedResultsController

        do {
            try _fetchedResultsController!.performFetch()
        } catch {
             // Replace this implementation with code to handle the error appropriately.
             // fatalError() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
             let nserror = error as NSError
             fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
        }

        return _fetchedResultsController!
    }
    var _fetchedResultsController: NSFetchedResultsController<Item>? = nil

    func controllerWillChangeContent(_ controller: NSFetchedResultsController<NSFetchRequestResult>) {
        tableView.beginUpdates()
    }

    func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>, didChange sectionInfo: NSFetchedResultsSectionInfo, atSectionIndex sectionIndex: Int, for type: NSFetchedResultsChangeType) {
        switch type {
            case .insert:
                tableView.insertSections(IndexSet(integer: sectionIndex), with: .fade)
            case .delete:
                tableView.deleteSections(IndexSet(integer: sectionIndex), with: .fade)
            default:
                return
        }
    }

    func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>, didChange anObject: Any, at indexPath: IndexPath?, for type: NSFetchedResultsChangeType, newIndexPath: IndexPath?) {
        switch type {
            case .insert:
                tableView.insertRows(at: [newIndexPath!], with: .fade)
            case .delete:
                tableView.deleteRows(at: [indexPath!], with: .fade)
            case .update:
                configureCell(tableView.cellForRow(at: indexPath!)!, withItem: anObject as! Item)
            case .move:
                configureCell(tableView.cellForRow(at: indexPath!)!, withItem: anObject as! Item)
                tableView.moveRow(at: indexPath!, to: newIndexPath!)
            default:
                return
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
    @NSManaged public var title: String?
    @NSManaged public var id: UUID?
    @NSManaged public var group: Group?
}
```
{% endcode %}

{% code title="Group+CoreDataProperties.swift" %}
```swift
import Foundation
import CoreData

extension Group {
    @nonobjc public class func fetchRequest() -> NSFetchRequest<Group> {
        return NSFetchRequest<Group>(entityName: "Group")
    }
    @NSManaged public var id: UUID?
    @NSManaged public var title: String?
    @NSManaged public var items: NSSet?
}

// MARK: Generated accessors for items
extension Group {

    @objc(addItemsObject:)
    @NSManaged public func addToItems(_ value: Item)

    @objc(removeItemsObject:)
    @NSManaged public func removeFromItems(_ value: Item)

    @objc(addItems:)
    @NSManaged public func addToItems(_ values: NSSet)

    @objc(removeItems:)
    @NSManaged public func removeFromItems(_ values: NSSet)
}
```
{% endcode %}

