# Diffable

{% code title="BoardsViewController.swift" %}
```swift
import CoreData
import UIKit

class BoardsViewController: UITableViewController {

    enum Section {
        case main
    }

    private lazy var controller: BoardsController = { BoardsController(delegate: self) }()
    let cellID = "Cell"

    override func viewDidLoad() {
        super.viewDidLoad()
        configureNavBar()
        configureTableView()
        controller.reloadData()
    }

    func configureNavBar() {
        navigationItem.title = "Boards"
        navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addBarButtonTouched(_:)))
    }

    func configureTableView() {
        self.tableView.register(UITableViewCell.self, forCellReuseIdentifier: self.cellID)
    }

    // MARK: - Actions
    @objc func addBarButtonTouched(_ sender: UIBarButtonItem) {
        presentCreateBoardAlert { (board) in
            if let board = board {
                print("Board created: \(board.title ?? "")")
            }
        }
    }


    // MARK: - Data Source
    lazy var datasource : UITableViewDiffableDataSource<Section, NSManagedObjectID> = {
        return UITableViewDiffableDataSource(tableView: self.tableView) { (tableView, indexPath, _) -> UITableViewCell? in
            let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
            let board = self.controller.fetchedResultsController.object(at: indexPath)
            cell.textLabel?.text = board.title
            return cell
        }
    }()
}


// MARK: - BoardCRUD
extension BoardsViewController: NSFetchedResultsControllerDelegate {

    func controller(_ controller: NSFetchedResultsController<NSFetchRequestResult>, didChangeContentWith snapshot: NSDiffableDataSourceSnapshotReference) {
        datasource.apply(snapshot as NSDiffableDataSourceSnapshot<BoardsViewController.Section, NSManagedObjectID>, animatingDifferences: true)
    }
}


// MARK: - BoardCRUD
extension BoardsViewController: BoardCRUD {}

```
{% endcode %}

{% code title="BoardsController.swift" %}
```swift
import CoreData
import UIKit

class BoardsController: NSObject {

    weak var delegate: NSFetchedResultsControllerDelegate?
    var boards: [Board] {
        return fetchedResultsController.fetchedObjects ?? [Board]()
    }

    init(delegate: NSFetchedResultsControllerDelegate) {
        self.delegate = delegate
    }

    lazy var fetchedResultsController: NSFetchedResultsController<Board>  = {

        // 1. Fetch Request
        let request: NSFetchRequest<Board> = Board.fetchRequest()

        // 2. Sort Descriptor
        let primary = NSSortDescriptor(key: "title", ascending: true)
        request.sortDescriptors = [primary]

        // 3. Context
        let context = CoreDataStack.shared.context

        // 4. Fetched Results Controller
        let fetchedResultsController = NSFetchedResultsController(fetchRequest: request, managedObjectContext: context, sectionNameKeyPath: nil, cacheName: nil)
        fetchedResultsController.delegate = self.delegate
        return fetchedResultsController
    }()


    // MARK: - Fetch
    func reloadData() {
        let predicate = NSPredicate(value: true)
        reloadData(predicate: predicate)
    }

    func reloadData(searchText: String) {
        let predicate = NSPredicate(format: "title BEGINSWITH %@", searchText)
        reloadData(predicate: predicate)
    }

    func reloadData(predicate: NSPredicate) {

        // 1. Update Predicate
        fetchedResultsController.fetchRequest.predicate = predicate

        // 2. Perform Fetch
        do {
            try fetchedResultsController.performFetch()
        } catch {
             // Replace this implementation with code to handle the error appropriately.
             // fatalError() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
             let nserror = error as NSError
             fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
        }
    }
```
{% endcode %}

{% code title="BoardCRUD.swift" %}
```swift
import UIKit

protocol BoardCRUD {
    func createBoard(name: String, completion: @escaping (Board?) -> Void)
}

extension BoardCRUD {

    func createBoard(name: String, completion: @escaping (Board?) -> Void) {
        let trimmed = name.trimmingCharacters(in: .whitespacesAndNewlines)
        guard trimmed.count > 0 else {
            completion(nil)
            return
        }
        let cd = CoreDataStack.shared
        let board = Board(context: cd.context)
        board.id = UUID()
        board.title = trimmed
        cd.save()
        completion(board)
    }
}

extension BoardCRUD where Self: UIViewController {

    func presentCreateBoardAlert(completion: @escaping (Board?) -> Void) {
        let alert = UIAlertController(title: "New Board", message: "What would you like to call this board?", preferredStyle: .alert)
        alert.addTextField { (textfield) in
            textfield.placeholder = "Board Name"
        }
        alert.addAction(UIAlertAction(title: "Create", style: .default, handler: { (_) in
            let name = alert.textFields?.first?.text ?? ""
            self.createBoard(name: name, completion: completion)
        }))
        alert.addAction(UIAlertAction(title: "Cancel", style: .cancel, handler: nil))
        present(alert, animated: true, completion: nil)
    }
}

```
{% endcode %}

