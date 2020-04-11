# Table View - Typed

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

{% code title="ItemsViewController.swift" %}
```swift
import UIKit

class ItemsViewController: UITableViewController {

    var items = [Item]()

    override func viewDidLoad() {
        super.viewDidLoad()

        refreshControl = UIRefreshControl()
        refreshControl?.addTarget(self, action: #selector(refreshControlValueChanged(_:)), for: .valueChanged)

        navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addBarButtonTouched(_:)))
        navigationItem.rightBarButtonItem = editButtonItem

        tableView.register(UITableViewCell.self, forCellReuseIdentifier: "ItemCell")
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        fetchAll()
    }

    // MARK: - Actions

    @objc func refreshControlValueChanged(_ sender: UIRefreshControl) {
        tableView.reloadData()
        tableView.refreshControl?.endRefreshing()
    }

    @objc func addBarButtonTouched(_ sender: UIBarButtonItem) {
        let item = Item(title: "Item \(items.count+1)")
        items.insert(item, at: 0)
        tableView.insertRows(at: [IndexPath(row: 0, section: 0)], with: .automatic)
    }

    // MARK: - CRUD
    func fetchAll() {
        let all = Item.all()
        let sorted = all.sorted(by: {$0.created > $1.created})
        items = sorted
    }
}

// MARK: - UITableViewDataSource
extension ItemsViewController {

    override func numberOfSections(in tableView: UITableView) -> Int {
        return 1
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return items.count
    }

    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "ItemCell", for: indexPath)
        let item = items[indexPath.row]
        cell.textLabel?.text = item.title + " - " + item.formattedCreated
        return cell
    }

    // Editing
    override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
        return true
    }

    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {
            items.remove(at: indexPath.row)
            tableView.deleteRows(at: [indexPath], with: .fade)
        }
        else if editingStyle == .insert {
            // Create a new instance of the appropriate class, insert it into the array, and add a new row to the table view
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
        tableView.deselectRow(at: indexPath, animated: true)
    }
}

struct Item {
    let id = UUID()
    let created = Date()
    let title: String

    static func all() -> [Item] {
        (1...20).map({Item(title: "Item \($0)")})
    }

    // Formatted
    static let formatter: DateFormatter = {
        let f = DateFormatter()
        f.dateStyle = .none
        f.timeStyle = .medium
        return f
    }()

    var formattedCreated: String {
        Self.formatter.string(from: created)
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



