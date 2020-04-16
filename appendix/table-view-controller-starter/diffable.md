# Diffable

## Basic

{% code title="StatesViewController.swift" %}
```swift
import UIKit

class StatesViewController: UITableViewController {

    lazy var datasource: UITableViewDiffableDataSource<Int, String> = {
        return UITableViewDiffableDataSource(tableView: self.tableView) { (tableView, indexPath, state) -> UITableViewCell? in
            let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
            cell.textLabel?.text = state
            return cell
        }
    }()

    // MARK: - View Lifecycle
    override func viewDidLoad() {
        super.viewDidLoad()

        let states = States.all
        var snap = NSDiffableDataSourceSnapshot<Int, String>()
        snap.appendSections([0])
        snap.appendItems(states)
        datasource.apply(snap, animatingDifferences: false)
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

#### Sections

{% code title="StatesViewController.swift" %}
```swift
import UIKit

class StatesViewController: UITableViewController {

    struct Section {
        var sectionName : String
        var rowData : [String]
    }
    var sections : [Section]!

    lazy var datasource: UITableViewDiffableDataSource<String, String> = {
        return UITableViewDiffableDataSource(tableView: self.tableView) { (tableView, indexPath, state) -> UITableViewCell? in
            let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
            cell.textLabel?.text = state
            return cell
        }
    }()

    // MARK: - View Lifecycle
    override func viewDidLoad() {
        super.viewDidLoad()

        // see: https://nshipster.com/uitableviewheaderfooterview/
        self.tableView.register(SectionHeaderView.nib, forHeaderFooterViewReuseIdentifier: SectionHeaderView.identifier)

        let states = States.all
        let d = Dictionary(grouping: states) {String($0.prefix(1))}
        self.sections = Array(d).sorted{$0.key < $1.key}.map {
            Section(sectionName: $0.key, rowData: $0.value)
        }
        
        var snap = NSDiffableDataSourceSnapshot<String, String>()
        for section in sections {
            snap.appendSections([section.sectionName])
            snap.appendItems(section.rowData)
        }
        self.datasource.apply(snap, animatingDifferences: false)
    }

    override func tableView(_ tableView: UITableView, viewForHeaderInSection section: Int) -> UIView? {
        let header = tableView.dequeueReusableHeaderFooterView(withIdentifier: SectionHeaderView.identifier) as? SectionHeaderView
        header?.titleLabel.text = self.datasource.snapshot().sectionIdentifiers[section]
        return header
    }
}


final class SectionHeaderView: UITableViewHeaderFooterView {

    @IBOutlet var titleLabel: UILabel!

    static let identifier: String = String(describing: self)

    static var nib: UINib {
        return UINib(nibName: String(describing: self), bundle: nil)
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



