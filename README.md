import UIKit

class CarsTableVC: UITableViewController {
    
    let dataManager = DataManager.sharedDataManager
    var cars: [String: [Car]] = [:]
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Fetch cars from data manager
        cars = dataManager.fetchCars()
        
        // Configure table view
        tableView.rowHeight = 75.0
    }
    
    // MARK: - Table view data source
    
    override func numberOfSections(in tableView: UITableView) -> Int {
        return cars.count
    }
    
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        let sectionKey = Array(cars.keys)[section]
        return cars[sectionKey]?.count ?? 0
    }
    
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "CarCell", for: indexPath)
        
        let sectionKey = Array(cars.keys)[indexPath.section]
        if let car = cars[sectionKey]?[indexPath.row] {
            cell.textLabel?.text = "\(car.make) \(car.model)"
            cell.imageView?.image = UIImage(named: "car") // Assuming the image name is "car"
        }
        
        return cell
    }
    
    // MARK: - Table view delegate
    
    override func tableView(_ tableView: UITableView, trailingSwipeActionsConfigurationForRowAt indexPath: IndexPath) -> UISwipeActionsConfiguration? {
        let favoriteAction = UIContextualAction(style: .normal, title: "Favorite") { (_, _, completionHandler) in
            // Handle favoriting action
            completionHandler(true)
        }
        favoriteAction.backgroundColor = .green
        
        let unfavoriteAction = UIContextualAction(style: .destructive, title: "Unfavorite") { (_, _, completionHandler) in
            // Handle unfavoriting action
            completionHandler(true)
        }
        unfavoriteAction.backgroundColor = .systemBlue
        
        let swipeActions = UISwipeActionsConfiguration(actions: [favoriteAction, unfavoriteAction])
        return swipeActions
    }
    
    // MARK: - Navigation
    
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "showOrderSummary" {
            if let indexPath = tableView.indexPathForSelectedRow {
                let sectionKey = Array(cars.keys)[indexPath.section]
                if let car = cars[sectionKey]?[indexPath.row] {
                    let destinationVC = segue.destination as! OrderSummaryVC
                    destinationVC.selectedCar = car
                }
            }
        }
    }
    
    // MARK: - Bonus Questions
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        
        // Answering bonus questions
        print("1. Purpose of protocols in Swift: Protocols define a blueprint of methods, properties, and other requirements that suit a particular task or piece of functionality.")
        print("2. Data structure used by a Tab Bar Controller: Tab Bar Controller uses an array of view controllers.")
        print("3. Tab bar item in a view controller: Tab bar item is an object that encapsulates the tab bar-related properties of a view controller.")
    }
}

class OrderSummaryVC: UIViewController {
    
    @IBOutlet weak var orderSummaryTextView: UITextView!
    @IBOutlet weak var orderButton: UIButton!
    
    var selectedCar: Car?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Display order details
        if let car = selectedCar {
            let basePrice = car.price
            let salesTax = basePrice * 0.10
            let registrationFee = 850.00
            let totalPrice = basePrice + salesTax + registrationFee
            
            orderSummaryTextView.text = """
                Order Details:
                Car: \(car.make) \(car.model)
                Base Price: $\(basePrice)
                Sales Tax (10%): $\(salesTax)
                Registration Fee: $\(registrationFee)
                Total Price: $\(totalPrice)
                """
        }
    }
    
    @IBAction func orderButtonTapped(_ sender: UIButton) {
        let alertController = UIAlertController(title: "Confirm Order", message: "Are you sure you want to place this order?", preferredStyle: .alert)
        let confirmAction = UIAlertAction(title: "Confirm", style: .default, handler: nil)
        let cancelAction = UIAlertAction(title: "Cancel", style: .cancel, handler: nil)
        alertController.addAction(confirmAction)
        alertController.addAction(cancelAction)
        present(alertController, animated: true, completion: nil)
    }
}

