# Searching

## String Array

Using an array of  `String` values

> See: Programming iOS 13 \| Chapter 8 \| Table View Searching \| 517

{% code title="ItemsViewController.swift" %}
```swift
import UIKit

class StatesViewController: UITableViewController {

    var states = States.all

    // MARK: - View Lifecycle
    override func viewDidLoad() {
        super.viewDidLoad()

        // Search Controller
        let sc = UISearchController(searchResultsController: nil)
        sc.obscuresBackgroundDuringPresentation = false
        sc.searchResultsUpdater = self
        sc.delegate = self
        navigationItem.searchController = sc
    }
}


// MARK: - UITableViewDataSource
extension StatesViewController {

    // Row display
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        states.count
    }

    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
        let item = states[indexPath.row]
        cell.textLabel?.text = item
        return cell
    }
}


// MARK: - UITableViewDelegate
extension StatesViewController {
}


// MARK: - UISearchControllerDelegate
extension StatesViewController : UISearchControllerDelegate {
}


// MARK: - UISearchResultsUpdating
extension StatesViewController: UISearchResultsUpdating {

    func updateSearchResults(for searchController: UISearchController) {
        let searchText = searchController.searchBar.text ?? ""
        if searchText == "" {
            self.states = States.all
        }
        else {
            self.states = States.all.filter {
                if let _ = $0.range(of: searchText, options: .caseInsensitive) {
                    return true
                } else {
                    return false
                }
            }
        }
        self.tableView.reloadData()
    }
}

struct States {
    static var all = [
        "Alabama",
        "Alaska",
        "Arizona",
        "Arkansas",
        "California",
        "Colorado",
        "Connecticut",
        "Delaware",
        "Florida",
        "Georgia",
        "Hawaii",
        "Idaho",
        "Illinois",
        "Indiana",
        "Iowa",
        "Kansas",
        "Kentucky",
        "Louisiana",
        "Maine",
        "Maryland",
        "Massachusetts",
        "Michigan",
        "Minnesota",
        "Mississippi",
        "Missouri",
        "Montana",
        "Nebraska",
        "Nevada",
        "New Hampshire",
        "New Jersey",
        "New Mexico",
        "New York",
        "North Carolina",
        "North Dakota",
        "Ohio",
        "Oklahoma",
        "Oregon",
        "Pennsylvania",
        "Rhode Island",
        "South Carolina",
        "South Dakota",
        "Tennessee",
        "Texas",
        "Utah",
        "Vermont",
        "Virginia",
        "Washington",
        "West Virginia",
        "Wisconsin",
        "Wyoming"
    ]
}
```
{% endcode %}



