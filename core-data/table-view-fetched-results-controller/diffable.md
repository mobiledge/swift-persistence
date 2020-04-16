# Diffable

{% code title="GroupsTableViewController.swift" %}
```swift
import CoreData
import UIKit

class GroupsTableViewController: UITableViewController, NSFetchedResultsControllerDelegate {

    override func viewDidLoad() {
        super.viewDidLoad()

        navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addBarButtonTouched(_:)))
        navigationItem.rightBarButtonItem = editButtonItem

        performFetch()
    }

    // MARK: - Actions
    @objc func addBarButtonTouched(_ sender: UIBarButtonItem) {
        let group = Group(context: context)
        let id = UUID()
        let count = fetchedResultsController.fetchedObjects?.count ?? 0
        let title = "Group \(count + 1)"
        group.id = id
        group.title = title
        save()
    }

    // MARK: - Table View Diffable Data Source
    lazy var datasource : UITableViewDiffableDataSource<String, NSManagedObjectID> = {
        return UITableViewDiffableDataSource(tableView: self.tableView) { (tavleView, indexPath, _) -> UITableViewCell? in
            let cell = tavleView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
            let group = self.fetchedResultsController.object(at: indexPath)
            cell.textLabel?.text = group.title
            return cell
        }
    }()

    // MARK: - Fetched results controller
    lazy var fetchedResultsController: NSFetchedResultsController<Group> = {
        let fetchRequest: NSFetchRequest<Group> = Group.fetchRequest()
        fetchRequest.fetchBatchSize = 20
        let sortDescriptor = NSSortDescriptor(key: "title", ascending: false)
        fetchRequest.sortDescriptors = [sortDescriptor]
        let fetchedResultsController = NSFetchedResultsController(fetchRequest: fetchRequest, managedObjectContext: context, sectionNameKeyPath: nil, cacheName: "Master")
        fetchedResultsController.delegate = self
        return fetchedResultsController
    }()

    func performFetch() {
        do {
            try fetchedResultsController.performFetch()
        } catch {
             // Replace this implementation with code to handle the error appropriately.
             // fatalError() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
             let nserror = error as NSError
             fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
        }
    }

    func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>, didChangeContentWith snapshot: NSDiffableDataSourceSnapshotReference) {
        let oldsnapshot = datasource.snapshot()
        let snapshot = snapshot as NSDiffableDataSourceSnapshot<String, NSManagedObjectID>
        if oldsnapshot.itemIdentifiers == snapshot.itemIdentifiers {
            return
        }
        datasource.apply(snapshot, animatingDifferences: false)
    }
}
```
{% endcode %}



