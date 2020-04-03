# Fetch Request

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

{% code title="ItemsViewController.swift" %}
```swift
import CoreData
import UIKit

class ItemsViewController: UITableViewController {

    var items = [Item]()
    static var maxCount = 0

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

        // MARK: - CRUD - Fetch All

        // 1. Reload Data
        let req: NSFetchRequest<Item> = Item.fetchRequest()
        items = fetch(req)

        // 2. Reload UI
        tableView.reloadData()
    }

    // MARK: - Actions

    @objc func refreshControlValueChanged(_ sender: UIRefreshControl) {
        tableView.reloadData()
        tableView.refreshControl?.endRefreshing()
    }

    @objc func addBarButtonTouched(_ sender: UIBarButtonItem) {

        // MARK: - CRUD - Create

        // 1. Create Item
        let item = Item(context: context)
        Self.maxCount += 1
        item.title = "Item \(Self.maxCount)"
        save()

        // 2. Reload Data
        let req: NSFetchRequest<Item> = Item.fetchRequest()
        items = fetch(req)

        // 3. Reload UI
        tableView.reloadData()
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
        cell.textLabel?.text = item.title
        return cell
    }

    // Editing
    override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
        return true
    }

    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
        if editingStyle == .delete {

            // MARK: - CRUD - Delete

            // 1. Delete Item
            let item = items[indexPath.row]
            delete(item)

            // 2. Update Data
            items.remove(at: indexPath.row)

            // 3. Update UI
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

        //MARK: - CRUD - Update

        // 1. Update Item
        let item = items[indexPath.row]
        Self.maxCount += 1
        item.title = "Item \(Self.maxCount)"
        save()

        // 2. Reload UI
        tableView.reloadRows(at: [indexPath], with: .automatic)

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



