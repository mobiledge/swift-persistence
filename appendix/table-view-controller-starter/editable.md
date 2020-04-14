# Editable

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

{% code title="ItemsViewController.swift" %}
```swift
import UIKit

class ItemsViewController: UITableViewController {

    var items = (1...20).map({"Item \($0)"})

    // MARK: - View Lifecycle
    override func viewDidLoad() {
        super.viewDidLoad()

        refreshControl = UIRefreshControl()
        refreshControl?.addTarget(self, action: #selector(refreshControlValueChanged(_:)), for: .valueChanged)

        navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addBarButtonTouched(_:)))
        navigationItem.rightBarButtonItem = editButtonItem
    }

    // MARK: - Actions
    @objc func refreshControlValueChanged(_ sender: UIRefreshControl) {
        tableView.reloadData()
        tableView.refreshControl?.endRefreshing()
    }

    @objc func addBarButtonTouched(_ sender: UIBarButtonItem) {
        items.append("Item \(items.count+1)")
        tableView.insertRows(at: [IndexPath(row: items.count-1, section: 0)], with: .automatic)
    }
}


// MARK: - UITableViewDataSource
extension ItemsViewController {

    // Row display
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        items.count
    }

    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "ItemCell", for: indexPath)
        let item = items[indexPath.row]
        cell.textLabel?.text = item
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
    }
}


// MARK: - UITableViewDelegate
extension ItemsViewController {
}

```
{% endcode %}



