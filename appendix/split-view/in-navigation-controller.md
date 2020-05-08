# In Navigation Controller

{% code title="SceneDelegate.swift" %}
```swift
//
//  SceneDelegate.swift
//  TestSplitView
//
//  Created by Rabin Joshi on 2020-05-08.
//  Copyright © 2020 Rabin Joshi. All rights reserved.
//

import UIKit

class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        // Use this method to optionally configure and attach the UIWindow `window` to the provided UIWindowScene `scene`.
        // If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
        // This delegate does not imply the connecting scene or session are new (see `application:configurationForConnectingSceneSession` instead).
        guard let _ = (scene as? UIWindowScene) else { return }

        let svc = UISplitViewController()
        let master = MasterViewController(style:.plain)
        master.title = "Pep" // *
        let nav1 = UINavigationController(rootViewController:master) // *
        let detail = DetailViewController()
        let nav2 = UINavigationController(rootViewController:detail) // *
        svc.viewControllers = [nav1, nav2] // *
        self.window!.rootViewController = svc
        let b = svc.displayModeButtonItem // *
        detail.navigationItem.leftBarButtonItem = b // * detail.navigationItem.leftItemsSupplementBackButton = true // *
    }
}

class MasterViewController: UITableViewController {
    let model = ["Manny", "Moe", "Jack"]
    let cellID = "Cell"
    override func viewDidLoad() {
        super.viewDidLoad()
        self.tableView.register(UITableViewCell.self, forCellReuseIdentifier: self.cellID)
    }
    override func numberOfSections(in tableView: UITableView) -> Int {
        return 1
    }
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return model.count
    }
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: self.cellID, for: indexPath)
        cell.textLabel!.text = model[indexPath.row]
        return cell
    }
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let detail = DetailViewController()
        detail.boy = model[indexPath.row]
        let b = self.splitViewController?.displayModeButtonItem
        detail.navigationItem.leftBarButtonItem = b // *
        detail.navigationItem.leftItemsSupplementBackButton = true // *
        let nav = UINavigationController(rootViewController: detail) // *
        self.showDetailViewController(nav, sender: self)
    }
}

class DetailViewController: UIViewController {
    var lab : UILabel!
    var boy : String = "" {
        didSet {
            if self.lab != nil {
                self.lab.text = self.boy
            }
        }
    }
    override func viewDidLoad() {
        super.viewDidLoad()
        self.view.backgroundColor = .white
        let lab = UILabel()
        lab.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(lab)
        NSLayoutConstraint.activate([
            lab.topAnchor.constraint(equalTo: self.view.safeAreaLayoutGuide.topAnchor, constant: 100),
            lab.centerXAnchor.constraint( equalTo: self.view.centerXAnchor)
        ])
        self.lab = lab
        self.lab.text = self.boy
    }
}

```
{% endcode %}

